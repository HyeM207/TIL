# 📌 Git Commit Message Format Template

협업 시 일관된 commit message 작성을 위해 템플릿을 이용할 수 있다.  
commit message format은 가장 많이 이용되는 [AngularJS commit message format](https://github.com/angular/angular/blob/main/CONTRIBUTING.md#commit)에 맞춰 작성한다.


<br>

## #️⃣ 템플릿 적용 방법 
### ✅ 1. 프로젝트 적용 
1. 모든 프로젝트를 global로 적용하는 경우, __사용자 HOME 디렉토리__ 에 생성한다. 
```shell
touch ~/.gitmessage.txt
```

2. 현재 프로젝트에만 적용하는 경우, __프로젝트 루트 경로__ 에 생성한다.
```shell
touch .gitmessage.txt
```

### ✅ 2. 템플릿 작성 
위에서 생성한 `gitmessage.txt`의 내용을 작성합니다.  
이때 현재 repository에 있는 `.gitmessage.txt` 파일 내용 혹은 아래 코드를 복사하여 붙여넣습니다. 
<details><summary>gitmessage.txt</summary>

```txt
################
# <타입> : <제목> 의 형식으로 제목을 아래 공백줄에 작성 (필수)
# 제목은 50자 이내로 아랫줄에 영문 명령문으로 작성(첫글자 대문자) / 변경사항이 "무엇"인지 명확하게 작성 / 끝에 마침표 금지
# (예시) feat : login

# 바로 아래 공백은 지우지 마세요 (제목과 본문의 분리를 위함)

################
# 본문(구체적인 내용)을 아랫줄에 작성 (선택사항)
# 여러 줄의 메시지를 작성할 땐 "-"로 구분 (한 줄은 72자 이내)

################
# 꼬릿말(footer)을 아랫줄에 작성 (선택사항) (현재 commit과 관련된 이슈 번호 등 추가)
# (예시) Close #5

################
# <타입 목록>
# feat : 새로운 기능 추가
# fix : 버그 수정
# docs : 문서화 및 문서 수정
# style : 코드 의미에 영향을 주지 않는 변경사항 (코드 포맷팅, 세미콜론 누락 등)
# refactor : 코드 리팩토링
# test : 테스트 코드 추가 및 수정
# chore : 빌드 부분 혹은 패키지 매니저 수정사항 (유지보수 및 그 외 수정사항)
# build : 빌드나, 외부 dependency에 영향 준 변경사항 (ex. npm, gulp 등)
################
```
</details>


<br>

### ✅ 3. git config를 template으로 설정하기
1. 모든 프로젝트를 global로 적용하는 경우
```shell
git config --global commit.template ~/.gitmessage.txt
```

2. 현재 프로젝트에만 적용하는 경우
```shell
git config commit.template .gitmessage.txt
```

### ✅ 4. (활용) commit message 작성
위 과정에서 git commit message를 config로 등록해주어, 템플릿 내용을 바탕을 commit message를 작성할 수 있다.  

1. 터미널(git bash) 이용하는 경우  
    - `git add` 다음에 `git commit` 명령어를 입력하면 vi 편집기가 열리면서 커밋 메시지 템플릿을 확인할 수 있다. 
    - 템플릿의 맨 앞에 `#` 있는 부분은 주석 부분이니 이를 제외하고, 빈 줄에 주어진 템플릿에 따라 commit message를 작성한다.
2. VSCode 이용시 
    - add 한 후 'commit'버튼을 클릭하면 `COMMIT_EDITMSG`창이 열리며 commit message를 편집할 수 있다. 


vi 편집시에는 `i`를 눌러 편집 모드를 활성화하여 commit message를 작성가능하다. 작성 후에는 `:wq`를 눌러 커밋을 저장한다. 

<details><summary>작성 예시 </summary>

template의 가이드라인대로 `'아랫줄에 작성'` 문구의 비어있는 아랫 줄에 해당하는 내용을 작성한다.
```
################
# <타입> : <제목> 의 형식으로 제목을 아래 공백줄에 작성  (필수)
# 제목은 50자 이내로 아랫줄에 영문 명령문으로 작성(첫글자 대문자) / 변경사항이 "무엇"인지 명확하게 작성 / 끝에 마침표 금지
# (예시) feat : login
feat: onUrlChange event 
# 바로 아래 공백은 지우지 마세요 (제목과 본문의 분리를 위함)

################
# 본문(구체적인 내용)을 아랫줄에 작성  (선택사항)
# 여러 줄의 메시지를 작성할 땐 "-"로 구분 (한 줄은 72자 이내)
- forward popstate event if available
- forward hashchange event if popstate not available
- do polling when neither popstate nor hashchange available

################
# 꼬릿말(footer)을 아랫줄에 작성 (선택사항) (현재 commit과 관련된 이슈 번호 등 추가)
# (예시) Close #5
Closes #392
################
# <타입 목록>
# feat : 새로운 기능 추가
# fix : 버그 수정
# docs : 문서화 및 문서 수정
# style : 코드 의미에 영향을 주지 않는 변경사항 (코드 포맷팅, 세미콜론 누락 등)
# refactor : 코드 리팩토링
# test : 테스트 코드 추가 및 수정
# chore : 빌드 부분 혹은 패키지 매니저 수정사항 (유지보수 및 그 외 수정사항)
# build : 빌드나, 외부 dependency에 영향 준 변경사항 (ex. npm, gulp 등)
################
```
</details>

<br>