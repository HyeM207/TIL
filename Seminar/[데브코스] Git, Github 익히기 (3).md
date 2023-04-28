# [특강] Git/Gihub 익히기
> 프로그래머스 데이터 엔지니어 1기 - Git, Github 익히기 특강 내용 요약  
> 일자 : 2023.04.28  

<br>

# 📌 실습에 사용된 명령어 정리 

### ✅ 새로운 Git 저장소(repository) 생성
로컬 프로젝트 폴더에서 git을 사용할 수 있도록 초기화함
```shell
git init
```

### ✅ Git add
- Git에 작업 변경 사항을 스테이징 영역에 추가 
```shell
git add (파일명)
```

### ✅ Git Commit  
commit은 __변경 사항을 저장소에 저장하는 명령어__ 이다. 
```shell
git commit -m "설명" (파일명)
```
### ✅ 원격 저장소(repo)와 연결 
- origin은 서버를 의미함 
```shell
git remote add origin https://github.com//계정명//특정repo.git
```

### ✅ branch 목록 
__로컬 저장소에 있는 모든 브랜치 목록__ 을 보여줌 
```shell
git branch
```

### ✅ branch 이름 변경
`m` 은 'move'의 약자로, 해당 옵션을 사용하면 __브랜치 이름을 변경__ 할 수 있다.
아래 예시 명령어는 현재 작업 중인 브랜치의 이름을 'main'으로 변경하는 명령어이다. 
```shell
git branch -M main
```

### ✅ Git push 
push는 Git에서 __로컬 저장소에서 변경된 내용을 원격 저장소에 업로드__ 하는 명령어이다. 
- `-u` : 원격 저장소와 로컬 브랜치를 연결함. 
- `git push -u origin main`는 'origin/main'브랜치가 추적되어, 다음에 `git push`명령어를 실행 할 때, 'git push'만으로 원격 저장소에 푸시할 수 있다. 
```shell
git push <REMOTE> <BRANCH>
git push -u origin main
```

### ✅ Git pull
__원격 저장소에서 변경된 내용을 로컬 저장소로 가져오는 명령어__
```shell
git pull
```
### ✅ 브랜치 생성 및 변경
아래 명령어 실행시, 지정된 브랜치로 전환된다.   
만약 `-b`옵션을 사용하면 새로운 브랜치를 만들면서 동시에 해당 브랜치로 전환 가능하다. 
```shell
git checkout <BRANCH 명>
```


### ✅ Git clone
원격 저장소의 내용을 복사하여 로컬 Git 저장소를 생성함
```shell
git clone https://github.com/(계정명)/(원격 저장소명).git
```

<br><br>

# 📌 실습 1 & 2

## #️⃣ 실습1 과정 
> 로컬에 repo만들고 변경 사항을 만들어 원격 저장소에 push하는 실습

1. 코드 hangman.py 만들기
2. repo 로컬에 만들기   
`git init`
3. 변경한 파일 중에 repo에 추가하고 싶은 것들 선택   
`git add (파일명)`
4. 선택한 파일 기능 설명과 함께 repo에 commit (로컬 repo)   
`git commit -m "설명" hangman.py` 
    - 커밋은 관련 있는 변경 파일들을 하나로 묶어서 사용 
        (버그 고치는데 이용한 파일 여러개면 한 번에 같이 묶어서 commit 함 )
    - 이는 팀원들이 나중에 어떤 파일들이 버그 고치는데 사용됐는지 파악 가능함   

5. Github 자기 계정에 비어있는 hangman repo 만들기 
6. 로컬 repo의 remote repo 위치 설정   
`git remote add origin https://github.com/계정명/hangman.git`
    - origin은 서버를 의미함 
7. 브랜치를 리모트로 푸기 전에 먼저 로컬 브랜치 이름을 main으로 변경 
    - git init으로 만들면 기본 브랜치 이름이 master임
```shell
git branch -M main
git push -u origin main
```
8. Github repo 확인


<br>


## #️⃣ Github 계정 인증 방법
처음 git 사용하면 pw입력하라고 하는데 아래 2가지 방법 중 하나를 선택하면 pw를 인증할 수 있다.

1. https를 사용한다면 personal access token
    - settings > developers settings  
2. ssh 를 사용한다면 ssh private/public key pair를 만들기  
    - 조금 더 설정은 복잡하지만 그뒤 사용법은 간단함 


강사님은 2번을 선호하시는데 1번이 간단함   



<br>

## #️⃣ 실습2 과정 
> 원격 저장소에서 clone하여 새 브랜치를 만든 후 내용 변경하고  push 하는 실습

1. 이미 있는 repo를 clone하기 
```shell
mkdir hangman_v2 
cd hangman_v2
git clone https://github.com/HyeM207/hangman.git
```
2. 작업용 브랜치 만들기
    - 우선 가장 최신 버전것을 가져오기 위해 git pull 하기
    - git branch 하면 현재 있는 모든 브랜치를 보여줌  
```shell
git pull
git branch
```
3. 작업용 브랜치로 이동    
`git checkout modify_greeting_message`
4. 파일 수정 
5. 변경된 파일을 해당 브랜치로 커밋   
    - 이미 있는 파일을 수정하는 경우에는 add 할 필요없이 commit 하면 됨 
` git commit -m "changed greeting message" hangman.py`
6. 브랜치를 Github 서버로 push  
`git push -u origin modify_greeting_message`