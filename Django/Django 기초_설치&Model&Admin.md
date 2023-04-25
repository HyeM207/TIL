# 📌 Django 설치까지 준비과정
## #️⃣ 준비 사항 
python 설치하기

### ✅ Python 가상 환경 생성 및 실행 그리고 비활성화
```bash
# 가상환경 생성
py -m venv PROJECT_NAME

# 가상환경 실행 
PROJECT_NAME\Scripts\activate.bat

# 비활성화
deactivate
```
### ✅ 가상 환경 안에 Django를 설치하기
```bash
py -m pip install Django
django-admin –-version
```
<br><br>

# 📌 Django 프로젝트 시작
## #️⃣ Django 프로젝트 생성
```bash
django-admin startproject PROJECT_NAME
# 예시
django-admin startproject mysite
``` 
위의 명령어를 입력하면 PROJECT_NAME 로 하는 장고 프로젝트가 생성된다.
<br>

## #️⃣ Django 서버 시작 
```bash
cd PROJECT_NAME
python manage.py runserver
```
- 만든 장고 프로젝트 폴더로 들어가서 `python manage.py runserver`  명령어로 서버를 실행할 수 있다.
그러면 기본으로 'http://127.0.0.1:8000/'가 실행되며 웹브라우저로 접속하면 django기본 페이지를 확인할 수 있다.

<br><br>

# 📌  Django App 만들기 
하나의 프로젝트는 여러개의 App으로 구성됨 
## #️⃣ App만들기
```bash
python manage.pt startapp APP이름
# 예시
python manage.pt startapp polls
```
app을 만들면 기본 프로젝트 경로에서 APP이름으로 폴더가 생기게 된다. 해당 폴더는 APP 관련하여 urls 혹은 views등을 설정할 수 있다.


<br><br>

# 📌  Django App으로 URL 지정하기
먼저 기본 'http://127.0.0.1:8000/' url 경로에서 app이름으로 경로를 추가하고 페이지 뷰를 조정하려면 다음 파일들을 수정해줘야한다. 

1. `기본 urls 파일`
    - 기본 도메인아래로 연결되는 app을 지정할 수 있음
2. `APP 폴더 안의 urls 파일`
    - url에서 app뒤로 오는 경로들 지정 가능  
3. `APP 폴더 안의 views 파일`
    - app의 url과 연결되는 페이지 뷰 조정 가능

## #️⃣ 예시
만약 'http://127.0.0.1:8000/polls/' 와  'http://127.0.0.1:8000/polls/some_url'로 연결되는 페이지를 구성한다고 하자

1. 기본 urls.py 수정
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```

2. polls 앱의 urls.py 수정
```python
urlpatterns = [
    path('', views.index, name='index'),
    path('some_url', views.some_url),
]
```
3. polls 앱의 views.py 수정
```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello world!")

def some_url(request):
    return HttpResponse("Hello world! This is some_url page")
```

<br><br>

# 📌  Django Model (DB연결)
## #️⃣  Model 이란?
model은 db를 테이블별로 읽어서 하나의 테이블에 저장되어 있는 값을 코드에서 읽어들일 수 있도록 도와주는 기능으로 `models.py`파일에서 작성하면 된다. (`ORM`)

[ 과정 ]
1. 모델 생성 
2. app 등록
3. 모델을 테이블에 작성하기 위한 '마이그레이션'을 만든다.
4. 해당 모델에 맞는 테이블을 만든다. 

<br>

## #️⃣ Model Layer
`models.py`를 작성할 때 타입별로 작성할 수 있는 것을 말하며 다음과 같은 것들이 있다.
- `BooleanField`
- `CharField`
- `DateField` : 날짜만 저장
- `DateTimeField` : 날짜 시간 
- `FlaotField`
- `JSONField`
- `TextField`
- [그 외 타입](https://docs.djangoproject.com/en/4.2/ref/models/fields/#field-types) 

<br>

## #️⃣ 과정 
### ✅ 1. 모델 생성
```python
# polls > models.py
class Question(models.Model):
    question = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```
### ✅ 2. app 등록
마이그레이션을 만들기 전에 app 등록이 필요함 
- 기본 프로젝트 경로의 `settings.py`의 `INSTALLED_APPS`리스트 목록에 해당 app을 등록한다.
```python
INSTALLED_APPS = [
    'polls.apps.PollsConfig', # app 등록
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```
### ✅ 3. 마이그레이션 
터미널에서 다음 명령어를 입력한다.
- 처음 models.py를 작성하거나 수정 후에 명령어를 입력하면 변경되거나 추가된 사항을 반영하여 마이그레이션 한다.
```bash
python manage.py makemigrations APP이름
# 예시
python manage.py makemigrations polls

'''출력내용
Migrations for 'polls':
  polls\migrations\0001_initial.py
    - Create model Question
    - Create model Choice
'''
```

마이그레이션 내용을 살펴보기 위해 다음 명령어를 입력한다. 
이때 상태번호 부분에는 위에서 마이그레이션할 때 출력된 `0001`을 입력해주면 된다. 
```bash
python manage.py sqlmigrate APP이름 상태번호
# 예시 
python manage.py sqlmigrate pools 0001

''' 출력 내용 
--
-- Create model Question
--
CREATE TABLE "polls_question" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "question" varchar(200) NOT NULL, "pub_date" datetime NOT NULL);
--
-- Create model Choice
--
CREATE TABLE "polls_choice" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "choice_text" varchar(200) NOT NULL, "votes" integer NOT NULL, "question_id" bigint NOT NULL REFERENCES "polls_question" ("id") DEFERRABLE INITIALLY DEFERRED);
CREATE INDEX "polls_choice_question_id_c5b4b260" ON "polls_choice" ("question_id");
COMMIT;
'''
```
- SQL 쿼리문이 터미널 창에 띄워진다.
- 이때 foreign key에 대한 index를 자동으로 만들어주는 것을 확인 가능하다.  


### ✅ 4. 테이블 만들기
```python 
python manage.py migrate

'''출력내용 일부
Apply all migrations: admin, auth, contenttypes, polls, sessions
'''
```
- 등록한 polls 외에도 `INSTALLED_APPS`에 기본으로 등록된 app들이 같이 테이블이 만들어진 것을 확인할 수 있다.

### ✅ 추가. 이전 마이그레이션 상태로 변경 
- __만약 이전 마이그레이션 버전으로 db를 수정하고 싶다면 상태번호를 이전 마이그레이션 번호로 수정__ 하면 된다. 
```python 
python manage.py migrate APP이름 상태번호

# 예시
python manage.py migrate polls 0001
```
<br>

### ✅ 추가. 변경한 model 반영하기
- 변경한 model을 마이그레이션 하여 테이블을 변경하는 코드이다.
- APP이름은 필요에따라 쓰고, 안 쓰면 전체 변경사항을 반영하여 업데이트 한다.
```python 
python manage.py makemigrations APP이름
python manage.py migrate APP이름
```
<br>

## #️⃣ 기본 DB인 db.sqlite 확인하기
1. 기본 프로젝트 경로로 가서 다음 명령어 입력로 sqlite db 터미널로 접속하여 테이블과 스키마 등을 확인할 수 있다. 

```bash
# sqlite db 터미널로 접속 
 sqlite3 db.sqlite3

# 테이블 목록 조회
.tables

# 특정 테이블 조회 
SELECT * FROM django_migrations;

# 스키마 확인
.schema polls_question

# 터미널 종료 
CTRL+D
```

<br><br>

# 📌  Django Admin
Django Admin은 관리자들이데이터를 수정하거나 추가하는 페이지이다.

## #️⃣ Admin 만들기
```python
python manage.py createsuperuser
```
- 사용자 명과 pw를 지정해준다. 
- 만들면 자동으로 기본 프로젝트 경로의 `urls.py`에 `admin` 경로가 추가된다. 
    - `http://127.0.0.1:8000/admin/`에서는 사용자를 추가하고 수정, 삭제할 수 있는 기본 기능을 제공함
    - admin에서는 만든 model를 수정,삭제, 추가를 UI적으로 편하게 할 수 있음

<br>

## #️⃣  특정 모델을 Admin 페이지에 등록하기
- 등록하고자 하는 모델이 있는 APP 경로의 `admin.py` 파일에 접속해서 모델을 등록하는 코드를 작성한다.

```python
# polls > admin.py
from django.contrib import admin
from .models import *

# Register your models here.
admin.site.register(Question)
admin.site.register(Choice)
```
<br>

## #️⃣ Admin 페이지 커스터마이징
### ✅ models.py
- `verbose_name` : 칼럼 표시명 지정
- `@admin.display()` : admin 페이지 레이아웃 지정
    - `boolean` : True시 이모지로 UI표현
    -  `description` : 칼럼 표시명 지정
```python
from django.db import models
from django.utils import timezone
import datetime
from django.contrib import admin

class Question(models.Model):
    question_text = models.CharField(max_length=200, verbose_name='질문')
    pub_date = models.DateTimeField(auto_now_add=True, verbose_name='생성일')  

    @admin.display(boolean=True, description='최근생성(하루기준)')
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)

    # 출력시 기본 형태 지정
    def __str__(self):
        return f'제목: {self.question_text}, 날짜: {self.pub_date}'
```
<br>

### ✅ admin.py
- `fieldsets` : 표에 표시할 field 지정 (순서대로 출력)
    - `'classes': ['collapse']` : 펼치고 닫을 수 있음 
- `list_display` : 표에서 보여줄 칼럼 지정 
- `readonly_fields` : 수정 불가 
- `inlines` : 인라인 설정 (다른 model을 인라인으로 보여줌)
- `list_filter` : 필터 생성
- `search_fields` : 검색 옵션 설정 

```python
from django.contrib import admin
from .models import *

class ChoiceInline(admin.TabularInline): # 수직으로 인라인 삽입
    model = Choice
    extra = 3 # 보여줄 최대 개수 


class QuestionAdmin(admin.ModelAdmin):
    # fieldset에 작성한 순서대로 UI에보여짐
    #  (섹션, {'fields': [ 보여줄 값] })
    fieldsets = [
        ('질문 섹션', {'fields': ['question_text']}),
        ('생성일', {'fields': ['pub_date'], 'classes': ['collapse']}),  # collapse 옵션으로 펼치고 닫을 수 있음      
    ]
    list_display = ('question_text', 'pub_date', 'was_published_recently') #표에서 칼럼 만들어서 따로 보여줌
    readonly_fields = ['pub_date']  #읽기모드
    inlines = [ChoiceInline] # 인라인 설정
    list_filter = ['pub_date'] # 필터 생성
    search_fields = ['question_text', 'choice__choice_text'] # 검색창 생성

admin.site.register(Question, QuestionAdmin)
```