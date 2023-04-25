# 📌 [1] Django_Templates
## #️⃣ views.py와 templates 연결 
views.py에서 결과를 Templates/APP이름 폴더의 특정 html 파일과 연결지어 출력할 수 있다.


아래는 polls 앱의 views.py의 코드와 프론트를 구성하는 index.html파일을 작성한 예시이다.
```python
# polls/views.py
from django.http import HttpResponse
from django.shortcuts import render
from .models import *

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'first_question': latest_question_list[0]}
    return render(request, 'polls/index.html', context)
```

```python
# polls/templates/polls/index.html
<ul>
    <li> {{first_question}} </li>
</ul>
```

- `views.py`에서는 `render(request, 응답할 html 경로, html에 보여줄 데이터)`를 이용하면 html파일과 연결해줄 수 있다. 
- 그럼 html 파일에서는 중괄호 `{{}}`를 이용하여 받은 변수명을 그대로 사용하여 데이터를 출력 가능하다.

<br>

## #️⃣ Templates에서 제어문 사용
### ✅ if문 
- `{% %}`로 감싸서 작성하고 `endif`로 닫아준다. 
```html
{% if questions %}
<ul>
    Questions...
</ul>
{% else %}
<p>no questions</p>
{% endif %}
```
<br>

### ✅ for문 
- `{% %}`로 감싸서 작성하고, `endfor`로 닫아준다.
- `choice_set.all`의 경우 `()`없이 작성한다.
```html
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
```


<br>

## #️⃣ Templates에서 상세 페이지로 링크 연결 
### ✅ 1. 문자열로 직접 작성 
```html
 <li><a href="/polls/{{ question.id }}">{{question}}</a></li>
```
<br>

### ✅ 2. urls의 name  활용하기 
- `{% %}`로 url을 이용하여 `urls.py`에 name으로 지정해둔 detail를 따라 가고, 이때 인자로 `question.id`를 넘김 
```html
<li><a href="{% url 'detail' question.id %}">{{question}}</a></li>
```
<br>

### ✅ 3. urls의 name과 app_name 활용하기 
- `urls.py`의 `app_name`을 이용하여 detail 페이지를 나눌 수 있음
```python
# polls>urls.py
app_name = 'polls'
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>', views.detail, name='detail'),
    path('some_url', views.some_url),
]
```

```html
<li><a href="{% url 'polls.detail' question.id %}">{{question}}</a></li>
```


<br><br>

# 📌 [2] Django_404 Error 처리하기
## #️⃣ 방법 1 
- `try-except`로 `Http404` 에러 발생
```python 
from django.http import Http404

def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html',  {'question': question})
```
<br>

## #️⃣  방법 2
- Django에서 제공하는 `shortcuts`의 `get_object_or_404`으로 이용하기
```python 
from django.shortcuts import render, get_object_or_404

def detail(request, question_id):
    question =get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html',  {'question': question})
```

<br><br>

# 📌 [3] Django_Forms(폼)과 에러처리
## #️⃣  예시
> polls/detail.html에서 폼을 입력받아 choice에 +1 하는 로직  


### ✅ __urls.py__ 
- `vote`이름의 path를 추가한다.
```python
from django.urls import path
from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.index, name='index'), 
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/vote/', views.vote, name='vote'),  
]
```

<br>

### ✅ __view.py__ 
- 선택을 안했으면 `try-except`에서 `KeyError`로 에러를 잡아 `error_message`를 보낸다.
    - 추가적으로 `Choice.DoesNotExist`을 두어 중간에 choice가 사라지거나 value값이 변경되어 들어온 경우를 막는다. 
- 성공적으로 값을 받아 변경했다면, `HttpResponseRedirect`함수로 polls:index url로 넘긴다.
- `F('votes')`를 사용하여 DB에서 +1 작업을 하도록 한다
    - 만약 +1로 코드(메모리)상에서 작업하고 db에 넣으면 동시요청 시 응답이 반영이 안 될수도 있기 때문이다. 

```python
from django.http import HttpResponseRedirect
from django.shortcuts import render, get_object_or_404
from .models import *
from django.urls import reverse

def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice']) 
    except (KeyError, Choice.DoesNotExist):
        return render(request, 'polls/detail.html', {'question': question, 'error_message': f"선택이 없습니다. id={request.POST['choice']}"})
    else:
        selected_choice.votes = F('votes') + 1
        selected_choice.save()
        return HttpResponseRedirect(reverse('polls:result', args=(question.id,)))
```
<br>

### ✅ __detail.html__ 
- `form action`부분에는 폼결과를 보낼 url을 지정한다. 
- `{% csrf_token %}` : 서버에서 자동으로 CSRF 토큰을 발행하도록 함
- `{% if error_message %}`: 에러 메세지 있을 경우 출력함
- `{{ forloop.counter }}` : 반복문 돌때마다 1씩 증가하도록 하는 카운터
- 태그의 `name`값은 `view.py`에서 해당 태그 값을 가져올 때 사용하는 식별자가 됨

```html
<form action="{% url 'polls:vote' question.id %}" method="post">
    {% csrf_token %}
    <h1>{{ question.question_text }}</h1>
    {% if error_message %}
    <p><strong>{{ error_message }}</strong></p>
    {% endif %}
    
    {% for choice in question.choice_set.all %}
        <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
        <label for="choice{{ forloop.counter }}">
            {{ choice.choice_text }}
        </label>
        <br>
    {% endfor %}

<input type="submit" value="Vote">
</form>
```



