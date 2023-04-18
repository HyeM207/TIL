# 📌 [1] 이론
## #️⃣ 인터넷과 웹
> `컴퓨터`  > `네트워크(Network)` > Network 묶은 `LAN`(Local Area Network; 근거리 지역 네트워크) > `인터넷(Internet)`

- `인터넷(Internet)` : 여러 컴퓨터끼리 네트워크 연결
- `WWW(World Wide Web;Web)` : 인터넷에서 정보를 교환하기 위한 시스템

<br>

## #️⃣웹에서 정보 주고받기
- `클라이언트(Client)` : 정보 요청하는 컴퓨터
- `서버(Server)` : 정보를 제공하는 컴퓨터

<br>

## #️⃣ HTTP
### (1) Request와 Response
> HTTP  (Hypertext Transfer Protocol)
웹 상에서 정보를 주고 받기 위한 약속
- HTTP 요청(`Request`) : 클라이언트 -> 서버에게 정보 요청
- HTTP 응답(`Response`) : 요청된 정보에 대해 서버 -> 클라이언트에게 정보 응답

### (2) 구조
HTTP는 요청/응답에 대한 정보를 담은 `Head`와 내용물인 `Body`로 나뉨

- Head : method, path, Host, User-Agent, 응답 상태 등 요청 정보 및 응답 정보 담김
- Body : 요청하는내용 혹은 응답 내용이 담김


<br>

## #️⃣ 웹 브라우저의 역할 
1. HTML 요청을 보냄
2. HTTP 응답에 담긴 HTML 문서를 시각화하여 화면을 그려주는 역할을 담당함  

즉, 웹페이지는 HTML로 되어 있고, 웹 브라우저는 __HTTP 요청을 보내고, 응답받은 HTML코드를 렌더링 해줌__  


<br><br>

# 📌 [2] 실습 
## Python으로_HTTP_통신해보기
[Python으로_HTTP_통신해보기](https://github.com/HyeM207/TIL/blob/main/HTML%2CWEB/Python%EC%9C%BC%EB%A1%9C_HTTP_%ED%86%B5%EC%8B%A0%ED%95%B4%EB%B3%B4%EA%B8%B0.ipynb)