
# [Git, Github 익히기] 소프트웨어 개발 과정 소개 
> 프로그래머스 데이터 엔지니어 1기 - Git, Github 익히기 특강 내용 요약
> 일자 : 2023.04.26

<br>

# 📌 소프트웨어 플랜 방법 
## #️⃣ 소프트웨어 개발 어려움 
- 요구 조건 계속 변화함
- water-fall 모델은 소프트웨어 개발에 부적합
    - 요구 조건 > 디자인 > 개발> 테스트 > 릴리스 > 유지보수 
    - 테스트가 중간 단계에 있어서 불편함이 있고, 한 사이클을 돌며 개발하는 과정이 별로였음 
    - 애자일 개발 방법론 등장!

- 애자일 방법론 (Agile Development)
    - 짧은 사이클이 특징 (보통 2주)
        - 이를 보통 `스프린트(Sprint)` 라고 함 
    - 매 사이클마다 바로 쓸 수 있는 기능 구현 

<br>

## #️⃣ 애자일 개발 
- 매 사이클 마다 다음 작업 반복 
> backlog -> todo -> in progress -> testing -> done (화이트보드에 정리)


- backlog grooming은 팀 내에서 시니어나 매니저가 기본 과정 외로 진행함 
    1. 작업별 우선 순위 결정 (Backlog Priortization)(PM이 미리 수행해둠)
    2. Planning : Backlog에 있는 task를 할 수 있을 만큼 todo에 옮기는 것
    3. 마지막 날 Retrospective & Demo 미팅
        - Demo 미팅  
            - 어떤 걸 했는지 자랑하는 타임
            - visualize 된 형태로 보여줌
        -  Retrospective 회고 미팅
            - 아쉬웠던 점, 좋았던 점 이야기 
- Stand-up 업데이트를 매일 정해신 시간에 각자 2-3분씩 이야기함   
    - 지난 24시간 동안 한 거, 앞으로 24시간 동안 할일, 막힌 부분 3가지를 팀원 별로 짧게 이야기함  

<br>

### ✅ 스프린트 카드 예제
> 어디까지 했고 끝났을 때 성공의 정의나 체크리스트가 있으면 좋음   
아래 2개는 backlog를 만들 때 이거까지 만들게 되어 있음

- 타이틀 (작업 타이틀)
- 세부 설명
- 포인트 (숫자)
    - 회사/팀마다 다르고, 매기기 힘든 부분
    - e.g. 중요도, 시간 등 
- 성공의 정의 
    - Definition of Done
- 체크리스트 
    - 이 작업이 성공적으로 끝나는 데 필요한 세부 작업


 

<br>


### ✅ 플래닝 포커
> 포인트를 매기는 방법 

- 작업별 일정 산정 기법
    - 작업별로 포인트를 산정 
    - 포인트 정의는 회사마다 다름
- 개발자 모두 모여 작업별로 들어가는 일정 투표함
    - 포인트 카드를 드는 방식
    - 숙련된 팀은 포인트가 비슷함

<br>


### ✅ 일일 스탠드업(데일리 스탠드업/ 스크럼)
- 매일 모든 팀원이 10-15분 모여각자상황 공유  
    __[ 각자 2-3분씩 아래 내용 공유 ]__
    - 마지막 스탠드 업 이후로 한 작업들
    - 오늘 하려고 하는 일이나 진행중인 일
    - 일할때 문제 있거나 도움 필요하면 언급

<br>


### ✅ 요즘 쓰는 툴 

요즘엔 화이트보드보단 온라인 툴인 JIRA나 Trello 있음 
JIRA는 기능 많고, Trello는 좀 더 직관적임 
- trello 추천 
    - 팀장이 프로젝트 새로 만들고, 탬플릿으로 보드 만들기

<br>


## #️⃣ 소스코드 관리 툴
- Git/Github
    - 가장 인기있는 버전 컨트롤 소프트웨어

<br>

## #️⃣  TDD (Test Driven Development)
- 개발시 테스트코드 부터 작성 
    - 이걸 하면 테스트 하기 편한 코드로 작성하게 됨 
    - 그렇기에 테스트를 하기 쉬운 형태로 작성할 수 있게됨

<br><br>

# 📌 How to build 및 기타
## #️⃣ Build 
- 개발한 소프트웨어를 배포하기 위한 것
    - 안정성이 중요하므로 테스트가 빌드의 중요한 일부임
- `Continuous Integration` : 
    - 커밋하는 순간 바로 모든 테스트를 돌림 -> 소프트웨어 안정성 증대
- 이러기 위해선 __Master하나로만 코드 Repo를 유지하고 나머지는 branch로 개발진행__
    - 이러면 코드 리뷰 요청 -> merge되고 test되면 Good
    - test coverage가 중요해짐 
    - 빌드를 계속적으로 수행(자동화)
        - 빌드는 최소한의 시간으로 쓰게 하고, 주기적으로 테스트 모아서 한번에 하는 2가지 종류가 있음 (commit build VS nightly build)

<br>

## #️⃣  Jenkins
- 오픈소스 CI 빌드 소프트웨어 
- 빌드를 이걸로 많이 씀
<br>

## #️⃣  Github 과 연동되는 CI 서비스
> 젠킨스 안쓰고 아래 쓰면 됨 

> public repo는 무료

- github actions 
- travis ci
- circle ci

<br>


## #️⃣ Slack 
> 사내 업무 메신저의 대두 
- 업무 자동화 툴로 쓰기 좋음 
- CI과정을 슬랙과 연동해볼것임 (예정)

<br><br>

# 📌 실습 
1. Repo 생성하기
    - public
    - name : e.g. github_lession
    - gitignore -> python 설정 
    - readme 설정 

2. collaborator 추가
    - setting> collaborator > 사용자 추가
3. pull request 
    - 작성한 두개의 코드를 upload file
    - main branch말고 new branch 로 하기 
        - (e.g. 'first-checkin')
    - pull request 
        - (e.g. pull request 'bery fist check-in' )
    - reviewrs로 collaborator 추가하기
    - description이 매우 중요 
        - 내일 설명 주실 내용(필수 사항 들 ) 
    - create pull request 누르기 
4. 코드 리뷰   
    - 코드 리뷰들은 file changes 탭가서 확인하면 됨 
         - `+` 눌러서 바꾸기 
            - start a review 누르면 end 전까지 append 됨. single은 바로 보내짐
         - approve는 승인 고치는 건 request 

<br><br>

# 📌  앞으로 일정 
> 4/27 깃허브 소개와 깃허브 실습

> 4/28 테스트 추가와 CI/슬랙 연동 