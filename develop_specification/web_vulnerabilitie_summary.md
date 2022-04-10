
# 웹 보안 취약점 정리

---

## 1. Client side: Session Higacking

HTTP 통신은 connectionless, stateless 속성을 가진다. 즉 웹 서버는 어떤 클라이언트가 접속해있었는지, 어떤 클라이언트가 누구인지 기억할 수 없다. 이것을 위해 쿠키(or 세션) 정보를 사용하는데, 쿠키는 key와 value로 이뤄진 일종의 단위이며 정보와 상태를 표현하는 용도로 사용한다. 쿠키 데이터는 클라이언트가 지니게 되며 웹 서버로 request시 필요한 쿠키를 헤더에 담아 전송한다.

예시로 A가 웹 서비스에 로그인하면 서버는 해당 유저의 클라이언트를 식별하기 위해 A임을 식별할 수 있는 인증 정보를 담은 쿠키를 response에 담아 보낸다.

A의 클라이언트는 쿠키를 지니고 있고 앞으로 인증이 필요한 모든 request 통신에 포함한다.

이 취약점은 어떠한 공격자가 어떠한 행위를 통해 인증정보가 담긴 쿠키를 탈취, 해당 쿠키로 권한을 탈취하는 공격이다.

## 2. Client side: XSS (Cross Site Scripting)

XSS 공격은 웹 서비스를 이용하는 다른 사용자에게서 실행할 수 있는 악의적인 스크립트를 주입할 수 있다. SOP(Same Origin Policy)의 등장으로 사그라들었지만 우회 기법의 발전으로 다시 주목받고 있다. 이 공격은 공격 스크립트가 포함된 컨텐츠에 사용자가 접근하여 화면에 출력할 때 발생한다.

JS는 버튼 이벤트와 같이 웹이 어떻게 동작할지를 정의한다. 이러한 구현을 위해 JS는 사용자와의 상호작용 없이 사용자 권한으로 서버와 통신하고 데이터를 조작하는 것이 가능하다. 이런 이유로 JS는 XSS공격에 흔히 사용한다.

예시로 XSS 취약점이 있는 게시물 및 댓글기능에 `<script>location.href = ”http://hackersite.io/phishing”;</script>`와 같은 스크립트가 주입되어있고 사용자가 접근하게 되면, 사용자는 사용자 모르게 공격자가 만들어둔 피싱 사이트로 이동된다.

## 3. Client side: CSRF (Cross Site Request Forgery)

CSRF 취약점은 어떠한 사용자던 그 사용자의 권한으로 특정 주소로 임의 HTTP 요청을 보내도록 만들 수 있다. 이를 위해서 타겟으로 하여금 악의적 스크립트를 실행하도록 유도해야하는 점은 XSS와 동일하다. CSRF 공격 스크립트는 JS 혹은 HTML로 작성할 수 있고 img 혹은 form 태그로 세션 탈취를 위해 요청에 쿠키를 포함시키도록 할 수 있다. `http://samplehttp:/sample/valn?param=<img src = “/admin/notice_flag?userid=admin”>`

## 4. Server side: SQL Injection

RDBMS는 SQL을 사용하여 데이터를 조작 및 관리한다. SQL Injection은 SQL에 임의 문자열을 삽입할 수 있을 때 발생한다. 이 공격을 통해 인증을 우회하거나 DB 데이터를 조작할 수 있다. 아래는 인증을 우회하는 공격 예시이다.

`ID: admin"--`

`PW: dummy`

이와 같이 입력할 경우 별도의 방어가 없다면 SQL 쿼리는 다음과 같이 작성된다.

`SELECT * FROM users WHERE userid="admin"--" AND userpassword="dummy";`

중간에 삽입된 --으로 인해 그 뒤의 내용은 주석이 되버리며 별도의 PW 확인 없이 인증 우회가 가능하다.

## 5. Server side: NoSQL Injection (해당 없음)

## 6. Server side: Command Injection (해당 없음)

## 7. Server side: File Vulnerability

파일 취약점은 웹 서비스에서 파일을 업로드하거나 다운로드 할 수 있고 대상 파일명을 지정할 수 있을 때 발생한다. 파일 다운로드 취약점, 파일 업로드 취약점으로 구분할 수 있다.

### 7.1 File Upload Vulnerability

업로드되는 파일의 이름을 임의로 조작할 수 있을 때 발생한다.

* Path Traversal: 업로드 규칙을 우회하여 공격자가 원하는 위치에 파일을 업로드한다. 서버나 시스템을 구성하는 파일을 덮어씌울 수도 있다.

* Upload Malicious File: 파일 확장자를 제대로 확인하지 않았을 때 발생한다. 서버는 보통 php, jsp, asp와 같은 실행파일들을 CGI를 통해 사용자에게 보낸다. 여기서 악의적인 스크립트를 업로드할 수 있다.

### 7.2 File Download Vulnerability

다운로드할 파일의 이름을 임의로 조작할 수 있을 때 발생한다. ../와 같은 디렉토리 경로를 사용하여 서버 시스템의 민감한 파일을 받을 수 있게된다. (like /etc/passwd)

## 8. Server side: SSRF (해당 없을듯)

SSRF 취약점은 서버측, 웹 서비스 측에서 임의의 request를 보내도록 한다. CSRF와는 다르게 웹 서비스 권한으로 진행된다. 최근 웹 서비스 개발에 있어서 마이크로서비스, 백오피스와 같은 구조를 사용하는 경우가 많아지면서 동시에 SSRF 위협이 증가하고있다.