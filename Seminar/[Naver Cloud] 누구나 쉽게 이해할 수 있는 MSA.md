# 1편
## 1️⃣ 모놀리식 Vs MSA 
MSA란 각각 서비스가 나눠진 형태로 각 기능은 API와 연결됨
각각 서비스가 고유의 DB를 갖게됨
즉, 기능별로 나눠져있음

반면 모놀리식은 모든 소프트웨어 로직이 하나로 묶여있는 형태이다.

<br><br>

## 2️⃣ MSA 아키텍처가 인기 있는 이유
모놀리식 문제점 : 모든 서비스가 한 번에 관리되기 때문에, 추가 및 기능 추가에 있어 유연성에 한계가 있음

모놀리식 문제점을 보완한 것이 MSA이기 때문

1. 기존 Monolithic 방식은 모든 구성 요소가 한 프로젝트에 합쳐져 있어, 큰 변화에 대한 대응이 어려우며, `새로운 기능 추가 및 업데이트에 어려움`이 있었음 

2. 여러 역할을 하는 시스템이 하나의 소프트웨어로 집합되어, `특정 부분에 문제가 발생시, 큰 장애로 이어질 수 있음`

3. 여러 역할을 하는 시스템이 하나의 서버에 함께 올라가 있어, `Scale-Out시 필요 없는 자원이 함께 증가됨`

4. 민첩하고 손쉬운 배포 및 업데이트 가능
    - 모놀리식은 잘못된 부분 수정 후 다른 바이너리 파일을 만들어 재배포하지만, <b>MSA는 잘못된 기능이 올라가 있는 부분만 수정후, 컨테이너화 하여 해당 부분만 재배포함 </b>

<br><br>

## 3️⃣ MSA 아키텍처 구성을 위해 어떤 기술과 구성요소를 알아야할까
### (1) MSA를 구성하는 주요 Component
1. Config Management
    - 서비스 재빌드/재부팅 없이 설정사항 반영
    - 환경 변수를 별도의 객체로 관리할 수 있는것
2. Service Discovery
    - MSA 기반 서비스 배포 시 서비스 검색 및 등록 
3. API Management
    - 클라이언트 접근 요청을 일원화
4. Centralized Logging
    - 서비스별 로그의 중앙집중화
    - 중앙화된 서비스 별 로그
5. Distributed Tracing
    - 마이크로서비스 간의 호출 추적
6. Centralized Monitoring
    - 서비스별 메트릭 정보의 중앙집중화
7. Resilience & Fault Tolerance
    -  서비스 장애가 전체적으로 퍼지지 않도록 하는 계단식 실패 방지 구조 
8. Auto-scaling & Self-Healing
    - 자동화 된 관리 (자동 스케일링), 복구 자동화를 통한 서비스 관리 효율화 

<br>

### (2) MSA를 구현하는 기반 기술 
Spring Cloud와 Kubernetes로 나뉨 
- Spring Cloud는 JAVA 언어를 잘 알고 있으면 좋음 
- Kubernetes 는 오픈소스라 쉽게 사용가능. Naver Cloud도 그렇고 관리형 서비스로 제공하다보니 손쉽게 구축할 수 있다. 


쿠버네티스로 할 경우 `Service Mesh`를 알고 있으면 좋음
- `Service Mesh` : 마이크로 서비스 간 통신을 위해 각 서비스를 식별하고, 경로를 파악하고, 로드밸런싱을 하고 전체 서비스의 장애 전파를 차단하며 Telemetry와 통합되어 로깅, 모니터링, 트레이싱 기능을 담당함 (즉 서비스 간 통신을 추상화하여 안전하고 빠르고 신뢰할 수 있게 만드는 아키텍처) 
- Service Mesh 사용 시, MSA내부의통신을 다 다룰 필요가 없고, Service Mesh가 프록시 인스턴스 형태로 실행되기 때문에, 개발자가 통신에 큰 집중 없이 서비스 코드 개발에만 집중할 수 있다는 장점이 있음 

<br>

### (3) Service Mesh 서비스들 
ISTIO가 가장 대표적  
그 외 : LINKERD2, CONSUL, Traefik messh 등

<br>

### (4) MSA 도입한 기업들 활용사례
MSA 도입한 기업들 활용사례 살펴보면 좋음  
대표적으로는 NETFLIX   
: 테크 블로그 있으니 살펴보기   

국내는 배민, 11번가에서도 적용함   
: 초반에는 모놀리식이었음  

> 절대적으로 써야되는 기술은 없고, 회사별로 서비스 형태와 프레임워크를 보고 결정하는 것이 좋음 

<br><br>

## 4️⃣ MSA를 구축하며 직면할 수 있는 어려움은?
> "Don't even consider microservices unless you have a system that's too complex to manage as a monolith."

너무 복잡한 서비스가 아닌 이상 모놀리식이 맞다. 
무작정 MSA를 도입하는것은 아니고, 회사 서비스 규모에 맞게 도입해야됨

<br>

### (1) Service Mesh 적용 시 고려사항 
시스템이 복잡할 때만 생산성이 높다
1. 복잡성
    - service mesh 사용 시 런타임 인스턴스 수가 증가함
2. 사이드카 컨테이너 수 증가
    - 각 서비스는 서비스 매쉬의 사이드카 프록시를 통해 호출되므로 개별 프록시 수가 증가함. 이에 따른 부하로 서비스 운영에 문제가 발생할 가능성 있는 지에 대해 아키텍처 구조 측면에서 사전 검토 필요
3. 기술력의 미성숙
    - 빠르게 발전하고 있으나 아직은 새롭고 미성숙한 기술에 속함. 또한 많은 기업이 서비스 매쉬에 대한 경험이 없음

<br>


## 5️⃣ 네이버 클라우드 플랫폼으로 MSA를 구성한다면?
네이터 클라우드 플랫폼 Kubernetes Services는 하나 혹은 그 이상의 컨테이너로 구성되 Pod를 통해 각 서비스를 Pod형태로 구성하고 서비스 매쉬는 ISTIO를 통해 사용하여 마이크로서비스 구성 가능함




