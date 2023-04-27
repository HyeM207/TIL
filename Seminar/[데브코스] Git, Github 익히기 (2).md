# [특강] Git/Gihub 익히기
> 프로그래머스 데이터 엔지니어 1기 - 좋은 코드 작성하기 특강 내용 요약
> 일자 : 2023.04.20

# [1] Git/Gihub
- 태스트 전에 스냅샵을 잡아서 테스트 함
-  오늘의 목표 
    - clone, init, commit, push, pull, merge, branch, checkout
    - 고급기능 rebase, cheery-pick, reset, revert, git-flor
- Git 사용 방법 : 클라우드 (Github, Gitlab)
- 분산 개발 : Github

<br>

# [2] 용어
- `Repo` : Repository의 줄인말로 Git으로 관리되는 소프틍뒈어 프로젝트 지칭
- `Master/Main` : 한 repo에서 기본이 되는 메인 코드 지칭 
- `Branch` : 자신의 Repo에서 새로운 기능을 개발하기위해 Master나 다른 branch로 부터 코드 작업본을 copy해서 새로운 branch를 만들고, 나중에 원본 branch와 병합함
- `Clone` : 다른 계정에 존재하는 repo를 복사해서, 내 local repo를 만드는 것
    - 오픈 소스 프로젝트를 내맘대로 쓸 수 있는 경우 clone하여, 내 local에서 작업을 하다가 내 계정으로 upload가능
- `fork` : clone과 비슷한 기능으로, 다른 계정의 원격 저장소 repo를 내 계정으로 가져올 때 사용함
- `commit(check-in)` : 내가 만든 코드 변경을 branch의 local repo에 반영함 
    - push, merge는 remote인 중앙 저장소에 sync하는 명령어 

<br>

# [3] Push&Pull

크게 `Local Repository`와 `Remote Repository`가 있음 
- Remote repo가 main/master 
- 혼자 개발하면 branch 팔 필요없음
- 여러명 작업하면 branch 라는 copy를 하고 local에서 개발하고 push하면 됨
    - pull : Remote repo에서 local repo로 가져오는 것
        - 하루에 한 번 정도는 pull해두기
    - push : 내가 작업 중인 local repo를 서버 remote repo에 반영함
        - 모든 변경사항을 한번에 push하지 말고 가능한 한 하루에 한번 정도는 push하기 
    - merge : 
        - 만약 두 사람이 동일한 코드 라인을 건들면 충돌 Conflict 발생함 
        - merge는 해당 confict를 해결하는 것

<br>

# [4] 전체 플로우 
`git server` : master/main과 이를 복사한 remote branch가 있음
- remote branch는 local에서 push했을때  충돌났을때 해결하고 정상적인걸 master/main에 push함    

`git client` : local branch에 local 작업 상황을 반영함
- local branch는 server의 `remote branch`와 연결됨

<br>

# [5] 브랜치 작업 명령어  
- `git checkout -b 브랜치 이름`, `git branch 브랜치 이름` : 새 branch 생성   
- `git checkout 브랜치 이름` : 해당 브랜치로 작업 공간 이동 
- `git commit -m "메세지" 파일 이름` : branch에서 코드 변경 후 커밋 

- `git push -u origin 브랜치 이름` : 영희ㅣ의 branch의 변경을 서버로 반영
    - origin : remote
Pull Request  master의 merge를 위한 리뷰 요청 
    - 깃헙 ui에서 하는 걸 추천 !!!
    - 질문하거나 minor의 경우로 comment로 보통 하다가 approve나 request changes 함

<br>

# [6] 좋은 PR 포맷
- __주요 변경 사항__ 
    - 간단한 설명
- __링크__ 
    - Trello나 Jira 링크공유 (회사에선 거의 기본)
- __시급한 정도 (데드라인 적어주면 좋음)__
    - 보통 : 최대한 빠르게 리뷰 부탁 
    - 긴급 : 선 approve 후 리뷰 부탁드립니다 (리뷰는 추후 반영)
    - 천천히 : 급하지 않음 
- __중점적으로 봐주었으면 하는 부분__
    - 변경사항이 큰 경우 집중할 부분, 혹은 불안해서 봐줘야하는 부분 

<br>

# [7] Pull Request 
__서버 remote repo 관점__ 에서의 Pull request임
- 내가 만든 코드 변경을 remote repo로 pull 해달라는 것임
- 이 과정에서 코드 리뷰가 들어감