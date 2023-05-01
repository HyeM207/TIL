# 📌 Git branch Rule
## #️⃣ 브랜치 종류
branch 종류를 크게 5개로 나눌 수 있다.

1. `Main branch`
    - 기본 브랜치로 사용하며, 언제나 배포 가능한 상태로 유지
2. `develop branch`
    - `main` 브랜치에서 분기한 __개발 브랜치__ 로, __새로운 기능 개발, 버그 수정__ 등의 작업을 진행
3. `feature branch`
    - `develop` 브랜치에서 분기한 __기능 개발 브랜치__ 
    - __각 개발자는 자신이 담당한 기능을 개발하기 위해 feature 브랜치를 만들어 작업합니다.__ 작업이 완료되면 develop 브랜치로 merge 함
    - 🌟 __Naming Convention__ : `git checkout -b feature/{function} develop`
        - ex. `git checkout -b feature/login develop`
4. `release branch` 
    - `develop` 브랜치에서 분기한 __배포 준비 브랜치__
    - QA, 테스트 등의 작업을 진행합니다. 이후 main 브랜치와 develop 브랜치로 merge함
    - 🌟 __Naming Convention__ : `release-RB` 혹은 `release-..`
        - ex. `release-1.8`
5. `hotfix branch` : 
    - main 브랜치에서 분기한 긴급 수정 브랜치
    - 버그 수정이나 보안 이슈 등이 발생했을 때 사용함. 이후 main 브랜치와 develop 브랜치로 merge함
      - 🌟 __Naming Convention__ : `hotfix-..`
        - ex. `hotfix-1.5.2`

<br>



## #️⃣ 주의 사항 🌟
> ! 개발 초기의 단계에서는 다음 규칙은 꼭 지켜주시길 바랍니다 !

- `Main`브랜치의 경우 배포 가능한 상태의 코드이므로, __바로 코드를 push하는 일이 없도록__ 한다.   
- 개발은 개발 브랜치인 `develop`을 중점으로, 각자 맡은 개발 파트를 이름으로 하는 새로운 `feature branch`를 만들어 작업한다. 
    - 작업 후에는 `pull request`를 통해 동료에게 코드리뷰를 요청한 후 완료되면 __해당 branch는 삭제__ 한다.
    - 코드 리뷰의 경우 필수 사항은 아니지만, 코드 퀄리티를 위해 하는 것을 지향함.

