# 📌 관계형 데이터베이스 SQL 실습
> Redshift에 저장된 테이블 정보 불러와 Colab에서 진행 

## #️⃣ 테이블 구조
> 웹 서비스 사용자/세션 정보 

Redshift에 raw_data 스키마(폴더)안에 user_session_channel와 session_timestamp 테이블이 위치함

### ✅ 테이블 1: user_session_channel
> 사용자 세션 정보

필드 :  
- userId (int) : 사용자 uid
- sessionId (varchar(32)) : 고유 session값
- channel (varchar(32)) : 웹서비스 이름


레코드 예시 :
```
*------------------------------------------------------*
| userId |              sessionId            | channel |
*------------------------------------------------------*
|   20   |  7cdace91c487558e27ce54df7cdb299c |  Naver  |
*------------------------------------------------------*

channel 종류 : Instagram, Naver, Youtube, Facebook, Oraganic, Google
```
<br>

### ✅ 테이블 2: session_timestamp
> 세션id와 타임스탬프

필드 :
- sessionId (varchar(32)) : 고유 session값
- ts(timestamp) : 타임스탬프   

레코드 예시 :
```
*----------------------------------------------------------*
|             sessionId             |         ts           |
*----------------------------------------------------------*
|  7cdace91c487558e27ce54df7cdb299c |  2023-05-10 38:26:00 |
*----------------------------------------------------------*
```

<br>

### ✅ 테이블 3: session_transaction
> 실제 구입한 것과 관련, 모든 세션이 해당되진 않음

필드 :
- sessionId (varchar(32)) : 고유 session값
- refunded (boolean) : 반환 여부
- amount (integer) : 상품구매액수

<br>

### ✅ 테이블 4: channel
>  채널 목록

필드 :
- channelname (varchar(32)) : 사용자들이 접점으로 사용할 수 있는 채널

<br><br>

# 📌 예제1- 기초
> SQL 기초 학습 

## #️⃣ 채널 종류 조회
유일한 채널 이름 알고 싶을때
```sql
SELECT DISTINCT channel                     
FROM raw_data.user_session_channel;
```

## #️⃣ 채널 별 데이터 카운트 
유일한 채널 이름 알고 싶을때
```sql
SELECT channel, COUNT(1)                    
FROM raw_data.user_session_channel
GROUP BY 1;
```

## #️⃣ 모든 레코드 수 카운트
```sql
SELECT COUNT(1)  --  COUNT(*) 가능
FROM raw_data.user_session_channel;
```

## #️⃣ 특정 채널의 레코드 수만 카운트
### ✅ 1. 채널 한 개
```sql
SELECT COUNT(1)
FROM raw_data.user_session_channel
WHERE channel = 'Naver'; 
```
### ✅ 2. 채널 여러 개
```sql
SELECT COUNT(1)
FROM raw_data.user_session_channel
WHERE channel in ('Naver', 'Google'); 
```

## #️⃣ CASE WHEN 활용하기
```sql
SELECT
  channel,
  CASE
    WHEN channel in ('Facebook', 'Instagram') THEN 'Social-Media'
    WHEN channel in ('Google', 'Naver') THEN 'Search-Engine'
    ELSE 'Something-Else'
END channel_type
FROM raw_data.user_session_channel
LIMIT 10;
```
<br><br>

# 📌 예제2- 응용
> SQL Group by 이용

TIP!
1. 어떤 정보(필드)가 필요한 지 생각하기   
2. 필드가 속한 테이블을 생각하여 필요에 따라 Join 및 Group by   
3. 나머지 기타 연산 수행  
4. ORDER BY 

## #️⃣ 월별 총 세션 수 
```sql
SELECT 
    LEFT(ts, 7) AS mon,
    COUNT(1) AS session_cnt
FROM raw_data.session_timestamp
GROUP BY 1  -- GROUP BY mon이나 Group by LEFT(ts, 7)와 같음
ORDER BY 1; 
```
- ts는 timestamp 타입으로, `2023-05-10 38:26:00`와 같이 구성되어 있다.
    - LEFT 함수를 쓰면 앞에서부터 7글자를 추출하니 `'2023-05'`이 추출된다. 

## #️⃣ 가장 많은 사용된 채널
가장 많이 사용되었다는 정의는 `사용자기반` 혹은 `세션기반`에 따라 달라 질 수 있다.

### ✅ 세션 기반 
```sql
SELECT
  channel,
  COUNT(1) AS session_count, 
  COUNT(DISTINCT userId) AS user_count
FROM raw_data.user_session_channel
GROUP BY channel, -- GROUP BY 1 동일
ORDER BY 2 DESC;
```
세션 기반으로 정렬함

### ✅ 사용자 기반 기반 
```sql
SELECT
  channel,
  COUNT(1) AS session_count, 
  COUNT(DISTINCT userId) AS user_count
FROM raw_data.user_session_channel
GROUP BY channel, -- GROUP BY 1 동일
ORDER BY 3 DESC;
```
사용자 기반으로 정렬함

## #️⃣ 가장 많은 세션을 만들어낸 사용자 ID
```sql
SELECT
  userId,
  COUNT(1) AS count, 
FROM raw_data.user_session_channel
GROUP BY userId, -- GROUP BY 1 동일
ORDER BY 1 DESC
LIMIT 1;
```
가장 많이 세션을 만들어낸 사용자 ID이므로, 사용자 ID로 group by 후 그 개수를 COUNT한다.

## #️⃣ 월별 유니크한 사용자 수 (MAU - Monthly Active User)
> MAU (Monthly Active User)에 해당함
> 한 사용자는 한 번만 카운트되어야 함
```sql
SELECT 
    LEFT(A.ts, 7) AS month,
    COUNT(DISTINCT B.userId) AS mau
FROM raw_data.session_timestamp A 
JOIN raw_data.user_session_channel B on A.sessionId = B.sessionId
GROUP BY month, -- GROUP BY 1과 동일함
ORDER BY month DESC;
```
- 디폴트 JOIN은 INNER JOIN임
- `LEFT(A.ts, 7)` 와 동일한 함수
    - `TO_CHAR(A.ts, 'YYYY-MM')` : timestamp 형을 자동으로 문자열로 바꾸고, 지정한 형식에 따라 출력함
    - `DATE_TRUNC('month', A.ts)` : timestamp 형으로 return 하는데, 이때 일은 1일로 하여 보냄
    - `SUBSTRING(A.ts, 1, 7)` : 문자열 리턴
- `COUNT(DISTINCT B.userId)` : DISTINCT로 userID를 묶고 그 개수를 센다.
- `AS`는 Alias 별칭 붙일때 사용되고, 필수는 아니다.

## #️⃣ 월별 채널별 유니크한 사용자 수
```sql
SELECT 
    TO_CHAR(A.ts, 'YYYY-MM') AS month,
    channel,
    COUNT(DISTINCT B.userid) AS mau
FROM raw_data.session_timestamp A
JOIN raw_data.user_session_channel B ON A.sessionid = B.sessionid
GROUP BY 1, 2 
ORDER BY 1 DESC, 2;
```
바로 위 예제에서 '채널별' 붙었으니까, 채널별로 group by하는 것이 추가됨

<br><br>

# 📌 예제3- 응용
> SQL CTAS 응용 및 데이터 품질 확인 방법들

## #️⃣ 항상 시도해봐야하는 데이터 품질 확인 방법들
1. 중복된 레코드들 체크
2. 최근 데이터의 존재 여부 체크 (freshness)
3. Primary key uniqueness가 지켜지는지 체크
4. 값이 비어있는 컬럼들이 있는지 체크

## #️⃣ 1. 중복된 레코드들 체크하기
### ✅ 방법 1
```sql
SELECT COUNT(1)
FROM adhoc.hyemin_session_summary;

SELECT COUNT(1)
FROM (
 SELECT DISTINCT userId, sessionId, ts, channel
 FROM adhoc.hyemin_session_summary
);
```
- 두번째 중복을 없앤 DISTINCT를 했을 때 첫 번째와 차이가 없다면 중복 없음
- 이거 말고 __모든 필드를 group by해서 count하는 방법__ 도 있는데 모두 1이면 중복 없는것임. 그게 더 편할수도

### ✅ 방법 2
CTE 이용하여 임시 테이블 만들어 체크하는 방법
```sql
With ds AS (
 SELECT DISTINCT userId, sessionId, ts, channel
 FROM adhoc.hyemin_session_summary
)
SELECT COUNT(1)
FROM ds
```
- 임시 테이블 ds를 만듦
- 이렇게 만들면 이를 재사용할 수있어서 좋음

## #️⃣ 2. 최근 데이터의 존재 여부 체크하기
```sql
SELECT 
  MIN(ts), MAX(ts)
FROM adhoc.hyemin_session_summary;
```
- 최근에 업데이트 된 데이터가 있는지 확인하는 것이 중요함

## #️⃣ 3. Primary key uniqueness가 지켜지는지 체크
```sql
SELECT 
  sessionId, COUNT(1)
FROM adhoc.hyemin_session_summary
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1;
```
- 해당 테이블에서 sessionId가 Primary Key이므로, 이를 Count하여 모두 1이면 uniqueness 지켜지는 것임

## #️⃣ 4. 값이 비어있는 컬럼들이 있는지 체크
```sql
SELECT
  COUNT(CASE WHEN sessionId is NULL THEN 1 END) sessionid_null_count,
  COUNT(CASE WHEN userId is NULL THEN 1 END) userid_null_count,
  COUNT(CASE WHEN ts is NULL THEN 1 END) ts_null_count,
  COUNT(CASE WHEN channel is NULL THEN 1 END) channel_null_count
FROM adhoc.hyemin_session_summary;
```
- null일때만 1로 return 하므로, null의 개수를 카운트함

<br><br>

# 📌 예제4- 응용
> 3일차 숙제
## 채널별 월별 매출액 테이블 만들기 
> adhoc 밑에 CTAS로 본인이름을 포함한 테이블로 만들기
- session_timestamp,user_session_channel,session_transaction 사용
- 아래와 같은 필드로 구성
  - month 
  - channel
  - uniqueUsers (총방문 사용자)
  - paidUsers (구매 사용자: refund한 경우도 판매로 고려)
  - conversionRate (구매사용자 / 총방문 사용자)
  - grossRevenue (refund 포함)
  - netRevenue (refund 제외)


## 과제 제출물 
```sql
SELECT 
    TO_CHAR(c.ts, 'YYYY-MM') AS month,
    channel,
    COUNT(DISTINCT A.userid) AS uniqueUsers,
    COUNT(CASE WHEN B.amount IS NOT NULL THEN 1 END) AS paidUsers,
    paidUsers/uniqueUsers::float AS conversionRate,
    SUM(CASE WHEN B.amount IS NOT NULL THEN B.amount END) AS grossRevenue,
    SUM(CASE WHEN B.amount IS NOT NULL AND B.refunded is False THEN B.amount END) AS netRevenue
FROM raw_data.user_session_channel A
LEFT OUTER JOIN  raw_data.session_transaction B ON A.sessionid = B.sessionid
JOIN raw_data.session_timestamp C ON A.sessionid = C.sessionid
GROUP BY 1, 2 
ORDER BY 1 DESC, 2;
```