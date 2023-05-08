# AWS Redshift
**Scalable한 SQL 엔진**
- 최대 2PB까지 지원 
- `데이터 웨어하우스`. still OLAP
    - 응답속도가 빠르지 않아 프로덕션 데이터베이스로 사용불가
- `Columnar storage`
    - 컬럼별로 저장함하여 컬럼별 압축 가능
    - 컬럼을 추가하거나 삭제하는 것이 아주 빠름

- `벌크 업데이트 지원`
    - 다른 BigQuery, Snowflake도 지원하는 기능
    - Redshift의 경우 레코드가 들어있는 파일을 S3로 복사후 `COPY`커맨드로 Redshift로 일괄 복사
- `고정 용량/비용` SQL 엔진
    - BigQuery, Snowflake는 `가변용량/비용`이라 비용 예측이 어렵지만, 확장성 좋음
- `primary key uniqueness`를 보장하지 않음
    - 다른 데이터 웨어하우스도 동일
    - 프로덕션 데이터베이스들은 보장함
- Postgressql 8.x이 호환됨
    - 모든 기능을 지원하지는 않지만, Postgressql 8.x을 지원하는 툴이나 라이브러리로 액세스 가능(JDBC/ODBC)
    - SQL이 메인 언어

<Br>

## 비용 
- 소규모인 경우(많은 데이터를 다루진 않을 때) : dc2.large, dc2.8xlarge를 사용
- 중간이상 규모 : ds2.xlarge, ds2.8xlarge 이용
- 대규모 : ra3.4xlarge, ra3.16xlarge

<br>

## Redshift Schema (폴더) 구성
```
            DEV
             |
    ----------------------
    |        |           |
raw_data    analytics   adhoc
``` 
- Redshift, PostgreSQL는 위의 폴더를 `스키마`라고 부름
- `CREATE SCHEMA (폴더명)`을 관리자 권한을 가진 사람이 만들 수 있음
- 폴더 구조
    - raw_data : ETL로 가져온 데이터 적재
    - analytics : 데이터 애널리스티가 주로 관리, raw_data에 있는 데이터를 요약하여 해당 폴더에 저장 및 대시보드 만듦
    - adhoc : 개발자나 데이터 종사자들이 테스트 용도로 사용