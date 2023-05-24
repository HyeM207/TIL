# AWS Redshift
## #️⃣ AWS Redshft란?
**Scalable한 SQL 엔진**
- 최대 2PB까지 지원 
    - 최소 160GB로 시작해서 점진적으로 용량 증감 가능
- `데이터 웨어하우스`. still OLAP
    - 응답속도가 빠르지 않아 프로덕션 데이터베이스로 사용불가
- `Columnar storage`
    - 컬럼별로 저장함하여 __컬럼별 압축 가능__
    - 컬럼을 추가하거나 삭제하는 것이 아주 빠름
- `벌크 업데이트 지원`
    - 다른 BigQuery, Snowflake도 지원하는 기능
    - Redshift의 경우 레코드가 들어있는 파일을 S3로 복사후 `COPY`커맨드로 Redshift로 일괄 복사
- `고정 용량/비용` SQL 엔진
    - BigQuery, Snowflake는 `가변용량/비용`이라 비용 예측이 어렵지만, 확장성 좋음
    - __최근 가변 비용 옵션도 제공 (Redshift Serverless)
- `primary key uniqueness`를 보장하지 않음
    - 다른 데이터 웨어하우스도 동일
    - 프로덕션 데이터베이스들은 보장함
- Postgressql 8.x이 호환됨
    - 모든 기능을 지원하지는 않지만, Postgressql 8.x을 지원하는 툴이나 라이브러리로 액세스 가능(JDBC/ODBC)
    - __SQL이 메인 언어__
        - 데이터 모델링이 중요함 
- `데이터 공유 기능(Datashare)`
    - Snowflake의 기능을 따라한 것으로, 다른 AWS계정과 특정 데이터 공유를 가능하게 함


<br><br>

## #️⃣ 비용 및 스케일링
### ✅ Redshift 옵션 
- 소규모인 경우(많은 데이터를 다루진 않을 때) : dc2.large, dc2.8xlarge를 사용
- 중간이상 규모 : ds2.xlarge, ds2.8xlarge 이용
- 대규모 : ra3.4xlarge, ra3.16xlarge

### ✅ Redshift 스케일링 방식 
- 용량이 부족해질 때마다 _새로운 노드를 추가_ 하는 방식으로 스케일링 함
- `Resizing`이라 부르며 __Auto Scaling__ 옵션을 설정하면 자동으로 이뤄짐
- 스케일링 방식 
    1. `Scale Out`
        - 개수를 추가 (ex. dc2.large 한 대를 더 추가함)
    2. `Scale Up`
        - 사양을 업그레이드 (ex. dc2.large를 dc2.8xlarge로 교체)

<details><summary>✔ 번외: Snowflake, BigQuery </summary>

Snowflake, BigQuery는 Redshift과 달리 가변 비용 옵션으로 진행된다.
- 특별히 용량이 정해져 있지 않고 쿼리를 처리하기 위해 사용한 리소스에 해당하는 비용을 지불함 
    - __Scalable하다는 장점이 있지만, 비용 예측이 어렵다는 단점 존재함__
- Redshift도 가변비용 옵션인 Redshift Serverless가 존재한다. (Pay as You Go)
</details>


<br><br>

## #️⃣ Redshfit 레코드 분배와 저장 (최적화)
데이터는 분산 저장되어야 함.  
만약 Redshift가 두 대 이상의 노드로 구성되면 한 테이블의 __레코드들의 저장방식을 개발자가 직접 지정__ 해줘야한다.
- 이는 알아서 저장해주는 Snowflake, BigQuery와는 차이가 있음 


즉, __한 테이블의 레코드들을 어떻게 다수의 노드로 분배할 것이냐의 테이블 최적화가 중요하다,__

### ✅ Diststyle, Distkey, SortKey
위의 3개의 키워드로 __특정 테이블의 레코드 분배방식__ 을 설정함.
- `Diststyle` : 레코드 분배 방식 결정 
    <details><summary> 옵션 </summary>

    - `all` : 모든 노드들에 모든 레코드가 분배되도록 함
    - `even` : 라운드로빈 형태로 노드에 들어감
    - `key` : 특정컬럼의 값을 기준으로 레코드들의 다수의 노드로 분배됨
        - 주로 join이나 groupby할 때 사용함
    </details>

- `Distkey` : Diststyle이 key인 경우, 레코드가 어떤 칼럼을 기준으로 배포되는지 나타냄
- `SortKey` : 레코드가 한 노드내에서 어떤 칼럼을 기준으로 정렬되는지 나타냄
    - 보통 타임스탬프 필드가 됨 

```sql
-- 코드 예시
CREATE TABLE my_table (
    column1 INT,
    column2 VARCHAR(50),
    column3 TIMESTAMP,
    column4 DECIMAL(18,2)
) DISTSTYLE KEY DISTKEY(column1) SORTKEY(column3);
```
해당 테이블의 레코드들은 column1을 기준으로 분배되고, 같은 노드 안에서는 column3 값을 기준으로 sorting됨

<br>

<details><summary>✔ 번외: Diststyle이 key일 경우의 유의점 </summary>

Diststyle이 key인 경우 컬럼 선택이 잘못되면, 레코드 분포에 `Skew`발생함 -> 데이터 불균형 -> __분산처리의 효율성 사라짐__ 
-  BigQuery나 Snowflake에서는 이런 속성을 개발자가 지정할 필요가 없음 (시스템이 알아서 선택해줌)

</details>


<br><br>

## #️⃣ Redshfit 벌크 업데이트 방식
`벌크 업데이트(Bulk Update)`는 데이터베이스나 시스템에서 __대량의 데이터를 한 번에 업데이트__ 하는 작업을 의미합니다. 일반적으로 한 번에 여러 개의 레코드나 항목을 수정, 추가 또는 삭제하는 작업을 벌크 업데이트라고 한다.

1. Data Source로부터 데이터를 추출함
2. S3에 업로드
    - 업로드 시, 압축률이 좋은 parquet이나 바이너리 형태가 좋음
3. `COPY SQL`로 S3에서 Redshift 테이블로 한 번에
복사

```sql
COPY (스키마명).(테이블명)
FROM 's3:/(bucket명)/(폴더명)/(불러올파일)'
credentials 'aws_iam_role=(생성한 규칙의 arn)'
```


<br><br>

## #️⃣ Redshfit의 기본/고급 데이터 타입
<details><summary> [ 기본 타입 ] </summary>

> PostgreSQL와 같은 데이터 타입
>> 다만 char 부분에서 차이 있음. PostgreSQL은 1 char 단위이지만, Redshift는 1 byte단위임
>
>> TEXT 데이터 형도 PostgreSQL에서는 큰 저장 타입이지만, Redshift는 VARCHAR(256)임
- SMALLINT (INT2)
- INTEGER (INT, INT4)
- BIGINT (INT8)
- DECIMAL (NUMERIC)
- REAL (FLOAT4)
- DOUBLE PRECISION (FLOAT8)
- BOOLEAN (BOOL)
- CHAR (CHARACTER)
- VARCHAR (CHARACTER VARYING)
- TEXT (VARCHAR(256))
- DATE
- TIMESTAMP

</details>
<details><summary> [ 고급 타입 ] </summary>

- GEOMETRY
- GEOGRAPHY
- HLLSKETCH
- SUPER

</details>


<br><br>

## #️⃣ Redshift Schema (폴더) 구성
```
            DEV
             |
    -------------------------------
    |        |           |        |
raw_data    analytics   adhoc     pii
``` 
- Redshift, PostgreSQL는 위의 폴더를 `스키마`라고 부름
- `CREATE SCHEMA (폴더명)`을 관리자 권한을 가진 사람이 만들 수 있음

<details><summary> [ 폴더 구조 ] </summary>

- raw_data : ETL로 가져온 데이터 적재
- analytics : 데이터 애널리스티가 주로 관리, raw_data에 있는 데이터를 요약하여 해당 폴더에 저장 및 대시보드 만듦
- adhoc : 개발자나 데이터 종사자들이 테스트 용도로 사용
- pii : 개인정보 들어감
    - 권한있는 사람만 접근 가능하도록 하며, 권한 가진 사용자 수를 최소한으로 설정  
</details>


<br><br>

## #️⃣ 사용자별 테이블/컬럼/레코드 권한 설정 
1. 역할 (Role) 혹은 그룹(Group) 별로 스키마별 접근 권한을 주는 것이 일반적이며,
최근 트랜드는 `RBAC(Role Based Access Control)`이다.
    - Role은 그룹과 달리 계승이 가능하여 더 편리함
    - 여러 역할에 속한 사용자의 경우는 각 역할의 권한을 모두 갖게 됨
2. 컬럼/레코드별 특정 사용자/그룹/역할만 접근가능하게 할 수 있지만 좋은 방법은 아님. 
    - 보통 개인정보에 해당하는 컬럼에 적용
    - 이것보다는 __칼럼을 별도 테이블로 구성하거나 아예 데이터 시스템으로 로딩 안 하는것이 best임__

### ✅ 사용자 그룹 권한 설정 
```sql
-- 특정 그룹에게 해당 스키마에 대한 권한을 줌
GRANT (권한) ON SCHEMA (스키마명) TO GROUP (그룹명);
-- 특정 그룹에게 해당 스키마의 모든 테이블에 대한 권한을 줌
GRANT ALL ON ALL TABLES IN SCHEMA (스키마명) TO GROUP (그룹명);
```
권한은 "SELECT", "INSERT", "UPDATE", "DELETE", "ALL"등이 될 수 있음

### ✅ 컬럼 레벨 보안(Column Level Security)
테이블내의 __특정 컬럼들을 특정 사용자나 특정 그룹/역할에만__ 접근 가능하게
하는 것

### ✅ 레코드 레벨 보안(Row Level Security)
테이블내의 __특정 레코드 특정 사용자나 특정 그룹/역할에만__ 접근 가능하게
하는 것
-  특정 사용자/그룹/테이블 대상으로 `SELECT, UPDATE, DELETE` 작업에 추가 조건을 다는 형태임 (`RLS(Record Level Security)`) Policy`)라고 함



<br><br>

## #️⃣ Redshift 백업과 테이블 복구
고정 비용과 가변비용 Redshift에서 백업과 복구 차이가 있음

### ✅ 고정비용 Redshift

[자동 백업과 매뉴얼 백업]
- 자동 백업 : 기본 하루 (최대 35일)
    - 같은 지역 s3에 자동 백업되며, 다른 지역의 s3에 저장하려면 `Cross-regional snapshot copy` 설정 필요함
- 메뉴얼 백업 : 원할 때 만드는 백업. 명식적으로 삭제할 때까지 유지됨

<details><summary> [ 단위 : `Snapshot` ] </summary>

- 기본적인 백업 방식으로 마지막 백업으로부터 바뀐 것들만 저장함
- 백업으로 과거 특정 테이블 복구 간으(Table Restore)
- 과거 시점 내용으로 Redshift 클러스터 새로 생성 가능  

</details>

<br>

### ✅ 가변비용 Redshift
가변비용 Redshift역시 자동 백업과 메뉴얼 백업 둘 다 지원함 

<details><summary> [ 단위 : Recovery Points ]   </summary>

- Snapshot 상위 개념으로 `Recovery Points`존재함 
- 매 30분 마다 생성되며 과거 24시간에 대해서만 유지됨
- Recovery Points에서 Snapshot 생성함. Snapshot에서부터 테이블 복구 등이 가능함

</details>

<br><br>


## #️⃣ Redshift 관련 기타 서비스 
### ✅ Redshift Spectrum
> S3에 있는 파일을 외부 테이블들로 처리하면서 Redshift 테이블과 SQL로 조인 가능 

- 보통 S3에 큰 데이터가 있는데 이를 Redshift로 로딩하기 힘들면, 외부테이블로 설정해서 Redshift에서 조작함
- S3 외부 테이블들은 보통 `Fact테이블`, Redshift 테이블들은 `Dimension테이블`이 됨.
    - 또한 S3 Fact데이터를 `외부 테이블(External Table)`로 정의해야됨
- 1TB 스캔할 때마다 $5 비용 소모
- 단 S3와 Redshift는 같은 region에 있어야 함

#### 외부 테이블용 스미카 설정 SQL문
```sql
-- 외부 테이블용 스키마 생성
CREATE EXTERNAL SCHEMA (스키마명)
from (테이블명)
database (데이터베이스)
iam_role 'arn:...'
create external database if not exists;


-- 예제. 이후 s3의 csv를 위에서 만든 external schema에 추가
CREATE EXTERNAL TABLE external_schema.user_session_channel(
   userid integer ,
   sessionid varchar(32),
   channel varchar(32)
)
row format delimited 
fields terminated by ',' -- csv파일이라 split
stored as textfile
location 's3:/bucket명/경로/*.csv';

```
- 외부 테이블용 스키마 생성하려면 먼저 데이터베이스를 생성해야됨
- iam_role은 __s3FullAccess와 AWSGlueConsoleFullAccess__ 권한이 주어져야함
- ‼ 주의 : 외부 스키마 생성후 테이블을 읽어다 적용할 때, 테이블 형식이 외부 파일과 꼭 맞아야한다.


<details><summary> ✔ Fact 테이블과 Dimension 테이블, Externala Table</summary>

[ Fact 테이블 ]   
- 큰 테이블로, 분석 초점이 되는 양적 정보를 포함한 중앙 테이블임
- Fact 테이블의 외래키를 통해 여러 Dimension 테이블과 연결됨
- 보통 s3에 저장해두고, 외부에서 연결해서 작업하는 형태로 감
- e.g. 매출수익,판매량, 이익같은 항목 포함함, user_session_channel, order 테이블

[ Dimension 테이블 ]
- 비교적 작은 테이블로, Fact 테이블에 대한 상세 정보를 제공하는 테이블임
- e.g. 고객, 제품 같은 테이블, user,channel, Product 테이블

[ External(외부) 테이블 ]
- 임시 목적으로 데이터베이스 엔진이 __외부에 저장된 데이터__ 를 마치 내부 테이블처럼 '읽기 전용'으로 사용하는 방법
- 외부테이블은 보안 및 네트워크 성능문제를 고려해야 됨

</details>

<details><summary> ✔ AWS Glue</summary>

>  AWS의 Serverless ETL 서비스

- 데이터 카탈로그 기능 : data destination(e.g warehouse)와 source(e.g. s3)의 메타데이터를 대상으로 검색 기능 제공함.
    -  Redshift Spectrum의 경우 외부테이블들을 AWS Glue 저장함
- ETL 작업 : 드래그 앤 드롭으로 ETL 작업 가능 
- 작업 모니터링 및 로그
- 서버리스 실행 : 서버리스 아키텍처로 Auto Scaling해줌 

</details>

<br>

### ✅ Athena
> AWS Preso 서비스로, Redshift Spectrum과 비슷하게 S3내의 데이터로 SQL 쿼리 가능

- 보통 S3는 데이터레이크 구조일 때임


### ✅ Redshift ML
> SQL로 Redshift의 데이터로 머신러닝 모델 훈련하고 사용 가능함

- AWS SageMaker에 의해 지원되며 (1) 이미 만든 모델을 적용하거나, (2) SageMaker의 `Auto Pilot` 기능으로 기계학습에 대해 최적화된 모델을 자동 생성해주는 기능을 사용할 수 있음


<br><br>


## #️⃣ Redshift 유지보수 및 중지/제거 
### ✅ Redshift유지보수 
고정비용 Redshift의 경우 주기적으로 버전 업그레이드를 위해 중단한다. (`Maintenance window`) 
그렇기에 해당 기간 미리 숙지하고 중요한 작업을 하지 않도록 한다.

### ✅ Vacuum 명령어
> 불필요한 데이터 청소 및 최적화
- `테이블 데이터 정렬` : 불규칙하게 분산된 데이터를, Vacuum명령어로 데이터 정렬하여 __남아있는 행을 모아 쿼리 실행시 검색해야 할 블록수를 줄일__ 수 있다.
- `디스크 공간 해제` : 데이터 행을 삭제하면 디스크 공간이 즉시 해제되지 않는데, Vacuum명령어로 __필요없는 행을 제거하고 사용한 디스크 공간 해제함__
- `삭제된 행에서 공간 회수`
- `테이블 통계 업데이트` : 해당 명령어로 테이블 통계 업데이트하여 `Query Planner`가 쿼리 최적화 지원함
- __주기적으로 실행해주는 것이 좋음__


### ✅ Redshit 중지/제거  (고정 비용만)
- `Pause` 시, 프로세스 비용 X 스토리지 비용만 O
    - `Resume`으로 재시작
- `Delete` 영구 삭제 :  .
    - 이때 s3로 백업할건지 여부 물어봄
    -  workgroup -> namespace 순으로 삭제하기


<br><br>

# 실습 
[GoogleDocs-8주차 3일차 과제](https://docs.google.com/document/d/1LrshdvMwtwKmxjmbUyCLgASRHVjv3iBg5IV2VVfMoAE/edit?usp=sharing)