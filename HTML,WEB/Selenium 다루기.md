# 📌 Selenium  다루기
## #️⃣ Selenium 란 
Selenium은 Python을 이용하여 웹 브라우저를 조작할 수 있는 자동화 프레임워크 이다. 

<br>


## #️⃣ Selenium 함수 
### ✅ 설치
1. Selenium 설치
```terminal
pip install selenium
```

2. Webdriver 설치
- Webdriver는 웹 브라우저를 제어할 수 있는 자동화 프레임워크이다.
- webdriver-manager는 webdriver를 관리하는 라이브러리
```terminal
pip install webdriver-manager
```
<br>


### ✅ Selenium 불러오기 
```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager
```
<br>

### ✅ Webdriver로 Chrome() 객체 생성
```python
driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))

# `with-as` 구문으로 명령이 끝나면 driver를 종료하도록 설정 가능
with webdriver.Chrome(service=Service(ChromeDriverManager().install())) as driver:
    driver.get("http://www.example.com")
```
<br>

### ✅ get(url) 요청보내기
- 응답을 `page_source`속성으로 HTML문서 확인 가능
```python
driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))
driver.get("http://www.example.com")
print(driver.page_source)
```
<br>

### ✅ driver로 특정 요소 하나/여러개 찾기
`.find_element(by, target)` : 하나 찾기  
`.find_elements(by, target)` : 여러개 찾기 (리스트로 반환)  

`by`: 
- By.ID
- By.CLASS_NAME
- BY.TAG_NAME
- By.XPATH
- By.CSS_SELECTOR 
- 등등  

`target`: 대상 속성 

#### 예제 
```python
driver.get("http://www.example.com")
    print(driver.find_element(By.TAG_NAME, "p").text)
```

```python
driver.get("http://www.example.com")
    for element in driver.find_elements(By.TAG_NAME, "p"):
        print("Text: " , element.text)
```

```python
driver.get("http://www.example.com")
driver.find_element(By.XPATH, '//*[@id="__next"]/div/main/div[2]/div/div[4]/div[1]/div[1]/div/a/div[2]/p[1]' ).text
```

<br>

### ✅ Wait and Call
>  *명시적 기다림(Explicit Wait) 과 암묵적 기다림(Implicit Wait)

- `Explicit Wait`: 다 로딩이 될 때까지 지정한 시간 동안 대기
- `Implicit Wait`: 특정 요소에 대한 제약을 통한 대기

#### Implicit Wait
암시적 기다림을 적용
(반드시 해당 시간을 기다리는 것이 아니라, 로딩이 다 될때까지의 한계 시간을 의미함)
```python
driver.implicitly_wait(10)
```

#### Explicit Wait
명시적 기다림을 적용
(해당 요소가 존재할 때까지 기다림)
```python
element = WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.XPATH, '//*[@id="__next"]/div/main/div[2]/div/div[4]/div[1]/div[1]/div/a/div[2]/p[1]' )))
```

<br>

### ✅ Mouse Event
마우스 움직이기(move), 마우스 누르기(press down), 마우스 떼기(press up) 등을 코드로 조작 가능함

이때 `ActionChains`을 통해 행위를 연속적으로 할 수 있게 한다.

[ 함수 종류 ]
- `.clickAndHold(clickable)`
- `.click(clickable)`
- `.contextClick(clickable)`
- `addAction(mouse.createPointerDown(PointerInput.MouseButton.BACK.asArg()))`
    - `.addAction(mouse.createPointerUp(PointerInput.MouseButton.BACK.asArg()));`
- `.addAction(mouse.createPointerDown(PointerInput.MouseButton.FORWARD.asArg()))` 
    - `.addAction(mouse.createPointerUp(PointerInput.MouseButton.FORWARD.asArg()));`
- `.doubleClick(clickable)`
- `.moveToElement(hoverable)`
- [다른 함수 더 보기(docs)](https://www.selenium.dev/documentation/webdriver/actions_api/mouse/)

```python
# click 예제
ActionChains(driver).click().perform()
```

<br>

### ✅ Keyboard Event
키보드 누르기(press down), 키보드 떼기(press up) 등을 코드로 조작 가능함  
[ 함수 종류 ]
- `.keyDown(Keys.SHIFT).sendKeys("a")`
- `.keyUp(Keys.SHIFT).sendKeys("a")`
- `.sendKeys(textField, "Selenium!")`
- [다른 함수 더 보기(docs)](https://www.selenium.dev/documentation/webdriver/actions_api/keyboard/)


```python
ActionChains(driver).send_keys_to_element(id_input, "자신의 id").perform()
```
<br><br>

# 📌 Selenium 실습
강의를 보며 강의 자료를 활용하여 실습을 진행하였다. (환경 : Windows 10 로컬 환경)   
[(Github) Selenium 실습 ](https://github.com/HyeM207/TIL/blob/main/HTML%2CWEB/Selenium%20%EC%8B%A4%EC%8A%B5.ipynb)

<br>

## #️⃣ 상세 실습
여러 실습 중 프로그래머스 'https://hashcode.co.kr' 사이트에서 로그인 버튼을 찾아 로그인 페이지로 이동 후 id, pw를 입력하여 로그인한 실습을 따로 첨부해본다.

프로그래머스 사이트 디자인이 바뀌어, 강의 내용 그대로 코드를 돌리면 오류가 발생한다. 이를 해결한 과정과 완성된 코드를 아래 첨부한다. 
```python
# 오류 없는 코드
from selenium import webdriver
from selenium.webdriver import ActionChains
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.common.actions.action_builder import ActionBuilder
from selenium.webdriver import Keys, ActionChains
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
import time

# driver를 이용해 해당 사이트에 요청 보냄
driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))
driver.get("https://hashcode.co.kr/")
driver.implicitly_wait(1)

# 내비게이션 바에서 "로그인" 버튼을 찾아 누름
button = driver.find_element(By.XPATH, '//*[@id="main-app-gnb-header"]/div/div/div[1]/div/div[1]/a') # 성공 코드 
driver.execute_script("arguments[0].scrollIntoView();", button)
driver.execute_script("arguments[0].click();", button)


# "아이디" input 요소에 여러분의 아이디  입력
id_input = driver.find_element(By.XPATH, '//*[@id="main-app-account"]/div/div[2]/div/div[2]/div[1]/div/div[2]/div[2]/input')
ActionChains(driver).send_keys_to_element(id_input, "자신의 id").perform()
time.sleep(1)

# "패스워드" input 요소에 여러분의 비밀번호 입력
pw_input = driver.find_element(By.XPATH, '//*[@id="main-app-account"]/div/div[2]/div/div[2]/div[1]/div/div[2]/div[4]/input')
ActionChains(driver).send_keys_to_element(pw_input, "자신의 pw").perform()
time.sleep(1)

# "로그인" 버튼을 눌러서 로그인 완료
login_btn = driver.find_element(By.XPATH, '//*[@id="main-app-account"]/div/div[2]/div/div[2]/div[1]/div/div[2]/button')
ActionChains(driver).click(login_btn).perform()
time.sleep(1)

```
<br>

### 🔥 오류 분석과 해결과정
만약 로그인 button 을 찾는 코드를 강의 내용처럼 아래 코드로 작성하면 오류가 발생한다.   
```python
button = driver.find_element(By.CLASS_NAME, "ReBoJZjit5lOjyMKk0bT").click()
ActionChains(driver).click().perform()
```
발생한 에러와 원인 그리고 그 해결과정을 아래에 정리해보았다.   

- 에러 이름 : ElementNotInteractableException

- 원인 :
    - className만으로 `<a>` 태그에 접근 불가함 
    - 로그인 클릭하는 부분이 버튼이 아닌 `<a>` 태그라 click() 상호작용이 안되는 것으로 보임 

[ 원인 1 해결 과정 ]  
- 원인 1 해결 방법 : XPath로 접근함 -> 성공
- 분석 : `<a>` 태그를 못 찾는것이 아닌가 해서 get_attribute로 class와 href를 출력해봤을 때는 잘 불러왔음 ->  `<a>` 태그 접근하는덴 문제 없음

[ 원인 2 해결 과정 ]  
- 시도 1: ActionChains(driver).move_to_element(button).click().perform() 로 바꿔서 해봄 -> 해결 X
- 시도 2: ActionChain이 아닌 Button 자체에서 click()함수와 send_keys(Keys.ENTER) 함수 써봄 -> 해결 X
- 시도 3: ChatGPT에서 알려준 자바스크립트로 요소를 찾을때 까지 스크롤해서 클릭을 강제하는 코드를 작성함 -> 성공 !!!!!!

✅ 정리 : XPath로 접근 방식 바꾸고, 클릭을 강제하는 자바스크립트 코드로 해결함
