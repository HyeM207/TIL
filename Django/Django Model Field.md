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

### default 
- 필드의 default값지정 


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

### IntegerField
- Integer 필드 
- -2147483648 에서 2147483647 까지는 안전함

### DateField
```
class DateField(auto_now=False, auto_now_add=False, **options
```
- datetime.date 타입 저장 
- `auto_now` : 객체가 저장될 때마다 해당 시간 저장
- `auto_now_add` : 객체가 처음 만들어질 때 해당 시간 저장
    -  ex. `DateField: default=date.today`
    -  ex. `DateTimeField: default=timezone.now`

### DateTimeField
```
class DateTimeField(auto_now=False, auto_now_add=False, **options)¶
```
- date와 time 저장 (datetime.datetime)

### UUIDField
- UUID 필드. Python의 UUID 클래스 사용함
- PostgreSQL 사용시 uuid 데이터 타입으로 저장되고, 그 외 데이터베이스에는 __char(32)__ 로 저장됨 
- 데이터베이스는 UUID를 생성하지 않으므로 기본 값 사용하는 것이 좋음 
```python
# 예제
import uuid
from django.db import models


class MyUUIDModel(models.Model):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
```


<br>

## #️⃣ Relationship fields
### ForeignKey
```
class ForeignKey(to, on_delete, **options)¶
```
- `many-to-one` 관계
- `on_delete` 옵션 
    - CASCADE : 
    - PROTECT : 'ProtectedError'에러를 발생시켜 삭제 방지함
    - RESTRICT : 'RestrictedError'에러를 발생시켜 참조된 개체 삭제하지 않도록 함.   
    PROTECT와 달리 참조된 개체는 CASCADE 관계를 통해 ,동일한 작업에서 삭제되는 다른 개체도 참조하는 경우 삭제할 수 있음

### ManyToManyField
- 다대다 관계
- Argument 
    - `related_name` 
    - `related_query_name`
    - `limit_choices_to`
    - `symmetrical`
- 옵션 
    - `through` : 장고에서 자동으로 만들어주는 다대다 관리 테이블말고, 수동으로 테이블을 정의하고 싶을 때 사용함 
    - `through_fields` : through가 사용된 경우 쓰는 옵션으로, 순서대로 사용할 중간 테이블의 필드를 결정함 

### OneToOneField
- 일대일 관계
- ForeignKet를 unique=True로 쓰는 것과 같지만, 관계의 반대쪽은 단일 개체를 직접 반환함

<br>

## #️⃣ Meta options
[Model Meta options](https://docs.djangoproject.com/en/3.2/ref/models/options/#django.db.models.Options.constraints)

### Constraints 
#### UniqueConstraint
```
class UniqueConstraint(*, fields, name, condition=None, deferrable=None, include=None, opclasses=())[source]¶
```
데이터베이스에 unique한 제약조건 생성함  
옵션 
- `fields` : unique set으로 지정할 칼럼 이름 리스트
- `name` : 제약조건 이름 