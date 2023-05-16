# AWS Route 53
> 도메인을 관리해주는 서비스

가용성과 확장성이 뛰어난 DNS 웹 서비스
Route 53을 사용하여 도메인 등록, DNS 라우팅, 상태 확인을 조합하여 실행할 수 있다.

- public host zone과 private host zone으로 구분됨
    - public 호스팅 영역 : 인터넷에서 트래픽 라우팅 하는 방식 결정
    - private 호스팅 영역: Amazon VPC 내에서 트래픽을 라우팅 하는 방식 결정
- Route53 = DNS(네임서버) + 모니터링 + L4 + GSLB
    - 네임서버에 등록되어야 찾아갈 수 있음

<br>

## DNS 서버란 
도메인 주소를 IP주소로 변환해주는 역할을 함.
- 쿼리 : 사용자가 어떤 서버에 연결할 것인지 요청하는 것

### DNS 동작 과정

<p align="center">
<img src="https://d1.awsstatic.com/Route53/how-route-53-routes-traffic.8d313c7da075c3c7303aaef32e89b5d0b7885e7c.png" width="75%" height="75%"/>
</p>

1. end user가 도메인 요청  
2. 글로벌하게 네임 서버 찾아감
3. 지역적으로 네임 서버 찾아감
4. 로컬적으로 등록된 네임 서버 찾아감

- 도메인을 등록하는 것이 Route 53




## 실습
- 호스팅 영역에서 트래픽을 라우팅할 도메인 이름등을 지정하여 하위 도메인에 대한 트래픽을 라우팅 방식을 저장할 수 있음
- 도메인 등록은 외부 서비스 혹은 AWS를 이용해서도 등록 가능한데, 외부 서비스를 등록했을 때는 별도의 설정 필요함 