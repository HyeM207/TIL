# 📌 AWS Elastic Beanstalk

## #️⃣ Elastic Beanstalk이란?
> AWS에서 애플리케이션을 배포할 수 있도록 하는 개발자 중심의 서비스 

AWS 클라우드에서 __애플리케이션을 신속하게 배포하고 관리할 수 있는 서비스로, 별도의 인프라 설정 없이도 쉽게 사용 가능함__ 
- `Managed service` : 애플리케이션을 업로드만 하면 Elastic Beanstalk에서  __용량 프로비저닝, 로드 밸런싱, 조정, 애플리케이션 상태 모니터링에 대한 세부 정보를 자동으로 처리해줌__
-  Go, Java, .NET, Node.js, PHP, Python 및 Ruby에서 개발된 애플리케이션을 지원함
-  애플리케이션을 배포 시, Elastic Beanstalk가 선택된 지원 가능 플랫폼 버전을 구축하고 Amazon EC2 등의 AWS 리소스를 하나 이상 프로비저닝하여 애플리케이션을 실행함
- UI와 연결되어, 로그파일도 터미널 접속 없이 다운로드 받을 수 있음

<br>

## #️⃣ Elastic Beanstalk 컴포넌트 및 워크플로
> Elastic Beanstalk의 구성요소와 동작과정 

1. `Application`
    - collection of Elastic Beanstalk (environments, versions, configurations, ...)
    - Elastic Beanstalk는 각각의 애플리케이션을 구성함. 각 애플리케이션은 개별적인 환경을 가질 수 있음
2. `Application Version`
    - 애플리케이션 코드의 iteration
    - 쉽게 말해 애플리케이션 코드 버전 v1, v2 지정 가능함
3. `Environment`
    - 애플리케이션의 특정 버전 하나를 실행 중인 AWS 리소소의 colleciton (__오직 한 번에 하나의 애플리케이션 버전만 가능함__)
    - `Tiers` : : Web Server Environment Tier & Worker Environment Tier로 구성됨 
    - dev, test, prod 등 여러 개의 환경을 구성가능함


<p align="center">
<img src="https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/images/clearbox-flow-00.png" width="75%" height="75%"/>
</p>

<br><br>

## #️⃣ Web Server Environment Tier & Worker Environment Tier
### ✅ 1. Web Server Environment Tier

<p align="center"><img src="https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/images/aeb-architecture2.png" width="65%" height="65%"/></p>

우리가 기존에 알고 있는 아키텍쳐 구조로, ELB가 앞단에 있고, Auto Scailing Group이 뒤에 있어 트래픽을 분산시키는 구조이다.

### ✅ 2. Worker Environment Tier

<p align="center"><img src="https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/images/aeb-architecture_worker.png" width="65%" height="65%"/></p>

메세징 큐인 `SQS Queue`가 있고, EC2 인스턴스가 worker가 되는 형태이다.  
EC2는 큐로 부터 message를 pull하여 process하고, SQS messages의 수에 따라 스케일링 된다.  
<br>

## #️⃣ Elastic Beanstalk Deployment Modes

### ✅ 1. Single Instance 
개발 목적으로 좋음
- Elastic IP를 가진 EC2 인스턴스 하나를 기반으로 작동함
- RDS 데이터베이스도 사용가능

### ✅ 2. High Availability with Load Balancer 
Production 모드로 좋음
- 로드밸런서가 있어 다수의 EC@ 인스턴스에 부하를 분산할 수 있음.
- 이는 autoscaling group과 다수의 availability zone으로 관리됨
-  Master와 Standby로 구성된 `RDS multi AZ` 구성도 가능함

<Br>

## #️⃣ 실습 
블로그 : [[AWS-SAA] Elastic Beanstalk 구축 실습](https://hyem207.tistory.com/91)

추가 내용 :
- cd /var/app에 애플리케이션 소스코드 있음 
- /var/log 에 로그 파일  있음
    - web.stdout.log에 많음
    - 자체 로그는 eb-engine.log 
    - 로그를 aws console (UI)로도 보고 다운로드 가능함

<br><br>

#### 참고 자료 
[AWS Elastic Beanstalk란 무엇입니까?](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/Welcome.html)