# 📌 Django Shell 사용하기 
파이썬 shell과 비슷하게 cli환경으로 한 줄씩 쳐서 코딩할 수 있는 공간이다. 

## #️⃣ 실행 방법
```python
python manage.py shell
```

## #️⃣ 실습 예제
```python
> from polls.models import *
> Question.objects.all() 
> Choice.objects.all()
> exit() # 끄기
> choice = Choice.objects.all()[0]
> choice.id
> choice.choice_text
> choice.votes
> choice.question.question_text
> choice.question.pub_date
> question = Question.objects.all()[0] # Question.objects.frist()와 같음
> question.question_text
> question.choice # question에는 choice가 연결되지 않아 오류 발생함
> question.choice_set.all() # 이렇게 해야 접근 가능함
```

<br><br>

# 📌 Django Shell - 현재 시간 구하기 
```python
> from datetime import datetime
> datatime.now() # 현재시간 
> from django.utils import timezone
> timezone.now()
```
- Django에서는 datatime이 아닌 `timezone`을 이용하여 시간을 구한다

<br><br>

# 📌 Django Shell - 레코드 생성하기
```python
> from polls.models import *
> q1 = Questtion(question_text="축구 vs 농구")
> q1.question_text
'축구 vs 농구'
> from django.utils import timezone 
> q1.pub_date = timezone.now()
> q1.save() # 모든 필수 필드값이 채워져 있어야 저장가능 
```
- 객체를 생성하여 `save()`를 호출하면 테이블의 레코드로 저장가능함 

```python
# 방법 1 
> q1.choice_set.all() # 널이어서 새로 만들고 싶으면 아래 명령어 입력
> q1.choice_set.create(choice_text='a') # 생성 가능 

# 방법2 : 혹은 아래 방법도 동일하다
> choice_c = Choice(choice_text='c', question=q1)
> choice_c.save()
```
- 부모에 연결된 자식을 만들때는 위의 2가지 방법으로 생성 가능함
    - `q1.choice_set.create()`
    - `choice_c.save()`


<br><br>

# 📌 Django Shell - 레코드 수정 및 삭제 하기
## #️⃣ 수정하기 
```python
> from polls.models import *
> q = Question.objects.last()
> q1.question_text
'abc'
> q1.question_text = q1.question_text + '...'
> q1.question_text
'abc...'
> q1.save() 
```
- 단순하게 객체를 불러와서 값 변경후 `save()`하면 됨

<br>

## #️⃣ 삭제하기 
```python
> from polls.models import *
> choice = Choice.object.last()
> choice.delete() # 삭제되고
> choice.save() #  다시 생김
> q1 = Question.objects.last()
> q1.choice_set.delete() # 연결된 모든 choice 삭제
```
- 단순하게 객체를 불러와서 `delete()` 하면 삭제됨

<br><br>

# 📌 Django Shell - 모델 필터링(Model Filtering)
## #️⃣ 기본 
```python
> from polls.models import *
> Question.objects.get(id=1) # 해당하는 Question 객체 리턴
> Question.objects.get(question_text__startswith='휴가를 ') # 해당하는  Question 객체 리턴
> Question.objects.get(pub_date__second==59) # 해당하는 값 한개 반환 
> Question.objects.filter(pub_date__year==2023) # 해당하는 값 여러개 QuerySet으로 반환 
> Question.objects.filter(pub_date__year==2023).count() # 개수 반환
> Question.objects.filter(pub_date__year==2023).query # 쿼리로 출력해줌
```
- `filter`, `q1.choice_set.all()`과 값이 여러개일 때()는 반환 값은 `QuerySet`타입이다
    - `get()`처럼 하나만 가져올때는 해당 객체 타입임

<br>

## #️⃣ 조건  
filter(), exclude()나 get()에서 사용할 수 있는 조건 알아보기   
- `__starwith` : 시작 문자열
- `__year` : 년도 
- `__contains` : 특정 문자열이 있는지 
- `__gt`: 해당 값보다 큰 값을 가져옴
- `__regex` : 정규표현식 만족하는 값만
- 그 외 (QuerySet API 문서 참고)

__예시__  
```python
> Choice.objects.filter(votes__gt=0).update(votes=0) # 0보다 큰 값들을 0으로 업데이트 함
> q=Question(question_text="휴가 계획이 있나요")
> q.save()
> Question.objects.filter(question_text__regex=r'^휴가.*어디') # 정규표현식 이용
> Question.objects.filter(question_text__startswith='휴가').filter(question_text__contains='어디') # filter 이어서 가능
Question.objects.exclude(question_text__startswith='휴가') # exclude 함수
```

<br><br>

# 📌 Django Shell - 모델 메소드
사용자 모델 함수를 지정하면 shell에서 출력할 때 다르게 표현할 수 있다.  
## #️⃣ 예시  
```python
# polls>models.py
class Question(models.Model):
    question = models.CharField(max_length=200)
    pub_date = models.DateTimeField(auto_now_add=True)
    
    # 현재 날짜 기준으로 최신 여부 return해주는 함수
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)

    # 출력시 기본 형태 지정
    def __str__(self):
        if self.was_published_recently():
            new_badge = 'NEW!!!'
        else:
            new_badge = ''
        return f'{new_badge} 제목: {self.question_text}, 날짜: {self.pub_date}'
```
쉘에서 아래와 같이, 객체에 함수 `was_published_recently()`를 붙여서 호출 가능함
```python
> from polls.models import *
> new_q = Question(question_date='new question')
> new_q.save()
> neq_q.was_published_recently()
True
```

