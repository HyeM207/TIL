# 📌 BeautifulSoup 다루기
## #️⃣ BeautifulSoup이란 
> Python의  HTML Parser 라이브러리


BeautifulSoup은 HTML 및 XML 문서를 파싱하고 분석하기 위한 Python 라이브러리이다. 

<br>

### ‼ 비슷한 툴들과 비교 
- `Selenium` : __브라우저 자동화 툴__ 로 클릭과 폼 제출 등 상호작용등의 동적 행위 가능함. 다만 다른 툴들에 비해 무겁다는 단점 존재함.
- `Scrapy` : 웹 스크래핑을 위한 Python 프레임워크로, 데이터 추출을 위한 고급 API 제공함. __대량의 데이터를 빠르고 효율적으로 스크랩할 수 있다는 강점을 지니고 실무에서도 많이 쓰임__ 
- `BeautifulSoup` : 속도 빠르고, 쉬움. 다만 동적 사이트를 크롤링하기엔 한계가 있음

__동적 웹 페이지__ 의 경우 렌더링과 데이터처리가 `비동기 처리`되어, HTML 로딩시 _데이터가 완전하지 않은 경우_ 가 발생할 수 있다.   
이때 `selenium`의 기능을 이용하여 임의의 시간을 지연 후, 데이터 처리가 끝난 후 정보를 가져올 수 있다. 

<br>

## #️⃣ BeautifulSoup 함수 
### ✅ 설치
```terminal
pip install bs4
```

### ✅ 기본 (soup 만들기)
- `BeautifulSoup(파싱할 html/xml의 text, "html.parser" 혹은 "xml)`
```python
from bs4 import BeautifulSoup
soup = BeautifulSoup(res.text, "html.parser")
```

### ✅ prettify()
- 분석된 HTML을 보기 편하게 __들여쓰기__ 하여 보여줌 

### ✅ HTML 특정 요소 가져오기
- ✔ title 가져오기
```python
soup.title
# <title>Example Domain</title>

soup.title.text
# Example Domain
```
- ✔ head 가져오기 
```python
soup.head
'''
<head>
<title>Example Domain</title>
<meta charset="utf-8"/>
<meta content="text/html; charset=utf-8" http-equiv="Content-type"/>
    ...
</head>
'''
```
- ✔ body 가져오기 
```python
<body>
...
</body>
```

- ✔ 태그 이름/내용 가져오기 
```python
# 태그 이름 가져오기
h1.name
# 'h1'

# 태그 내용 가져오기
h1.text
# 'Example Domain'
```


- ✔ __특정 태그 요소 하나__ 가져오기
    - (1) 기본 
    - (2) id로 찾기
    - (3) class로 찾기
        - class는 키 지정 안 하고 디폴트로 바로 찾을 수 있음
```python
# (1) 기본 
h1 = soup.find("h1")
'''<h1>Example Domain</h1>'''

# (2) id로 찾기 
soup.find("div", id="promotions")
'''
<div id="promotions">
</div>
'''

# (3) class로 찾기
soup.find("div", "page-header") 
'''
<div class="page-header action">
<h1>Travel</h1>
</div>
'''
```

- ✔ __특정 태그 요소 여러개__ 가져오기
    - 리스트로 반환함
```python
# 기본
h1 = soup.find_all("h1")
'''
[<p>This domain is for use in illustrative examples in documents. You may use this
     domain in literature without prior coordination or asking for permission.</p>,
 <p><a href="https://www.iana.org/domains/example">More information...</a></p>]
'''

# 응용
import time

for i in range(1, 6) :
  res = requests.get("https://hashcode.co.kr/?page={}".format(i), user_agent)
  soup = BeautifulSoup(res.text, "html.parser")

  questions = soup.find_all("li", "question-list-item")
  for question in questions: 
    print(question.find("div","question").find("div","top").h4.text)
  
  time.sleep(0.5) # interval 줌
'''
파이썬 프로젝트 중 오류
구조체 사용하여 다항식의 덧셈 구현하는 문제
Fourier transform 된 사진파일의 HPF 코으
... (생략)
'''

```

<br>

### 참고 자료 
[BeautifulSoup 공식 document](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#id15)

<br>

# 📌 BeautifulSoup 실습 
강의를 보며 강의 자료를 활용하여 실습을 진행하였다. (환경 : Google Colab)   
[(Github) BeautifulSoup 기초 실습 ](https://github.com/HyeM207/TIL/blob/main/HTML%2CWEB/BeautifulSoup%20%EA%B8%B0%EC%B4%88%20%EC%8B%A4%EC%8A%B5.ipynb)
