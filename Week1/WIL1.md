# 스프링 웹 개발 기초

- 정적 컨텐츠 :Welcome Page 처럼 서버에서 하는 일 없이 파일을 웹브라우저로 그대로 가져오는 것
- MVC와 템플릿 엔진 (가장 많이 하는 방식)
    - 템플릿 엔진: html을 서버에서 프로그래밍 하여 동적으로 바꿔서 가져옴
    - 이를 위한 Model, View(화면), Controller의 3가지
- API: 안드로이드나 ios 개발 시, 서버 입장에서 JSON이라는 데이터 포맷으로 클라이언트에 데이터 전달 (화면은 클라이언트에서 정리) / 서버끼리의 통신 시 사용

---

### 📍MVC와 템플릿 엔진

- MVC: Model, View, Controller
- 사용자가 View를 통해 요청을 보내면 Controller가 요청을 받고, Controller는 Model을 통해 데이터를 가져오고, 그 데이터를 바탕으로 View를 통해 시각적 표현을 제어해 사용자에게 전달
- 사용자 인터페이스와 비즈니스 로직을 분리!

```java
@Controller
public class HelloController {
 @GetMapping("hello-mvc")
 public String helloMvc(@RequestParam("name") String name, Model model) {
 model.addAttribute("name", name);
 return "hello-template";
 }
}
```

```html
resources/templates/hello-template.html

<html xmlns:th="http://www.thymeleaf.org">
<body>
<p th:text="'hello ' + ${name}">hello! empty</p>
</body>
</html>
```

[http://localhost:8080/hello-mvc`?name=spring`](http://localhost:8080/hello-mvc?name=spring)

→ name이 spring 등으로 바뀜 → 바뀐 name이 model에 담김

- 정적 컨텐츠 와의 차이: 템플릿에서는 변환되어 html이 웹 브라우저에서 나타난다

| 모델(Model) | 뷰(View) | 컨트롤러(Controller) |
| --- | --- | --- |
| → 데이터를 가진 객체 | → 사용자가 볼 결과물 생성하기 위해 모델로 부터 정보 얻어옴 | → 사용자가 접근한 URL에 따라 사용자의 요청 사항 파악 후, 그 요청에 맞는 데이터를 Model에 의뢰, 데이터를 View에 반영 |
| → 상태에 변화가 있을 때 컨트롤러와 뷰에 통보 |  |  |

---

### 📍API

- 프로그램과 프로그램을 연결시켜주는 매개체

```java
@Controller
public class HelloController {
 @GetMapping("hello-string")
 @ResponseBody //http에서의 헤더와 바디 중 바디에 이 데이터를 직접 넣어주겠다는 의미
 public String helloString(@RequestParam("name") String name) {
 return "hello " + name; //name 부분이 요청한대로 바뀜.
 }
}
```

view 없이 받은  문자가 그대로 나타남

→ 페이지 소스에 html 파일이 아닌 문자 그 자체가 나타남

- API는 문자보다도 데이터를 받아야 할 때 주로 사용

```java
@Controller
public class HelloController {

	 @GetMapping("hello-api")
	 @ResponseBody
	 public Hello helloApi(@RequestParam("name") String name) {
		 Hello **hello** = new Hello();
		 hello.setName(name);
		 return **hello**; //문자가 아닌 객체를 넘김
	 }

	 static class Hello {
		 private String name;

		 public String getName() {
			 return name; //꺼낼 때
		 }

		 public void setName(String name) {
			 this.name = name;  //넣을 때
		 } 
	 }
}
```

@ResponseBody 를 사용하고, 객체를 반환하면 객체가 JSON으로 변환됨 (디폴트 세팅)

- JSON?
    - key-value 로 이루어진 구조 {키: 밸류}
    - html 처럼 열고 닫고 하지 않아도 됨
- @ResponseBody 사용 → HttpMessageConverter 동작

→ 객체: JsonConverter / 문자: StringConverter 형태로 웹브라우저나 서버, 클라이언트 등에 보냄

- API 서버: API를 제공해주는 서버

🌀 데이터를 클라이언트(예: 사용자가 설치한 앱) 로부터 분리하여 서버에 저장 → 클라이언트는 정보에 접근하고 수정하기 위해 서버에 지속적으로 요청 보냄 → 접근해야하는 프로그램, 데이터 에 접근하지 않고 API를 통해 정보를 요청

---

### 📍REST 와 RESTful

- REST (Representational State Transfer)

→ 자원(resource)의 표현(representation)에 의한 상태 전달: 자원을 이름(표현)으로 구분해 해당 자원의 상태(정보)를 주고 받는 모든 것

✔️ 구성요소

| 자원(Resource): URI  | 행위(Verb): Method | 표현(Representation of Resource) |
| --- | --- | --- |
| 모든 자원에 고유한 ID가 존재(이 자원은 Server에 존재) | HTTP 프로토콜의 Method를 사용 | Client(자원 요청)와 Server(자원 가짐)가 데이터를 주고 받는 형태 |
| 자원을 구별하는 ID는 ‘/groups/:group_id’와 같은 HTTP URI | GET, POST, PUT, DELETE 등의 메서드를 통해 CRUD Operation 적용 | JSON or XML 통한 전달이 일반적 |
- CRUD Operation?
    
    Create : 생성 (POST)
    
    Read: 조회 (GET)
    
    Update: 수정 (PUT)
    
    Delete: 삭제 (DELETE)
    
    HEAD: header 정보 조회 (HEAD)
    

- REST API: REST를 기반으로 서비스 API를 구현한 것

→ URI는 정보의 자원을 표현

→ 자원에 대한 행위는 HTTP Method로 표현(행위는 URI에 포함 X)

<aside>
⚠️  REST API 설계 규칙

1. URI는 명사 사용(동사 X)
2. 슬래시로 계층 관계 표현
3. URI 마지막 문자로 슬래시 포함 X
4. 밑줄(_) 사용 X, 하이픈(-) 사용
5. URI는 소문자로만 구성
6. HTTP 응답 상태 코드 사용
7. 파일확장자는 URI에 포함 X
</aside>

- RESTful : REST의 설계규칙을 잘 지켜서 설계된 API