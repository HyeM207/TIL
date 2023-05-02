
# Django Model Field
> models.py에서 field에서 사용할 수 있는 옵션과 타입을 정리한다. 

[Model field reference](https://docs.djangoproject.com/en/4.2/ref/models/fields/#field-options)

<br>

# 📌  Field Option
### null    
- value :  True/False
- False가 디폴트로 __not null__ 을 의미함

### blank
- value : True/False
- True로 설정하면 필드 값을 비워줄 수 있음 


### choices
- value : 값으로 둘 리스트와 같은 아이템 등록 
- 주로 다른 필드 값에 영향을 받아 값을 설정시 이용함 

### primary_key
- value : True/False
- True로 하면 `null=False`와 `unique=True` 자동으로 지정함

### verbose_name
- value : 사용자가 읽을 수 있는 이름으로 지정
- 이는 이후 admin 페이지에서 보여지는 이름이 됨

### validators
- 특정 필드를 검증할 때 validator를 지정가능함

<br>

# 📌 Field Type
> 장고에서 사용할 수 있는 데이터 타입

## #️⃣ 기본 type
- 문자열: CharField, TextField
- 숫자: IntegerField, FloatField, DecimalField
- 날짜/시간: DateField, TimeField, DateTimeField
- 파일: FileField, ImageField
- 참/거짓: BooleanField
- 선택: ChoiceField
- 그 외 관계형 필드 : ForeignKey, OneToOneField, ManyToManyField 등


### AutoField
- auto increment하는 필드 
- 기본키 필드에 적용되는 디폴트

### BooleanField
- True/False 필드

### CharField
```
class CharField(max_length=None, **options)¶
```
- string 필드

### DateField
```
class DateField(auto_now=False, auto_now_add=False, **options
```
- datetime.date 타입 저장 
- `auto_now` : 객체가 저장될 때마다 해당 시간 저장
- `auto_now_add` : 객체가 처음 만들어질 때 해당 시간 저장

### DateTimeField
```
class DateTimeField(auto_now=False, auto_now_add=False, **options)¶
```
- date와 time 저장 (datetime.datetime)

<br>

## #️⃣ Relationship fields
### ForeignKey
### ManyToManyField