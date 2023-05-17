# 📌 AWS Identity and Access Management(IAM)

AWS Identity and Access Management(IAM)은 **AWS 리소스에 대한 액세스를 안전하게 제어**할 수 있는 웹 서비스

- 리소스 사용하도록 인증(로그인) 및 권한 부여 된 대상 제어함
- 해당 자격 증명은 AWS 계정 root 사용자라고 하며, 계정 생성시 사용한 이메일 주소와 암호로 로그인하여 액세스 함
    - 일반 작업에는 루트 사용하지 않은 것을 권고함

### ✅ IAM 특징

- **AWS 계정** 공유
- 리소스마다 **권한을 세분화**하여 부여 가능
- EC2에서 실행되는 애플리케이션 위한 보안 AWS 리소스 액세스
- **MFA**(멀티 팩터 인증)
- **ID 페더레이션**
    - 용어 설명1: 페더레이션 아이덴티티는 서로 다른 아이덴티티 관리 시스템 사이에서 사용자의 아이덴티티를 연결하는 방법. 사용자는 페더레이션 아이덴티티를 통해 보안을 유지하면서 시스템을 빠르게 전환할 수 있음
    - 용어 설명2 : 사용자가 이미 회사 디렉터리와 같은 AWS 외부에 자격증명을 보유했을 때, 해당 사용자가 AWS 리소스를 사용해야할 경우, 해당 사용자에게 AWS 보안 자격 증명이 필요함.  
    그러면 자격 증명이 내 조직 또는 서드 파티 자격 증명 공급자(IdP)에서 페더레이션되는 사용자의 권한을 IAM 역할을 사용하여 지정가능함
- PCI DSS 준수
    -   PCI(Payment Card Industry) DSS(Data Security Standard)는 신용 카드 데이터에 대한 통제를 강화하여 사기를 방지하도록 고안된 글로벌 정보 보안 표준
- 많은 AWS 서비스와 통합
- 최종 일관성 제공
- 무료
<br>

## #️⃣ 정책

리소스에 접근 가능한 정도를 규정한 것 

- 사용자
- 사용자 그룹
- 역할

에 정책을 매칭함

<br>

## #️⃣실습

### ✅ 사용자 추가

1. **사용자 세부 정보 설정**
- 사용자 이름
- AWS 액세스 유형 선택
    - 액세스키
    - 암호 설정
1. **권한  설정** 
    - 그룹에 사용자 추가
    - 직접 정책 연결
    - 기존 사용자에서 권한 복사
2. 사용자 만들기 버튼 
    1. 만들면 **비밀 액세스키와 액세스 키 발급받음**

### ✅ 사용자 그룹 생성

- 그룹 이름 지정
- 필요한 정책 묶음

### ✅정책 생성

- JSON 혹은 UI에서 정책 선택하여 만들수 있음
- 만든 정책은 사용자나 사용자 그룹에서 가져다 사용할 수 있음


<br>

#### 참고
[okta-페더레이션 아이덴티티란 무엇인가요?](https://www.okta.com/kr/identity-101/what-is-federated-identity/)

[AWS-외부에서 인증된 사용자에게 액세스 권한 제공(ID 페더레이션)](https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/id_roles_common-scenarios_federated-users.html)    

[Microsoft-PCI(Payment Card Industry) DSS(Data Security Standard)](https://learn.microsoft.com/ko-kr/compliance/regulatory/offering-pci-dss)