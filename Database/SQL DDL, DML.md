# 📌 SQL 기본
- 구조화된 데이터 처리를 위함 

## #️⃣ 기본 문법
- SQL문은 `;`으로 분리
- 주석
    - `--` : 인라인 한 줄짜리 주석
    - `/* -- */` : 여러 줄 주석
- SQL 키워드는 __대문자__ 로 사용하는 등 포맷팅 필요
    - 팀 프로젝트면 팀에서 사용하는 공통 포맷 필요함
- __테이블 명/필드__ 이름 지정시 규칙 정하는 것이 중요
    - 단수형 vs 복수형 
    - __ vs CamelCasing


<br><br>
<hr>

# SQL DDL
> 테이블 구조 정의 언어

## #️⃣ CREATE TABLE
```sql
CREATE TABLE 폴더명.테이블명(
    필드명 타입 (옵션),
    userid int,
    sessionid varchar(32) primary key,
    channel varchar(32)
);
```
- 2단계 구조에서 폴더 생성하는 것도 포함됨
    - 2단계 구조  : 폴더(스키마) 밑에 테이블 위치 지정 
- `Primary key uniqueness` : 데이터웨어하우스에서는 Primary key 지정할 수 있지만 무시됨(지켜지지 않음)
    - 데이터웨어하우스 : Redshift, Snowflake, BigQuery
    - 지정은 할 수 있으나 보장되지 않음 (__무시됨__). __이후 sql 실행 시 해당 키는 primary key임을 알려주는 용도임__
- `CTAS` : `CREATE TABLE 테이블명 AS SELECT` 
    - SELECT로 읽어드린 데이터로 새로 만든 테이블에 데이터 넣음
    - `CREATE TABLE 테이블명 AS INSERT` 와 비교 

<br>

## #️⃣ DROP TABLE
```sql
DROP TABLE 테이블명 -- 없는 테이블 지우려고 하면 에러남

DROP TABLE IF EXISTS 테이블명 --테이블이 있는지 확인하고 삭제함
```
- DROP TABLE 실행하면 데이터와 함께 테이블 자체가 사라짐
    - 반면, `DELETE FROM`의 경우 조건에 맞는 레코들을 지우고 __테이블 자체는 존재함__

<br>

## #️⃣ ALTER TABLE
기본 틀 
```sql
ALTER TABLE 테이블 명 (실행할 명령) (옵션 등등)
```

1. 새로운 컬럼 추가

``` sql
ALTER TABLE 테이블이름 ADD COLUMN 필드이름 필드타입;
```
2. 기존 컬럼 이름 변경:
```sql
ALTER TABLE 테이블이름 RENAME 현재필드이름 to 새필드이름
```

3.  기존 컬럼 제거
```sql
ALTER TABLE 테이블이름 DROP COLUMN 필드이름;
```

4. 테이블 이름 변경
```sql
ALTER TABLE 현재테이블이름 RENAME to 새테이블이름
```

<br><br>
<hr>

# 📌 SQL DML
## #️⃣ SELECT 
기본틀 
```sql
SELECT 함수(필드) FROM 테이블 명 
                WHERE 선택 조건
                GROUP BY 필드명1, 필드명2, ...
                ORDER BY 필드명1, ... [DESC|ASC] -- 필드 이름 대신 숫자 사용 가능 
                LIMIT N;
```
- `FROM` : 레코드와 필드를 읽어오는 테이블 
- `WHERE` : 선택 조건
- `GROUP BY` : 정보를 그룹 레벨에서 가져와 사용함
    - DAU, WAU, MAU 계산은 GROUP BY 이용 
- `ORDER BY` : 레코드 순서 결정. 디폴트는 오름차순 ASC 
    - 여러개 칼럼 지정 가능 `ORDER BY 1 DESC 2,3;`
    - NULL 값 순서
        - 오름차순일 경우, NULL 마지막에 위치
        - 내림차순일 경우, NULL 처음에 위치함
        - NULL 출력 위치 바꾸고 싶으면 `NULLS FIRST` 혹은 `NULLS LAST` 사용함
- `LIMIT` : 위에서부터 N개만 출력
- 보통 다수의 테이블을 `JOIN`하여 사용함

<br>

## #️⃣ INSERT
```sql
INSERT INTO 테이블명 (열1, 열2, ...) VALUE (값1, 값2, ...);
INSERT INTO 테이블명 VALUES (값1, 값2, ..., 값n);
```
테이블에 레코드 추가

## #️⃣ UPDATE
```sql
UPDATE FROM 테이블명 SET 칼럼1=값1, 칼럼2=값2, ... WHERE 조건 ;
```
테이블 레코드 필드 값 수정 

## #️⃣ DELETE 
```sql
DELETE FROM 테이블명 WHERE 조건;
```
데이블에서 레코드 삭제
- Transaction 보장 

## #️⃣ TRUNCATE

```sql
TRUNCATE TABLE 테이블명;
```
테이블에서 레코드 모두 삭제
- Transaction 보장 X 


<br><br>
<hr>

# 📌 추가 옵션 
## #️⃣ DISTINCT
 중복 제거
```sql
/* 유일한 채널 이름을 알고 싶을 때 */
SELECT DISTINCT channel 
        FROM raw_data.user_session_channel;
```
<br>

## #️⃣ COUNT 
__주어진 인자__ 에 대한 개수를 카운트함   
- __만약 주어진 인자가 NULL이면 카운트 X__
        
```sql
/* 모든 레코드들의 channel값을 보고, 같은 channel 값을 가지는 것을 하나로 묶어서 그 개수를 카운트 함 (즉, 채널별 카운트) */
SELECT channel, COUNT(1)
        FROM raw_data.user_session_channel
        GROUP BY 1; -- 1은 SELECT문의 첫번째 인자 channel을 뜻함
```
- GROUP BY 로 같은 channel끼리로 묶고 축소시키고, 
- COUNT(1)로 같은 channel을 갖는 레코드가 몇 개인지 카운트함
- COUNT 함수 예시 

<details> 
<summary> COUNT 함수 예시  </summary>

``` 
# 데이터 예시
*-------*
| value |  
+++++++++ 
|  NULL | 
|   1   |
|   1   |
|   0   |
|   0   |
|   2   |
|   3   |
*-------*
```
- `SELECT COUNT(1) FROM count_test;` : 7
    - NULL이 아니니 레코드 수 return
- `SELECT COUNT(0) FROM count_test;` : 7
- `SELECT COUNT(NULL) FROM count_test;`: 0
    - 인자가 NULL임 -> 0
- `SELECT COUNT(value) FROM count_test;` : 6
    - 인자의 value의 레코드 값중 NULL 빼고 return
- `SELECT COUNT(DISTINCT value) FROM count_test;`: 4
    - DISTINCT하면 NULL, 0, 1, 2, 3인데 NULL 제외하면 4

</details>

<br>

## #️⃣ `CASE WHEN` 
- 조건에 맞게 __필드 값 변환__ 할 때 사용 
```sql
CASE
 WHEN 조건1 THEN 값1
 WHEN 조건2 THEN 값2
 ELSE 값3
END 필드이름

/* 예시 */ 
SELECT CASE
 WHEN channel in ('Facebook', 'Instagram') THEN 'Social-Media'
 WHEN channel in ('Google', 'Naver') THEN 'Search-Engine'
 ELSE 'Something-Else'
END channel_type
FROM raw_data.user_session_channel;
```
<br>


## #️⃣ `NULL`
- NULL은 값 없음을 나타내며 0과 ""와는 다르다.
- 특정 필드 값이 NULL 인지 확인하기 위해서는 `is NULL` 혹은 `is not NULL`을 사용한다. 
- NULL과 __사칙연산하면 결과는 다 NULL이다.__  

<br>

## #️⃣ WHERE 절 연산
> 아래 연산들은 CASE WHEN 사이에서도 사용 가능함

### ✅ IN
같은 조건 여러 개를 걸 때 
```sql
SELECT * FROM users WHERE address in ('Seoul', 'Incheon');
SELECT * FROM users WHERE address = 'Seoul' OR address = 'Incheon';
```
### ✅ LIKE and ILIKE
LIKE는 대소문자 구분하고, ILIKE는 대소문자 구분 안 함
```sql
WHERE name LIKE 'G%' ; -- G로 시작하는 모든 문자
WHERE name LIKE '%o%'; -- o가 포함된 모든 문자
```
- NOT LIKE나 NOT ILIKE도 사용 가능

### ✅ BETWEEN
- 양쪽 등호 포함하는 관계
```sql
WHERE age BETWEEN 10 and 19; -- 10 <= age <=19
```
<br>

## #️⃣ String 연산
- `LEFT(str, N)` : 문자열의 왼쪽부터 n 길이만큼 자름
- `RIGHT(str, N)` : 문자열의 오른쪽부터 n 길이만큼 자름
- `REPLACE(str, exp1, exp2)` :
- `UPPER(str)` : 모두 대문자로 변경
- `LOWER(str)` : 모두 소문자로 변경
- `LEN(str)` : 문자열 길이
- `LPAD(str, n, [c])` : str을 n자리수로 만들어, 왼쪽 공백을 c로 대체함
- `RPAD(str, n, [c])` : str을 n자리수로 만들어, 오른쪽 공백을 c로 대체함
- `SUBSTRING(str, start, n)` : 문자열의 start번째부터 n개 문자 자름

<br>

## #️⃣ 타입 변환
### ✅ DATE 타입 변환 
- `CONVERT_TIMEZONE ( ['source_timezone',] 'target_timezone', 'timestamp')`   
: 현재 타입스탬프의 시간대를 새로운 타임스탬프 시간대로, timestamp로 지정한 표현식으로 변환함
    - `select pg_timezone_names();` : 타임존 목록 출력 

- `DATE_TRUNC('datepart', timestamp)` :  첫번째 인자가 어떤 값을 추출하는지 지정 (week, month, day, …)  (Redshift, PostgreSQL 등)
    - 참고 : [Amazon Redshift 가이드 - DATE_TRUNC](https://docs.aws.amazon.com/ko_kr/redshift/latest/dg/r_DATE_TRUNC.html)

- `EXTRACT` or `DATE_PART`: 날짜시간에서 특정 부분의 값을 추출가능
    - `EXTRACT(날짜요소 from 컬럼명)` (오라클)
        - 날짜요소 : YEAR, MONTH, DAY, HOUR, MINUTE, SECOND
    - `DATE_PART(datepart, {date|timestamp})` (Redshift, PostgreSQL 등)
        - 참고: [Amazon Redshift 가이드 - DATE_PART](https://docs.aws.amazon.com/ko_kr/redshift/latest/dg/r_DATE_PART_function.html)

- `DATEDIFF(구분자,Start_Date,End_Date)` : start와 end 날짜 사이의 구분자만큼(날짜, 일수) 차이 구함

- `DATEADD(DAY|MONTH,YEAR, n, data)` : 월,일,년 계산하는 함수
    - e.g. `SELECT DATEADD(DAY, -10, '2023-05-09') AS before10days ... `

- `GETDATE()` : 현재 날짜와 시간 가져옴  
e.g.2023-05-09 18:56:48.049 


### ✅ 기타 타입 변환 
- `TO_CHAR(data, ['출력형식'])` : 숫자/날짜형의 data를 문자형으로 바꾼 후 선택적으로 출력 형식으로 지정함
- `TO_TIMESTAMP(data, ['날짜형식'])` : 문자형의 data를 지정한 날짜 형식으로 정함

<br>

## #️⃣ Type Casting
예시로 1/2 결과는 정수형이므로, float형 반환을 위해서는 분자or분모 둘 중 하나를 float로 캐스팅해야됨 
### ✅ cast(칼럼명 as 타입)
해당 칼럼을 지정한 타입으로 변환함