# 📌 [1] Serialize & Deserialsize 
## #️⃣ 개념
> model -- [Serialize] -> JSON -- [Deserialsize]  -> model

- `Serialize` : 모델 인스턴스나 QuerySet과 같은 데이터를 JSON 형식의 파일로 변환하는 작업 
- `Deserialsize` : Json 형식의 데이터를 정의된 포맷에 맞추어 다시 모델 인스턴스로 변환하는 작업

<br>

## #️⃣ 실습 예시 
### ✅ 0. 준비사항 
- djangorestframework 라이브러리 설치
```shell
pip install djangorestframework`
```

### ✅ 1. app 생성  
```shell
python manage.py startapp polls_api
```
<br>

### ✅ 2. 파일 생성 및 코드 작성 
#### __방법 1: Serializer 이용__ 
- 변수와 함수 모두 구현 필요
```python
from rest_framework import serializers
from polls.models import Question

class QuestionSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    question_text = serializers.CharField(max_length=200)
    pub_date = serializers.DateTimeField(read_only=True)

    def create(self, validated_data):
        return Question.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.question_text = validated_data.get('question_text', instance.question_text) + '[시리얼라이저에서 업데이트]'
        instance.save()
        return instance
```

#### __방법 2: ModelSerializer 이용__
- `model` : Serialize할 모델 지정
- `fields` : Serialize할 특정 필드들 지정
```python
class QuestionSerializer(serializers.ModelSerializer):
    class Meta:
        model = Question
        fields = ['id','question_text', 'pub_date']
``` 
<br>

### ✅ 3. Shell에서 Serializer 사용하기  
__Deserialize__
```python
> from polls.models import Question
> from polls_api.serializers import QuestionSerializer
> q = Question.objects.first()
> serializer = QuestionSerializer(q) # Deserialize
> from rest_framework.renderers import JSONRenderer # Json화
> json_str = JSONRenderer().render(serializer.data)
> json_str
```
- `JSONRenderer()`는 Serialize된 데이터를 JSON 형식으로 렌더링함

__Deserialize__
```python
> import json
> data = json.loads(json_str)
> serializer = QuestionSerializer(data=data)
> serializer.is_valid()  
True
>>> new_question = serializer.save() # Save는 Create()함수 호출함
```
- `json.loads()`는 JSON 형식의 데이터를 딕셔너리 형태로 Deserialize함
- `is_valid()` 로 먼저 유효한지 확인하고 save() 등의 행위를 해야한다. 

<br><br>


# 📌 [2] Django REST framework - GET/POST/PUT/DELETE 

views.py 파일에서 위에서 만든 Serializer를 가지고 json데이터를 return하는 코드를 작성할 수 있다.  
아래와 같이 파일을 작성하고 앱폴더와 메인 폴더의 `urls.py`에 해당 경로를 등록해주면 된다. 

<br>

## #️⃣ V1 : 기본 코드 
v1 버전의 코드 경우 `@api_view()`와 위에서 만든 `QuestionSerializer` 클래스를 이용하여 GET/POST/PUT/DELETE에 대한 코드를 모두 작성된 상태이다.

특히 `@api_view()`로 여러개를 등록해놓으면, 이를 `if`문 을 이용하여 하나씩 구분해서 코드를 작성해야 한다는 번거로움이 있다.  

__polls_api/views.py__ 
```python
from rest_framework.decorators import api_view
from polls.models import Question
from polls_api.serializers import QuestionSerializer
from rest_framework.response import Response
from rest_framework import status
from django.shortcuts import get_object_or_404

@api_view(['GET','POST'])
def question_list(request):
    if request.method == 'GET':
        questions = Question.objects.all()
        serializer = QuestionSerializer(questions, many = True)
        return Response(serializer.data)
    
    if request.method == 'POST':
        serializer = QuestionSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        else:
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


@api_view(['GET', 'PUT', 'DELETE'])
def question_detail(request, id):
    question = get_object_or_404(Question, pk=id)
    
    if request.method == 'GET':
        serializer = QuestionSerializer(question)
        return Response(serializer.data)

    if request.method == 'PUT':
        serializer = QuestionSerializer(question, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_200_OK)
        else:    
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    if request.method == 'DELETE':
        question.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```


__polls_api/urls.py__ 
```python
from django.urls import path
from .views import *

urlpatterns = [
    path('question/', question_list, name='question-list'),
    path('question/<int:id>/', question_detail, name='question-detail')
]
```
> ⚒ 특징 

✅` @api_view(http_method_names=['GET'])` 
- Django REST framework(DRF)에서 제공하는 __데코레이터 함수__ 로, __HTTP 요청을 처리하고 RESTful API__ 를 작성하는 데 사용됨
- ()가 비어있는 경우에 GET 요청을 처리함  

✅ `PUT`과 `DELETE`경우의 동작에 따라 return하는 Response 응답값을 다르게 보내도록 설정한다. 


<br><br>

## #️⃣ V2 : 클래스를 이용한 구현
v1의 코드는 함수로만 구현을 했다면, v2의 경우 `QuestionList`와 `QuestionDetail`를 클래스로 묶은 후, __`APIView`를 상속받도록 하여__ HTTP 메소드별로 함수로 구현한 것을 확인할 수 있다.     

__polls_api/views.py__ 
```python
# polls_api > views.py
from rest_framework.decorators import api_view
from polls.models import Question
from polls_api.serializers import QuestionSerializer
from rest_framework.response import Response
from rest_framework import status
from django.shortcuts import get_object_or_404
from rest_framework.views import APIView

class QuestionList(APIView):
    def get(self, request):
        questions = Question.objects.all()
        serializer = QuestionSerializer(questions, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = QuestionSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

class QuestionDetail(APIView):
    def get(self, request, id):
        question = get_object_or_404(Question, pk=id)
        serializer = QuestionSerializer(question)
        return Response(serializer.data)

    def put(self, request, id):
        question = get_object_or_404(Question, pk=id)
        serializer = QuestionSerializer(question, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_200_OK)
        else:    
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
        
    def delete(self, request, id):
        question = get_object_or_404(Question, pk=id)
        question.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

__polls_api/urls.py__ 
```python
from django.urls import path
from .views import *

urlpatterns = [
    path('question/', QuestionList.as_view(), name='question-list'),
    path('question/<int:id>/', QuestionDetail.as_view(), name='question-detail'),
]
```

> ⚒ 특징 

✅ `APIView` 를 이용함 🌟
- `APIView`는 Django REST framework에서 제공하는 클래스 중 하나로, 해당 클래스를 상속받아 새로운 API view 클래스를 작성할 수 있다. 
- `APIView`를 상속받은 클래스 안에 __request method에 맞는 함수들을 정의해주면__ 각각 요청은 알아서 구분되어 결과를 반환한다.  

✅ `urls.py`에서 `.as_view()`로 연결해줌 🌟
- `.as_view()` : 클래스 인스턴스를 생성하고, 해당 인스턴스의 `dispath()`를 호출하여 __어떤 HTTP Methods로__ 요청되었는지 알아내어, __인스턴스의 내의 해당 메소드의 이름을 갖는 함수로__ 요청을 연결해준다. 
- 그렇기에 위의 views.py의 클래스의 메소드를 보면 `get`, `post`들로 된 것을 확인할 수 있다. 


<br><br>

## #️⃣ V3 : mixins을 이용한 구현
v3의 코드는 `APIView`로 클래스화 했던 v2 코드와 달리, `mixins` 라이브러리를 이용하여 중복되는 코드를 없애고 간단히 한다. 

특히 코드를 보면 queryset과 serialize 클래스만 위에서 선언을 해주면, HTTP 메소드 함수들은 단 2줄만으로 기능 구현이 가능하다.

__polls_api/views.py__ 
```python
from polls.models import Question
from polls_api.serializers import QuestionSerializer
from rest_framework import mixins
from rest_framework import generics

class QuestionList(mixins.ListModelMixin,
                  mixins.CreateModelMixin,
                  generics.GenericAPIView):
    queryset = Question.objects.all()
    serializer_class = QuestionSerializer

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)

class QuestionDetail(mixins.RetrieveModelMixin,
                    mixins.UpdateModelMixin,
                    mixins.DestroyModelMixin,
                    generics.GenericAPIView):
    queryset = Question.objects.all()
    serializer_class = QuestionSerializer

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)

    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)

    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)
```
__polls_api/urls.py__ 
```python
from django.urls import path
from .views import *

urlpatterns = [
    path('question/', QuestionList.as_view(), name='question-list'),
    path('question/<int:pk>/', QuestionDetail.as_view(), name='question-detail'),
]
```

> ⚒ 특징 

✅ `mixins` 라이브러리를 이용함 🌟
- Django REST framework에서 mixins는 Django의 __APIView 클래스와 함께 사용하여 CRUD(Create, Read, Update, Delete) API를 빠르게 작성__ 하는 데 도움이 되는 클래스이다.
- __[ mixins에서 제공하는 클래스 ]__
   - `CreateModelMixin` : HTTP POST 요청을 처리하고 새로운 객체를 생성
   - `RetrieveModelMixin` : HTTP GET 요청을 처리하고 단일 객체를 조회
   - `UpdateModelMixin` : HTTP PUT 또는 PATCH 요청을 처리하고 기존 객체를 업데이트
   - `DestroyModelMixin` : HTTP DELETE 요청을 처리하고 기존 객체를 삭제
   - `ListModelMixin` : HTTP GET 요청을 처리하고 여러 객체를 리스트 형식으로 반환

✅ urls.py를 보면 경로 인자값으로 `id`가 아닌 `pk`값을 받는다.



<br><br>

## #️⃣ V4 : Generics
v4는 이미 RESTful API 기능이 구현된 `Generics` 클래스를 이용하여 코드가 매우 짧다.

__polls_api/views.py__ 
```python
from polls.models import Question
from polls_api.serializers import QuestionSerializer
from rest_framework import generics

class QuestionList(generics.ListCreateAPIView):
    queryset = Question.objects.all()
    serializer_class = QuestionSerializer

class QuestionDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Question.objects.all()
    serializer_class = QuestionSerializer
```
> ⚒ 특징 

✅ `Generics` 클래스를 이용함 🌟
- `Generics` : Django REST framework에서 매우 일반적인 RESTful API 패턴을 다루는 기능을 제공하는 클래스
- __[ Generics에서 제공하는 클래스 ]__
    - `ListCreateAPIView` : 객체 목록 조회 및 생성 API를 처리
    - `RetrieveUpdateDestroyAPIView` : 단일 객체 조회, 수정, 삭제 API를 처리
    - `ListAPIView` : 객체 목록 조회 API를 처리
    - `RetrieveAPIView` : 단일 객체 조회 API를 처리
- `Generics` 클래스를 이용하면 클래스내에 queryset과 serializer_class만 정의해주면 된다.