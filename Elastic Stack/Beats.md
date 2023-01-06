# Beats
책 '엘라스틱 스택 개발부터 운영까지 - 김준영/정상운 지음'을 공부하며 정리하였습니다.  

- [Beats](#beats)
- [1. Beats](#1-beats)
- [2. Filebeat (파일 비츠)](#2-filebeat--------)
- [3. 모니터링](#3-----)

</br> 

# 1. Beats
> 소개 

Beats는 가볍고 사용하기 쉬운 `데이터 수집기` 이다. Go언어로 작성된 경량 프로그램이라 로그 수집을 원하는 시스템에 `큰 부담을 주지 않으며` Logstash와 Elasticsearch와 연계해 다양한 시스템의 이벤트를 수집할 수 있게 도와준다.  

Logstash와는 다르게 범용성을 포기하고 `특정 목적만 수행`하도록 가볍게 구성되어 애플리케이션 성능에 영향 미치지 않고 필요한 이벤트를 수집할 수 있다.

Beats는 목적별로 다양한 비츠가 존재한다. 엘라스틱에서 공식적으로 지원하는 파일비트, 메트릭비트, 패킷 비트 등과 공동체에서 만든 카프카비트, 엔진엑스 비트, MySQL 비트 등 `종류가 다양`하다. 
그 외 종류는 [공식 문서](https://www.elastic.co/kr/beats/)에서 확인할 수 있다.

일반적으로 Beats에서 수집한 데이터는 Elasticsearch로 바로 보내거나, Logstash를 거쳐 Elaticsearch로 보낼 수 있다.

<br>

> 동작 과정 

1. Beats 다운로드
2. Beats 설정 파일 수정 
3. Elasticsearch와 Kibana 대시보드를 사용할 수 있게 설정
4. Beats 시작
5. Kibana 대시보드에서 데이터 확인 

<br>

# 2. Filebeat (파일 비츠)
> 소개

파일 비츠는 가장 많이 사용되는 비트로, 로그 파일을 실시간으로 읽어 들여 전송하낟. Logstash나 Elasticsearch에 데이터 전달 시 부하 방지 기능을 지원한다. 

<br>

> 아키텍쳐

파일 비트는 기본적으로 파일에 적재되는 로그들을 가져오는 역할을 한다.
파일 비트는 크게 세 가지 주요 구성요소로 이뤄져있다. 
| 구성요소 | 내용 |
| --- | ----|
| `입력(input)` | 설정 파일에서 하베스터에 대한 입력 소스를 정함. 파일비트는 하나 혹은 여러개 입력 가질 수 있음|
| ` 하베스터 (harvester)` | 입력에 명신된 파일을 직접 수집하는 주체. 파일은 하나의 하베스터를 가지며, 하베스터는 파일을 한 줄씩 읽고 내보내는 역할을 함. 또한 파일을 열고 닫는 역할도 함. 하베스터가 실행되는 동안에는 파일 디스크립터가 열려 있음 |
| `스풀러 (spooler)` | 하베스터가 수집한 이벤트를 Elasticsearch나 Logstash 같은 장소로 전달함 |

<br>

> 파일비트 실행

파일비트 다운로드 후 로그 파일을 수집하기 위해서는 `filebeat.yml` 설정 파일을 수정해야한다.

```
filebeat.inputs:
- type : log
  enabled : true
  paths:
    - C:/elasticsearch-7.10.1/logs/*log

output.elasticsearch:
    hosts: ["localhost:9200"]

setup.kibana:
    hosts: "localhost:5601"
```
`input`은 파일 비트가 가져오는 입력이고, `output`은 내보내는 출력이다.

`input`의 type은 다음과 같다.  
| input type | 설명 |
| ---- | ---- |
| `log` | 기본 타입으로, 파일 시스템의 지정한 경로에서 로그 파일을 읽어들임 (paths에 지정된 파일의 로그를 한 줄씩 가져옴) |
| `container` | 도커 같은 컨테이너의 로그를 수집하기 입으로, 파일을 읽어 들인다는 점에서 log와 유사함|
| `s3` | log 타입과 유사하나, aws s3 버킷에 위치한 파일을 읽어들임 |
| `kafka` | 다른 타입과는 다르게 파일을 읽어 들이는 대신 kafka의 topic을 읽어들임 |

<br>
`output`의 type은 다음과 같다.  

| output type | 설명 |
| ---- | ---- |
| `elasticsearch` | 가장 많이 사용되는 타입으로, 수집한 이벤트를 elasticsearch로 직접 인덱싱함 |
| `logstash` | 다수의 비츠를 사용해 elasticsearch로 전송되는 인덱싱 리퀘스트 양이 많거나, beats나 인제스트 노드 수준에서 처리하기 어려운 가공 작업이 필요할 때, 별도이 logstash를 구축하여 수집한 이벤트를 전송함. 다수의 인덱싱요청이 Logstash에서 단일 벌크 리퀘스트로 묶여 인덱싱 효율의 개선을 기대할 수 있음. 이때 전송한 이벤트는 Logstash의 beats 인풋을 이용해 입력받을 수 있음 |
| `kafka` | 파일비츠에서 1차적으로 수집한 이벤트를 카프카로 저송함. 카프카는 안정적인 수집 파이프라인을 구성할 때 신뢰할 만한 중간 저장소/큐 이므로 수집 중 장애 발생 시 데이터 손실을 최소화하기 위한 방안으롱 활용됨|
| `console` | 수집한 이벤트를 시스템 콘솔에 출력함. 일반적으로 수집이 정상적으로 이뤄지는지 입력 설정을 테스트하기 위한 목적으로 사용됨 |

> 유용한 설정 
>> input 설정 
- `ignore_older` : input 타입이 log일 때 쓸 수 있는 옵션으로, 10h, 10m처럼 타임스트링 형식으로 작성한다. 10h의 경우 최근 10시간 전의 로그만 수집하겠다는 의미이다. (기본값 0)
- `include_lines` : 특정 라인을 정규 표현식으로 이용해 필터링하고 매칭된 라인만 비츠에서 수용함
- `exclude_lines` : 특정 라인을 정규 표현식으로 이용해 필터링하고 매칭된 라인은 비츠에서 수집하지 않음
- `exclude_files` : 패턴에 일치하는 파일을 무시함 

<br>  

>> 멀티라인 로그 처리


자바 오류 경우 여러 라인의 오류를 발생하므로, 파일비트는 멀티라인 옵션을 제공한다.  
input타입이 log인 경우 멀티라인 처리할 수 있다.
- multiline.pattern : 정규식을 이용해 패턴을 지정하고, 패턴과 일치하는 라인이 나타나면 멀티라인으로 인식함 
- multiline.megate : true이면 패턴 일치 조건을 반전시킴
- multiline.match : 멀티라인을 처리하는 방식으로 before와 after를 지정할 수 있음 

<br>  

> 모듈 
>> 소개

모듈은 많이 사용되고 잘 알려진 시스템 데이터를 수집하기 위한 일반적인 설정을 사전 정의해둔 것이다. nginx나 apache 서버 로그 수집 시 특별히 사용자 설정 변경이 없었다면 설치 경로나 로그 발생 위치, 로그 형태는 동일할 것이니, 모듈을 사용해 손쉽게 로그 수집할 수 있다.  
파일비트의 경우 aws, cef, cisco, elasticsearch, googlecloud, logstash 등의 모듈을 지원한다. 

<br>  


>> 동작 과정 

1. Beats 다운로드
2. `비트 설정 파일 수정`
    - 모듈을 사용하기 위해서는 `filebeat.yml`에서 `filebeat.config.modules`로 모듈 경로를 지정한다. 
3. `모듈을 활성화하고 모듈 설정 파일을 수정`
    - `filebeat.exe modules enable (모듈명)`으로 모듈 활성화한다.
    - 모듈은 파일비트 디렉토리 아래에 `modules.d`디렉토리에 모듈별로 파일이 존재하고, 비활성화된 모듈은 `disabled` 확장자가 붙어있고, 활성화된 모듈은 disabled 확장자가 빠진다. 
    - 모듈 파일(모듈명.yml)에 접근하여 모듈 설정을 수정할 수 있다.
4. Beats 설정 파일 수정 
5. Elasticsearch와 Kibana 대시보드를 사용할 수 있게 설정
6. Beats 시작
7. Kibana 대시보드에서 데이터 확인 

<br><br>

# 3. 모니터링
> 소개

모니터링은 비츠가 데이터 입출력을 잘하고 있는지, 비츠가 동작 중인 호스트의 리소스들은 문제없는지 등을 엘라스틱서치와 키바나를 통해 확인할 수 있는 기능이다. 

`filebeat.yml` 설정 파일에서 모니터링 설정을 추가하면 된다.
```
monitoring.enbles: true
# monitoring.cluster_uuid: PRODUCTION_EX_CLUSTER_UUID
monitoring.elasticsearch:
    hosts: ["localhost:9200"]
```

`monitoring.cluster_uuid`는 모니터링 클러스터 주소를 적는다. 모니터링 클러스터와 파일비트의 아웃풋으로 설정하는 엘라스틱 클러스터가 동일하면 설정할 필요는 없다.  
일반적으로 데이터 연산을 하는 엘라스틱서치 클러스터와 관리 용도로 모니터링하는 클러스터를 분리해서 운영하는 경우가 많으니, 모니터링 클러스터의 UUID를 적어야한다.