# 📌 [1] 기존 클래스 필드로 User 추가하기 
Django에서 기본으로 제공하는 User를 Question의 ForeignKey로 등록하는 예제이다.   
이렇게 등록하게 되면 user 한 명이 여러개의 Question을 가지는 구조가 된다.


__model.py__ 
```python
from django.db import models
from django.utils import timezone
import datetime
from django.contrib import admin


class Question(models.Model):
    question_text = models.CharField(max_length=200, verbose_name='질문')
    pub_date = models.DateTimeField(auto_now_add=True, verbose_name='생성일')  
    owner = models.ForeignKey('auth.User', related_name='questions', on_delete=models.CASCADE, null=True) 
    # owner 삭제시 Question도 삭제

    @admin.display(boolean=True, description='최근생성(하루기준)')
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
    
    # 출력시 기본 형태 지정
    def __str__(self):
        return f'제목: {self.question_text}, 날짜: {self.pub_date}'

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)

    def __str__(self):
        return f'[{self.question.question_text}] {self.choice_text}'
```

__Shell에서 조회__
``` shell
>>> from django.contrib.auth.models import User
>>> from polls.models import * 
>>> user = User.objects.first()
>>> user.questions.all() # 주목
>>> q1 = Question.objects.all()[0]
>>> q1.choice_set.all() # 주목
```
> 주목할 점 

- Question에 owner는 `ForeignKey`로 연결되어 있다. 즉 한 명의 owner는 여러개의 question을 갖는 형태가 된다.
    - 🌟`on_delete`옵션을 써서 owner 삭제 시 Question도 삭제되도록 설정하였다. 
    - 🌟 `related_name`옵션을 쓰면 shell에서 조회시 `_set`이 아닌 __해당 옵션에 설정한 값__ 으로 불러올 수 있다.   
- Choice 역시 question이 `ForeignKey`로 연결되어 있어, question 1개에 여러개의 Choice를 갖는 형태가 된다. 


<br><br>

# 📌 [2] User Serializer 생성
Django rest framework를 기반으로 하는 app 'polls_api'에 User Serializer를 생성하여 User를 관리한다.

__polls_api/serializers.py__
```python
from django.contrib.auth.models import User

class UserSerializer(serializers.ModelSerializer):
    questions = serializers.PrimaryKeyRelatedField(many=True, queryset=Question.objects.all())
    
    class Meta:
        model = User
        fields = ['id', 'username', 'questions']
```
- 🌟 `PrimaryKeyRelatedField`: user의 Primary Key를 통해서 여러 개의 question을 갖고 있다


__polls_api/views.py__
```python
from django.contrib.auth.models import User
from polls_api.serializers import UserSerializer

class UserList(generics.ListAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    
class UserDetail(generics.RetrieveAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

__polls_api/urls.py__
```python
from django.urls import path
from .views import *

urlpatterns = [
    path('question/', QuestionList.as_view(), name='question-list'),
    path('question/<int:pk>/', QuestionDetail.as_view()),
    path('users/', UserList.as_view(),name='user-list'),
    path('users/<int:pk>/', UserDetail.as_view()),

]
```
<br><br>

# 📌 [2] User 생성하기 (회원가입)
user를 회원가입하는 코드 예제이다.   
회원가입을 구현하는 방법은 Form을 이용한 방법과 Serializer를 이용한 방법 총 2가지가 있다. 

## #️⃣ 방법 1 : Form 이용하여 user 생성
__polls/views.py__
```python
from django.views import generic
from django.urls import reverse_lazy
from django.contrib.auth.forms import UserCreationForm

class SignupView(generic.CreateView):
    form_class = UserCreationForm
    success_url = reverse_lazy('user-list')
    template_name = 'registration/signup.html'
```
- 🌟 `generic` : 장고 프레임워크에서 제공하는 여러 유틸리티 클래스와  View클래스를 포함하는 라이브러리  
    - 'rest_framework'에서 제공하는 `generics`와 다름 (s 빠짐)  
    - __[ 자주 사용되는 클래스 ]__  
        - `ListView` : 데이터베이스에서 모델의 모든 인스턴스를 가져와 목록 형태로 표시함
        - `DetailView`: 단일 모델 인스턴스를 가져와 세부 정보를 표시함
        - `CreateView`: 새로운 모델 인스턴스를 만드는 데 사용됨
        - `UpdateView`: 기존 모델 인스턴스를 업데이트하는 데 사용됨
- 🌟`reverse_lazy` : 해당 함수를 사용하면 응답시, `urls.py`에 있는 name들을 파라미터로 하여 해당 url로 넘어가 응답한다.
- 🌟 `UserCreationForm` : 장고에서 제공하는 기본 폼으로 회원가입(id와 pw입력, pw 확인) 폼을 제공한다.

__signup.html__
```html
<h2>회원가입</h2>
 <form method="post">
   {% csrf_token %}
   {{ form.as_p }}
   <button type="submit">가입하기</button>
</form>
```
- views.py에서 `generic.CreateView`을 상속받아 UserCreationForm 폼을 생성했으니, html에서는 `form`으로 불러오면 된다.

__polls/urls.py__
```python 
from django.urls import path
from . import views
from .views import *

app_name = 'polls'
urlpatterns = [
    ...
    path('signup/', SignupView.as_view(), )
]
```
<br>

## #️⃣ 방법 2 : Serializer 이용하여 user 생성
__polls_api/serializers.py__ 
```python
class RegisterSerializer(serializers.ModelSerializer):
    password = serializers.CharField(write_only=True, required=True, validators=[validate_password])
    password2 = serializers.CharField(write_only=True, required=True)
    
    def validate(self, attrs):
        if attrs['password'] != attrs['password2']:
            raise serializers.ValidationError({"password": "두 패스워드가 일치하지 않습니다."})
        return attrs
    
    def create(self, validated_data):
        user = User.objects.create(username=validated_data['username'])
        user.set_password(validated_data['password'])
        user.save()
        
        return user
    
    class Meta:
        model = User
        fields = ['username', 'password','password2']
```
- 기존에 구현되어 있던 `UserSerializer`와 달리 validate와 create를 구현해야하기에 클래스를 따로 구분하여 `RegisterSerializer`를 만듦
- 🌟`serializers.ModelSerializer` : __Django 모델을 JSON, XML 등의 직렬화 가능한 형식으로 변환해주는 클래스__ 
    -  `validate()`와  `create()` 이름으로 하면 검증하고 생성하는 함수를 커스텀화하여 코드를 작성하되 그 기능이 동작되도록 해준다. 
        - `User 자체`에 오직 하나의 user만 생성되도록 검증해주는 로직이 구현되어 있음.   
        또한 `set_password`를 이용하여 validate()함수를 통과한 'password'를 해당 user의 pw로 저장한다.
        - `Meta`의 `model`에는 생성해줄 클래스/모델을 지정하고, `fields`에는 생성시 입력받을 필드를 지정한다.

__polls_api/views.py__
```python
from polls_api.serializers import RegisterSerializer

class RegisterUser(generics.CreateAPIView):
    serializer_class = RegisterSerializer
```
__polls_api/urls.py__
```python
from django.urls import path
from .views import *

urlpatterns = [
    ...
    path('register/', RegisterUser.as_view()),
]
```

<br><br>

# 📌[3] User 권한 관리 (로그인 기능)
로그인 기능을 만들어 로그인한 사용자가 만든 question이라는 것을 구현하는 예제이다.  
## #️⃣ 1. 로그인 버튼 만들기

__polls_api/urls.py__ 
```python
from django.urls import path,include
from .views import *

urlpatterns = [
    ...
    path('api-auth/', include('rest_framework.urls'))
]
```

__mysite/settings.py__
```python
from django.urls import reverse_lazy
LOGIN_REDIRECT_URL = reverse_lazy('question-list')
LOGOUT_REDIRECT_URL = reverse_lazy('question-list')
```
여기까지 파일을 수정하면 상단에 `로그인` 기능이 수행되는 버튼이 생성된다.
-  `LOGIN_REDIRECT_URL`를 `reverse_lazy`를 써서, 응답 후에 name이 'question-list'로 된 view.py의 `QuestionList` 함수로 넘어가게 한다.  

__polls_api/serializers.py__
```python
class QuestionSerializer(serializers.ModelSerializer):
    owner = serializers.ReadOnlyField(source='owner.username')

    class Meta:
        model = Question
        fields = ['id', 'question_text', 'pub_date', 'owner']
```
- `QuestionSerializer`의 fields에 `owner` 추가함
    - 해당 url로 접속시 아래 'Owner' 항목에 사용자 목록이 뜬다.
    - 이를 안 보이게 설정하기 위해 `owner`를 `ReadOnlyField`로 설정한다. 

__polls_api/permissions.py__
```python
from rest_framework import permissions

class IsOwnerOrReadOnly(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        if request.method in permissions.SAFE_METHODS:
            return True
        
        return obj.owner == request.user
```

__polls_api/views.py__
```python
from rest_framework import generics,permissions
from .permissions import IsOwnerOrReadOnly

class QuestionList(generics.ListCreateAPIView):
    queryset = Question.objects.all()
    serializer_class = QuestionSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly] # 퍼미션 지정 (로그인 상태에서만 만들 수 있음)
    
    def perform_create(self, serializer):
        serializer.save(owner=self.request.user) # [오버라이드] create시에 로그인한 사용자 지정

class QuestionDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Question.objects.all()
    serializer_class = QuestionSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly, IsOwnerOrReadOnly] # 로그인 시에만 되도록 permission 추가 
```
- `perform_create` : question create시 로그인 사용자를 owner로 지정하여 저장한다. 
    - 원래 `perform_create`함수는 `mixins.CreateModeMixin`에 구현된 함수 인데, 해당 클래스를 상속한 `generics.ListCreatAPIView`가 위에 코드에서 구현한 `QuestionList`의 부모라서 결국 함수가 __오버라이드__ 된거라 볼 수 있다. 
    - 그리고 원래 owner는 ReadOnly가 지정되어 있는데, `save()`시에는 이를 읽지 않고, __단순 주어진 값을 그대로 쓰기 때문에 owner로 저장가능하다.__ 
- `IsAuthenticatedOrReadOnly` : 로그인 사용자만 수정할 수 있는 기능이다.
    - 이는 __로그인 사용자가 해당 question의 소유자가 아니여도 수정할 수 있다.__   owner만 수정가능하도록 아래의 permissions.py를 만들어 관리한다.
- `IsAuthenticatedOrReadOnly` : permissions 파일에 구현된 함수로, owner만 수정/삭제할 수 있다.

