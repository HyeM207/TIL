# 🐳 도커 컨테이너 기본

도커 명령어는 [공식 독스](https://docs.docker.com/engine/reference/commandline/docker/)에 가면 자세히 기입되어 있다.   
그 중 자주 쓰이는 명령어를 [책 (시작하세요! 도커/쿠버네티스)](https://ebook-product.kyobobook.co.kr/dig/epd/ebook/E000002928535)을 참고하여 아래에 정리해보았다.   

<br>

## 명령어 정리

### 도커 엔진 버전 확인  
``` shell
docker -v
```
<br>

### 이미지 내려받기 및 조회 
#### 이미지 내려받기 
``` shell
docker pull [옵션] 이미지명[:TAG|@DIGEST]
```
- 도커 공식 이미지 저장소인 docker hub에서 이미지를 다운로드 함

<br>

#### 이미지 조회
``` shell
docker images
```
- 도커 엔진에 존재하는 이미지 목록 출력

<br>

#### 컨테이너 목록 확인
``` shell
docker ps
```
- docker ps 명령어는 정지되지 않은 컨테이너만 출력함
    - exit 로 나온 컨테이너는 정지 상태라 목록에 출력되지 않음
    - ctrl+P, Q를 입력해 나온 컨테이너는 목록에 출력됨   
- 모든 컨테이너 출력하려면 `-a` 옵션을 추가함
- STATUS : exited 는 정지된 상태, UP..seconds는 실행 중인 상태

<br>


### 컨테이너 생성 후 실행 
``` shell
docker run [옵션] 이미지명

ex. docker run -i -t ubuntu:18.04
```
- 로컬 도커 엔진에 해당 이미지가 없으면 도커 허브에서 자동으로 이미지를 내려 받음 (docker pull)
- 컨테이너 기본 사용자는 root이고 호스트 이름은 무작위 16진수 해시값
    - 무작위 16진수 해시값은 컨테이너 고유한 ID의 앞 일부분이다.
- 옵션 
    - `-i` : 상호 입출력 사용 
    - `-t` : tty 활성화하여 bash 쉘 사용하도록 설정

<br>

### 컨테이너 생성만
``` shell
docker create [OPTIONS] IMAGE [COMMAND] [ARG...]

ex. docker create -i -t --name mycentos centos:7
```
- 로컬 도커 엔진에 해당 이미지가 없으면 도커 허브에서 자동으로 이미지를 내려 받음 (docker pull)
- docker run 명령과 달리 이미지로부터 컨테이너 생성만하고, 컨테이너 내부로 들어가지 않음
    - 내부로 들어가기 위해서는 start 와 attach명령어를 입력한다. 
- 옵션
    - `--name` : 컨테이너 이름 설정 

<br>

>  #### docker run과 create 명령어 비교  
> <b>[ run 명령어 ]</b>  
    1. docker pull (이미지가 없을 때)  
    2. docker create  
    3. docker start  
    4. docker attach (-i -t 옵션을 사용했을 때)      
> <b>[ create 명령어 ]</b>  
    1. docker pull (이미지가 없을 때)  
    2. docker create 


<br>

### 컨테이너 시작 및 내부 진입
#### 컨테이너 시작
``` shell
docker start 컨테이너

ex. docker start mycentos
```
- 컨테이너는 컨테이너 이름 혹은 컨테이너 Id (앞의 2~3만 입력해도 가능) 입력

#### 컨테이너 내부 진입
``` shell
docker attach 컨테이너

ex. docker attach mycentos
```
<br>


### 컨테이너 나오기 및 정지
#### 컨테이너 정지 후 나오기
``` shell
exit 입력 혹은 Ctrl+D
```  
#### 컨테이너 정지 없이 단순히 쉘에서 나오기
``` shell
Q 입력 혹은 Ctrl+P
```

#### 컨테이너 정지
``` shell
docker stop 컨테이너 

ex. docker stop mycentos
```

<br>

### 컨테이너 삭제
한 번 삭제한 컨테이너는 복구 불가함
#### 컨테이너 하나 삭제
``` shell
docker rm 컨테이너

ex. docker rm mycentos
```
- 실행 중인 컨테이너는 삭제할 수 없고, 컨테이너를 정지한 뒤 삭제할 수 있다.
    - 강제로 삭제하려면 `-f`옵션을 추가한다.  

#### 컨테이너 모두 삭제
방법 1 : prune 명령어
``` shell
docker container prune
```

방법 2 : docker ps 의 -a와 -q 옵션
``` shell
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

<br>

### 컨테이너 외부에 노출 (포트포워딩)
컨테이너는 가상 IP 주소를 할당 받는데, 아무런 설정을 하지 않으면 외부에서 해당 컨테이너 접근할 수 없고, 오직 도커가 설치된 호스트에서만 접근할 수 있다.   
이를 외부에 노출하기 위해서는 <b>컨테이너의 eth0의 IP와 port를 호스트의 IP와 port에 바인딩</b> 해야한다.  

``` shell
docker run -p (호스트port):(게스트port) 이미지명

ex. docker run -i -t --name mywebserver -p 80:80 ubuntu:18:04
```
- docker run명령어에 -p 옵션을 추가하여, 호스트의 port와 게스트 port를 연결한다. 
    - 여러 개의 포트를 외부에 개방하려면 -p를 여러번 써서 설정함
    - 만약 `-p 80`로 입력하면 컨테이너 80 포트를 쓸 수 있는 호스트의 포트 중 하나와 연결함 

