# 📌 JOIN
SQL JOIN은 두 개 이상의 테이블들의 공통 필드를 merge하는 것이다.

## #️⃣ [1] JOIN 종류
### ✅ 1. Inner Join
- 양쪽 테이블의 공통된 부분만 리턴만
- 양쪽 테이블 필드가 __모두 채워진 상태로 리턴됨__
- 디폴트 join
```sql
SELECT * FROM vital v 
JOIN alert a ON v.vitalId = a.vitalId;
```

### ✅ 2. Left Join
- 왼쪽 테이블의 모든 레코드를 리턴함.
- 오른쪽 테이블은 왼족 레코드와 매칭되는 경우만 채워지고, 왼쪽에는 있고 오른쪽에는 없는 경우는 NULL로 처리됨
```sql
SELECT * FROM vital v 
LEFT JOIN alert a ON v.vitalId = a.vitalId;
```

### ✅ 3. Right Join
- left 조인의 반대임
```sql
SELECT * FROM vital v 
RIGHT JOIN alert a ON v.vitalId = a.vitalId;
```

### ✅ 4. Full Join
- 왼쪽, 오른쪽 테이블 __모든 레코드__  리턴함
- 단 __'매칭'되는 경우에만 "양쪽 테이블의 모든 필드들이 채워진 상태"로 리턴됨__ 
```sql
SELECT * FROM vital v 
FULL JOIN alert a ON v.vitalId = a.vitalId;
```

### ✅ 5. Cross Join
- 왼쪽 테이블과 오른쪽 테이블의 __모든 레코드들의 "조합"__ 을 리턴함
```sql
SELECT * FROM vital v 
CROSS JOIN alert a ON v.vitalId = a.vitalId;
```

### ✅ 6. Self Join 
- 동일한 테이블을 alias를 달리해서 자기 자신과 조인함
- 보통 조건을 다른 조인과는 달리해서 self join함
```sql
SELECT * FROM vital v1 
JOIN vital v2 ON v1.vitalId = v2.vitalId;
```
<br>

## #️⃣ [2] JOIN 시 고려사항
1. 중복 레코드 없어야 함
2. Primary Key의 uniqueness 보장됨을 체크
3. 조인하는 테이블간의 관계를 명확히 정의하여, 어느 테이블을 베이스로 잡을지 결정해야 함 

### ✅ 테이블간의 관계 

#### ▶ One to one 
- 두 테이블간에 조인키가 __양쪽에 각각 한 번씩__ 나오는 형태
- 종류 :
    - 완전한 one to one : e.g. : user_session_channel & session_timestamp
    - 한 쪽이 부분집합이 되는 one to one : user_session_channel & session_transaction

#### ▶ One to many 
- 한 테이블의 조인이 되는 키가 __한번 나오지만 다른 테이블의 여러번__ 나올 수 있는 형태
- 방향만 바꾸면 many to one

#### ▶ many to one 
- One to many과 반대로 동일
- e.g. 직원과 부서테이블에 각각 departmentId가 있으면 얘를 기준으로 many to one임

#### ▶ many to many
- 두 테이블 __모두 조인되는 키가 여러번__ 나올 수 있는 경우 
- 이런 경우는 많지 않음. __될 수 있으면 one to one이나 one to many로 변환__ 하여 조인하는 것이 덜 위험함


