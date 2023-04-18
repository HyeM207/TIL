# [1] Form 
## 📌 Form 기초
- __정보를 제출__ 하기 위한 태그 
- 정보 입력/선택하는 input, selectbox, textarea 존재
- 정보 입력 후 form을 제출하는 button 필요
- 속성 
    - `action` : 정보 제출 시 페이지 이동
    - `method` : 정보 제출 시 처리 방식 결정 (get/post;post방식이 더 보안이 강화됨)

```html
<!DOCTYPE html>
<html lang="ko">
    <head>
        <title>form 태그 실습</title>
    
    </head>

    <body>
        <form action="form_result.html" method="get">
            <input name="id" type="text">
            <input name="password" type="password">

            <select name="opt">
                <option>옵션1</option>
                <option>옵션2</option>
                <option>옵션3</option>
                <option>옵션4</option>
                <option>옵션5</option>
            </select>

            <button type="submit">전송!</button>
        </form>
    </body>
</html>
```

<br>

## 📌 태그들 
### #️⃣ \<label\>
- input, textarea, selectbox의 __설명을 작성__ 할 수 있는 태그
- `for` 속성으로 연결하고자 하는 태그의 `id`를 지정함 -> label 클릭시 연결된 태그 선택됨
- `id` 속성 값은 절대로 중복 X

```html
<label for="userid">아이디</label>
<input id="userid" type="text" name="userid">
``` 


### #️⃣ \<input\>
- 사용자에게 __데이터를 입력__ 받을 수 있는 태그
- 속성 : 
    - `type` : text, password,checkbox, radio, file, button, hidden 등 속성에 따라 받을 수 있는 유형 달라짐
        - button의 경우 `value` 속성이 버튼 이름이 됨
        - hidden의 경우 input을 시각적으로 숨기고, 제출 시 `value`속성 값이 전달됨
    - `value` : 기본 내용 입력해둘 수 있음
    - `name` : input 이름 지정 가능 

### #️⃣ \<select\>
- `<option>` 태그로 옵션 메뉴 제공
- 첫번째 option이 이름이 됨
- `value` 속성 선언을 안 하면 <option>태그의 콘텐츠가 기본값이 됨

### #️⃣ \<textarea\>
- 여러줄 입력할 수 있는 대화형 태그
- `cols/rows`속성으로 기본 너비와 높이 지정 가능. 너비와 높이는 글자 크기 기준으로 정의됨 


<details><summary> input, select, textarea의 속성</summary>

- `readonly` : 사용자가 수정 못하는 "읽기전용"으로 만듦  
- `required` : form제출 시 "필수 입력 사항"으로 만듦
- `placeholder` : input, textarea에 부가 설명을 입력 가능 (단 \<select\>에선 불가능)
- `disabled` : 요소가 비활성화 되고, 제출 시 값 제출 안됨

</details>

<br>

## 📌 전체 실습
```html
<!DOCTYPE html>
<html lang="ko">
    <head>
        <title>form 태그 실습</title>
    
    </head>

    <body>
        <form action="form_result.html" method="get">\
            <label fpr="username">아이디</label>
            <input name="username" name="id" type="text">

            <label>
                비밀번호
                <input name="password" type="password">
            </label>
            
            <label for="option">옵션 선택</label>
            <select name="opt" required>
                <option value="">선택하세요</option>
                <option value="옵션2_값">옵션2</option>
                <option value="옵션3_값">옵션3</option>
                <option>옵션4</option>
                <option>옵션5</option>
            </select>


            <input id="agree" type="checkbox" value="true" name="checkbox" required>
            <label for="agree">약관에 동의합니다</label>

            <input type="radio" name="number" value="1" required> 1
            <input type="radio" name="number" value="2" required> 2
            <input type="radio" name="number" value="3" required> 3
            <input type="radio" name="number" value="4" required> 4
            
            <input type="file">

            <input type="button" blaue="나는 버튼이에요">
            <input type="hidden" name="where" value="samplecode">

            <textarea name="introduction" id="introduction" cols="50" rows="15"> 안녕하세요 </textarea>

            <button>전송!</button>

            <button type="submit">전송!</button>
        </form>
    </body>
</html>
```

<br><br>

# [2] HTML 주의사항
- ✔ 태그는 소문자로 작성 권고
- ✔ 태그 열고 닫기 정확히 하기 (닫는 태그 생략 NO)
- ✔ 개발은 영어로
- ✔ 태그의 id는 한 파일에서 __고유 id__ 이어야 함
- ✔ 뎁스(depth) 꼭 유지하기
- ✔ 중복 태그 사용하지 말기 (ex. `<b><strong>이중 볼드 불가!</strong></b>`)
- ✔ `<a>`안에 `<button>`태그 넣지 말 것!