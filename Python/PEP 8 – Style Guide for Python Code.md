# PEP 8 – Style Guide for Python Code


## #️⃣ 공백 
### (1) 공백 줄
- __ 각 함수나 클래스 정의 위와 아래__ 에 __빈 줄을 하나__ 씩 두어 가독성을 높임
    - import와 class 선언 사이에는 공백 `두 줄`
    - class와 첫 번째 내부 함수 사이에 공백 `두 줄`
    - 내부 함수끼리는 공백 `한 줄` (단 가독성을 위해 공백 줄 추가 허용)

### (2) 공백 (스페이스)
- 괄호 안쪽에 공백 X
- `콤마(,) 다음`, `연산자 앞 뒤` 에는 공백 추가
- 여러 줄로 구성된 표현식에서는 한 줄마다 연산자 앞에 공백을 추가

## #️⃣  들여쓰기
- 들여쓰기는 `스페이스` __4번__ 을 사용
- 불필요한 들여쓰기 사용 X
- 한 줄의 들여쓰기 수는 79자를 넘지 않음
- 긴 문자열은 여러 줄로 나누어 작성하되, 들여쓰기는 현재 블록에 맞춤


## #️⃣  변수명
- 변수명, 함수명, 메소드명은 __소문자__ 로 작성하며, 단어 간에는 밑줄(_)을 사용하여 구분
    - 단 `클래스` 이름은 __대문자로 시작__ 함, 단어 사이에 밑줄 사용
    - 단 상수는 모두 대문자로 작성 권고하며 단어 사이에 밑줄 사용

## #️⃣  import 순서
- 모듈은 __알파벳 순서__로 import 
- __모듈 임포트 순서__
    1. 표준 라이브러리 모듈
    2. 서드파티 모듈
    3. 로컬 모듈


## #️⃣ Docstring
> 모듈, 함수, 클래스 등을 정의할 때 __설명__ 을 위해 기술되는 텍스트 

- 모듈 module은 기본으로 docstring 가지고 있어야함
- docstring은 코드의 문서 역할과 같음

### (1) One-line Docstring
```python
def function(x, y) :
    """Check session and return a user list"""
```

- 공백 없이 바로 `'''` 사용해서 `명령`으로 설명함
    - e.g. Return user name 
    - e.g. Check session and return a user list 
- 만약 함수 내부를 확인할 수 없는 함수의 경우, 아래와 같이 사용함
    - function(x,y) -> dict


### (2) Multi-line Docstring
```python
def get_info(press:str , urls: list):
    """Form a complex number.

    Keyword arguments:
    real -- the real part (default 0.0)
    imag -- the imaginary part (default 0.0)
    """
    if imag == 0.0 and real == 0.0:
        return complex_zero
    ...
```
- 다음과 같이 3가지 구성
    1. One-line과 같이 함수 기능 한 줄 요약
    2. 한 줄 공백
    3. 자세한 설명

- 한 줄 요약은 `"""` 바로 뒤 혹은 바로 아래줄에 작성
- 모든 줄은 __여는 따옴표와 같은 인덴트__ 사용함
- 닫을 때는 `"""`을 쓰고 그 다음 줄에 코드 작성함  

#### [ 대상에 따른 작성 권고사항 ]
#### 함수
- 함수 설명 시, `함수 동작`, `arguments`, `return value`, `side-effects`, `exception`, `호출사항`등을 요약하여 작성함
- optional argument는 필수 작성

#### class
- public method와 instance 목록 요약 서술
- subclass와 추가된 interface는 목록에 별도 표시
- `__init__` 메소드를 포함한 __모든 개별 method는 각자의 Docstring__ 을 가짐 


<br><br>

#### 참고 문헌
[PEP 8 – Style Guide for Python Code](https://peps.python.org/pep-0008/)

[PEP-8 요약](https://yongwookha.github.io/Study/2022-02-26-pep8-summary)