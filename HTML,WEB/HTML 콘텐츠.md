# 📌 [1] 콘텐츠

## #️⃣ 1. 제목 태그(h1~h6)
- h1은 페이지 내에 `한 번만` 사용해야하며, `구획의 순서` 대로 작성해야된다. 

```html
<!DOCTYPE html>
<html lang="ko">
    <head>
        <title>문서 제목</title>
        <link rel="stylesheet" href="style.css"/>
    </head>

    <body>
        <h1>제목 1</h1>
        <h2>제목 2</h2>
        <h3>제목 3</h3>
        <h4>제목 4</h4>
        <h5>제목 5</h5>
        <h6>제목 6</h6>
    </body>
</html>
```

<br>

## #️⃣ 2. 문단 태그 (p)
- 문서에서 하나의 `문단(paragraph)`를 나타내는 태그
- 주로 제목 태그와 함께 사용됨. 단독 사용도 ok
```html
<p>문단 태그를 사용해보시오!</p>
```
<br>

## #️⃣ 3. 서식 태그 (b/strong, i/em, u, s/del)

| 태그명 | 분류 | 설명 | 
| ---- | ----- | ---- |
|`<b>` | 볼드체 | 의미 없이 단순 굵은 글씨|
| `<strong>` | 볼드체 |  굵은 글씨 변경 후 __"강조"__ 의 의미 부여| 
| `<i>` | 기울임체 | 기울임과 동시에 텍스트가 __"문단의 내용과 구분"__ 되어야 하는 경우에 사용 | 
| `<em>` | 기울임체 | 기울임과 내용에 __"강조"__ 의미 부여  |
| `<u>` | 밑줄 |  밑줄을 넣어 주석을 가지는 단어임을 나타냄. 단순 밑줄 긋는 용도로 사용하면 안됨 | 
| `<s>` | 취소선 | 단순 시각적인 취소선만 추가되고 접근성 기기에 취소에 대한 안내는 없음 | 
| `<del>` | 취소선 | 문서에서 제거된 텍스트 나타냄.`<ins>`태그와 함께 사용시 텍스트 옆에 추가된 텍스트 표현 가능 | 

 

<br><br>

## #️⃣ 4. 링크 이동 (a)
- 클릭하면 걸어둔 __링크__ 로 페이지 이동함
- 속성 : 
    - `href` : 이동하고자 하는 파일/URL
    - `target` : 이동할 링크를 새 창(`_blank`), 현재 창(`_self`)등 원하는 타겟을 지정 가능함 
```html
<a href="https://www.naver.com" target="_blank"> 네이버 바로가기 (새 창) </a>
```
<br><br>

## #️⃣ 5. 멀티미디어
### (1) \<img\>
- 문서 내 이미지를 넣을 수 있는 태그 
- 속성 :
    - `src` : 이미지 경로를 넣으면 이미지 출력됨 
    - `alt` : 이미지 로딩 문제 시 대체 텍스트

```html
<img src="/logo.png" alt="Hyemin Kim 로고">
<img src="/logo.svg" alt="Hyemin Kim 로고 svg">
```
### (2) \<figure\>, \<figcaption\>
- 하나의 __독립적인 콘텐츠__ 로 분리하고 그에 대한 설명 넣을 수 있는 태그
- `<figcaption>`으로 콘텐츠 설명/범례 추가 가능 (맨 위나 맨 아래 추가)
- 보통 이미지를 넣는데, 비디오/오디오 등 문서 흐름에 참조는 되지만 __독립적으로 분리__ 되어도 되는 내용을 담을 수 있음 
```html
<figure>
    <img src="assets/logo.png" alt="로고 이미지"/>
    <figcaption>로고 이미지 입니다.</figcaption>
</figure>
```



### (3) \<video\>
- 문서 내 __영상 첨부__ 가능한 태그
- 속성 : 
    - `src` :  비디오를 문서 내 첨부 가능
    - `poster` :  비디오 로드 전 포스터 보여줌
    - `<source>` : 여러 타입의 비디오 제공 가능 


```html
<video src="assets/test.mp4" autoplay></video>
```

```html
<video autoplay>
    <source src="assets/video1.webm" type="video/webm">
    <source src="assets/video2.mp4" type="video/mp4">
</video>
```

### (4) \<audio\>
- 문서 내 __소리 첨부__ 가능한 태그
- 속성 : 
    - `src` :  소리를 문서 내 첨부 가능
    - `controls` :  해당 속성 사용시 재생/정지 버튼의 __컨트롤러__ 띄울 수 있음 
    - `<source>` : 여러 타입의 비디오 제공 가능 

```html
<audio controls src="assets/audio.mp3"></audio>
```

<br><br>

## #️⃣ 6. 리스트
### (1) 정렬되지 않은 목록 \<ul\>, \<li\>
- 기본 bullet 형식으로 목록 그림
- `<li>` 태그로 목록 구성할 수 있다
- `<ul>` 자식으로는 `<li>`태그만 들어와야 하고, 하위 리스트를 만드려면 `<li>` 태그 안에 `<ul>`, `<ol>` 태그를 사용한다.

```html
<ul>
    <li> 리스트1 </li>
    <li> 리스트2 </li>
    <li> 리스트3
        <ul>
            <li>하위 리스트1</li>
            <li>하위 리스트2</li>
        </ul>
    </li>
</ul>
```
<br>

### (2) 정렬된 목록 \<ol\>, \<li\>
- 숫자(1,2,3 ...) 형식으로 목록 그림
- `<li>` 태그로 목록 구성할 수 있다
- `<ol>` 자식으로는 `<li>`태그만 들어와야 하고, 하위 리스트를 만드려면 `<li>` 태그 안에 `<ul>`, `<ol>` 태그를 사용한다.
```html
<ol>
    <li> 리스트1 </li>
    <li> 리스트2 </li>
    <li> 리스트3
        <ul>
            <li>하위 리스트1</li>
            <li>하위 리스트2</li>
        </ul>
    </li>
</ol>
```

<br>

### (3) 설명 목록 \<dl\>, \<dt\>, \<dd\>
- `<dt>` 태그에는 사용된 단어를, 설명은 `<dd>`태그에 작성한다. 그리고 `<dl>`로 전체를 감싼다.
- 주로 용어사전 처럼 __"키-값"__ 이 있는 쌍의 목록을 나타낼 때 사용한다
- `<dt>`태그 여러개 작성하고 하나의 `<dd>`태그 구성으로 여러개 용어를 설명 가능 (반대도 가능)

```html
<dl>
    <dt> Chrome </dt>
    <dd> Google의 웹 브라우저</dd>
</dl>
```

<br><br>

## #️⃣ 7. 표 (table)
- `<table>` : 표 만드는 태그
    - `<tr>`로 행 구분 (row)
    - `<td>`로 열 구분 (cell)
    - `<th>`는 열 제목 
    - `<thead>`는 제목 그룹으로, __태그 안에 열 제목 행을 넣어__ 그룹 지을 수 있다.
    - `<tbody>`는 표 본문 요소 태그로, 태그 안에 여러 열의 행을 넣을 수 있다. 
    - `<tfoot>`은 표 바닥글 요소 태그로, 태그 안에 여러 열의 행을 넣을 수 있다. 
    - `<caption>`은 표 설명 태그로, __표가 가진 데이터 설명__ 을 작성한다.
```html
<!DOCTYPE html>
<html lang="ko">
    <head>
        <title>테이블 실습</title>
    
    </head>

    <body>
        <table>
            <thead>
                <tr>
                    <th>cell 제목 1</th>
                    <th>cell 제목 2</th>
                </tr>
            </thead>
            

            <tbody>
                <tr>
                    <td>cell 셀1</td>
                    <td>cell 셀2</td>
                </tr>
                <tr>
                    <td>cell 셀1</td>
                    <td>cell 셀2</td>
                </tr>
                <tr>
                    <td>cell 셀1</td>
                    <td>cell 셀2</td>
                </tr>
            </tbody>

            <tfoot>
                <tr>
                    <td>마지막</td>
                    <td>tfoot</td>
                </tr>
            </tfoot>
        </table>
    </body>
</html>
```
<br>

## #️⃣ 8. 외부 콘텐츠 (iframe)
- 현재 문서 안에 __다른 HTML 페이지를 삽입__ 할 수 있는 태그
    -`src`에 띄울 HTML 파일 이나 URL 삽입
    - 외부 페이지를 띄우는 거라 보안에 영향 받을 수 있음
    - `<a>`태그의 `target`속성을 `iframe`의 `name`으로 지정해주면 iframe에 문서 혹은 URL 연결 가능함

```html
<iframe src="https://www.naver.com" frameborder="0"></iframe>
```

```html
<!DOCTYPE html>
<html lang="ko">
    <head>
        <title>iframe 실습</title>
    
    </head>

    <body>
        <iframe name="sample" src="https://example.com">
            해당 브라우저는 iframe을 지원하지 않습니다.
        </iframe>

        <a href="login.html" target="sample">로그인 하기</a>
    </body>
</html>
```