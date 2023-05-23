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

<Br>

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

#### ✔ 번외: Snowflake, BigQuery
Snowflake, BigQuery는 Redshift과 달리 가변 비용 옵션으로 진행된다.
- 특별히 용량이 정해져 있지 않고 쿼리를 처리하기 위해 사용한 리소스에 해당하는 비용을 지불함 
    - __Scalable하다는 장점이 있지만, 비용 예측이 어렵다는 단점 존재함__
- Redshift도 가변비용 옵션인 Redshift Serverless가 존재한다. (Pay as You Go)

<br>

## #️⃣ Redshfit 레코드 분배와 저장 (최적화)
데이터는 분산 저장되어야 함.  
만약 Redshift가 두 대 이상의 노드로 구성되면 한 테이블의 __레코드들의 저장방식을 개발자가 직접 지정__ 해줘야한다.
    - 이는 알아서 저장해주는 Snowflake, BigQuery와는 차이가 있음 


즉, __한 테이블의 레코드들을 어떻게 다수의 노드로 분배할 것이냐의 테이블 최적화가 중요하다,__

### ✅ Diststyle, Distkey, SortKey
위의 3개의 키워드로 __특정 테이블의 레코드 분배방식__ 을 설정함.
- `Diststyle` : 레코드 분배 방식 결정 
    - `all` : 모든 노드들에 모든 레코드가 분배되도록 함
    - `even` : 라운드로빈 형태로 노드에 들어감
    - `key` : 특정컬럼의 값을 기준으로 레코드들의 다수의 노드로 분배됨
        - 주로 join이나 groupby할 때 사용함
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

#### ✔ 번외: Diststyle이 key일 경우의 유의점
Diststyle이 key인 경우 컬럼 선택이 잘못되면, 레코드 분포에 `Skew`발생함 -> 데이터 불균형 -> __분산처리의 효율성 사라짐__ 
-  BigQuery나 Snowflake에서는 이런 속성을 개발자가 지정할 필요가 없음 (시스템이 알아서 선택해줌)


<br>

## #️⃣ Redshfit 벌크 업데이트 방식
`벌크 업데이트(Bulk Update)`는 데이터베이스나 시스템에서 __대량의 데이터를 한 번에 업데이트__ 하는 작업을 의미합니다. 일반적으로 한 번에 여러 개의 레코드나 항목을 수정, 추가 또는 삭제하는 작업을 벌크 업데이트라고 한다.

1. Data Source로부터 데이터를 추출함
2. S3에 업로드
    - 업로드 시, 압축률이 좋은 parquet이나 바이너리 형태가 좋음
3. `COPY SQL`로 S3에서 Redshift 테이블로 한 번에
복사

<br>

## #️⃣ Redshfit의 기본/고급 데이터 타입

[ 기본 타입 ] 
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

[ 고급 타입 ]
- GEOMETRY
- GEOGRAPHY
- HLLSKETCH
- SUPER

<br>

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
- 폴더 구조
    - raw_data : ETL로 가져온 데이터 적재
    - analytics : 데이터 애널리스티가 주로 관리, raw_data에 있는 데이터를 요약하여 해당 폴더에 저장 및 대시보드 만듦
    - adhoc : 개발자나 데이터 종사자들이 테스트 용도로 사용
    - pii : 개인정보 들어감
        - 권한있는 사람만 접근 가능하도록 하며, 권한 가진 사용자 수를 최소한으로 설정  
