# Git Commit Message Conventions
[AngularJS Commit Message Conventions](https://github.com/angular/angular/blob/main/CONTRIBUTING.md#type) 을 참고하여 작성하였습니다. 

<br/>

## Commit 메세지 형식
``` html
<header>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
``` 

- 커밋 한 줄에 100 문자 이상 되지 않도록 하기
- 커밋 메시지 형식은 header 1개, body 1개, footer 1개로 구성되고 각각은 공백 줄로 구분됨
    - `header` : 필수 작성. 커밋 형식 꼭 맞추기
    - `body` : docs 타입 빼고는 필수 작성. 본문이 있으면 최소 20자 이상이고 형식 맞춰서 작성
    - `footer` : 선택 사항.  

<br/>

## (1) Header 형식 구성
```html
<type>(<scope>): <short summary>
  │       │             │
  │       │             └─⫸ 현재시제로 작성. 대문자 X, 마침표 X
  │       │
  │       └─⫸ Commit Scope: animations|bazel|benchpress|common|compiler|compiler-cli|core|
  │                          elements|forms|http|language-service|localize|platform-browser|
  │                          platform-browser-dynamic|platform-server|router|service-worker|
  │                          upgrade|zone.js|packaging|changelog|docs-infra|migrations|ngcc|ve|
  │                          devtools
  │
  └─⫸ Commit Type: build|ci|docs|feat|fix|perf|refactor|test
```   


`type` 구성 
- feat (새 기능 추가)
- fix (버그 수정)
- docs (문서화)
- style (포맷팅, 세미콜론 누락 등)
- refactor (코드 리팩토링)
- test (테스트 관련)
- chore (유지보수 등 그 외 수정사항)

</br>

`scope` 구성  
commit한 부분의 구체적 범위를 작성. ex. $location, $browser, $compile, $rootScope, ngHref, ngClick, ngView, etc...

</br>

`summary` 구성  
변경 사항에 대해 간략한 설명 작성.
- 명령형, 현재 시제 사용
- 첫 글자 대문자 x
- 끝에 (.) 사용 x

</br>

## (2) Body 형식 구성
- 코드 변경에 대한 이유를 설명하는 부분 (이전 동작과 새 동작의 비교 등을 포함할 수 있음)
- 명령형, 현재 시제 사용

</br>

## (3) Footer 형식 구성
- reaking Change(주요 변경사항)에 대한 정보를 포함하거나, GitHub 이슈 등을 적을 수 있는 부분  

### Breaking Change(주요 변경사항) 경우  
```html
BREAKING CHANGE: <breaking change summary>
<BLANK LINE>
<breaking change description + migration instructions>
<BLANK LINE>
<BLANK LINE>
Fixes #<issue number>
``` 
- `Header` 부분 : "BREAKING CHANGE:"와 해당 부분의 간략한 설명을 적는다.
- `Body` 부분 : 한 줄 띄운 후, 'migration 지침'을 포함하여 주요 변경에 대한 자세한 설명을 작성한다.
- `Footer` 부분 : 두 줄 공백 뒤엔 "Fixes"와 이슈 넘버를 적는다.

</br>

### Deprecated(더 이상 사용되지 않고 사라질 것) 혹은 Referencing issues(해결된 이슈)의 경우
```html
DEPRECATED: <what is deprecated>
<BLANK LINE>
<deprecation description + recommended update path>
<BLANK LINE>
<BLANK LINE>
Closes #<pr number>
```
- `Header` 부분 :  "DEPRECATED:"와 무엇이 사라질 것인지에 대한 간략한 설명을 적는다.
- `Body` 부분 : 한 줄 띄운 후, 이에 대한 자세한 설명과 권장하는 업데이트 방법을 언급한다.
- `Footer` 부분 : 두 줄 공백 뒤엔 pr(pull&request) 넘버를 적는다.

</br>

## 예시
```md  
feat($browser): onUrlChange event (popstate/hashchange/polling)

Added new event to $browser:
- forward popstate event if available
- forward hashchange event if popstate not available
- do polling when neither popstate nor hashchange available

Breaks $browser.onHashChange, which was removed (use onUrlChange instead)
```  

```md 
fix($compile): couple of unit tests for IE9

Older IEs serialize html uppercased, but IE9 does not...
Would be better to expect case insensitive, unfortunately jasmine does
not allow to user regexps for throw expectations.

Closes #392
Breaks foo.bar api, foo.baz should be used instead
```  

```md
feat(directive): ng:disabled, ng:checked, ng:multiple, ng:readonly, ng:selected

New directives for proper binding these attributes in older browsers (IE).
Added coresponding description, live examples and e2e tests.

Closes #351
```  


```md  
style($location): add couple of missing semi colons
```  

```md  
docs(guide): updated fixed docs from Google Docs

Couple of typos fixed:
- indentation
- batchLogbatchLog -> batchLog
- start periodic checking
- missing brace
```   

```md  
feat($compile): simplify isolate scope bindings

Changed the isolate scope binding options to:
  - @attr - attribute binding (including interpolation)
  - =model - by-directional model binding
  - &expr - expression execution binding

This change simplifies the terminology as well as
number of choices available to the developer. It
also supports local name aliasing from the parent.

BREAKING CHANGE: isolate scope bindings definition has changed and
the inject option for the directive controller injection was removed.

To migrate the code follow the example below:

Before:

scope: {
  myAttr: 'attribute',
  myBind: 'bind',
  myExpression: 'expression',
  myEval: 'evaluate',
  myAccessor: 'accessor'
}

After:

scope: {
  myAttr: '@',
  myBind: '@',
  myExpression: '&',
  // myEval - usually not useful, but in cases where the expression is assignable, you can use '='
  myAccessor: '=' // in directive's template change myAccessor() to myAccessor
}

The removed `inject` wasn't generaly useful for directives so there should be no code using it.
```   
