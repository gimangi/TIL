# Servlet

WAS에서 요청과 응답의 흐름을 간단한 메소드 호출로 다룰 수 있도록 하는 자바 기반 프로그래밍 기술

> 비즈니스 로직 개발에만 집중할 수 있도록



- TCP/IP 대기, 소켓 연결
- HTTP 요청 메시지 파싱
  - URL 확인
  - Content Type 확인
  - 바디 내용 파싱
- HTTP 응답 메시지 생성
  - HTTP 시작 라인 생성
  - Header 생성
  - 메시지 바디에 HTML 생성해서 입력
- TCP/IP 응답 전달, 소켓 종료



### 서블릿 컨테이너

- 톰캣처럼 서블릿을 지원하는 WAS를 서블릿 컨테이너라고 한다
- 서블릿 객체를 생성, 초기화, 호출, 종료하는 생명주기 관리
- 서블릿 객체를 **싱글톤**으로 관리
  - 공유 변수 사용에 주의할 것
- JSP도 서블릿으로 변환되어서 사용된다
- 동시 요청을 위한 **멀티스레드** 처리 지원
  - 스레드풀로 관리 (톰캣 기본 설정은 최대 200개)
  - 스레드풀 성능 테스트 방법
    - 아파치 ab, 제이미터, nGrinder
  - 싱글톤 객체 (서블릿, 스프링 빈)을 주의해서 사용해야 한다



## 서블릿 사용법

```java
@ServletComponentScan // 스프링이 자동으로 서블릿들을 등록
@SpringBootApplication
public class ServletApplication {
  
  public static void main(String[] args) {
    SpringApplication.run(ServletApplication.class, args);
  }
}
```

애플리케이션이 서블릿을 인식하고 등록할 수 있도록 `@ServletComponentScan` 어노테이션 추가



```java
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {
  
  @Override
  protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException {
    // 애플리케이션 로직
  }
}
```

`HttpServlet` 클래스를 상속하는 웹 서블릿 생성



### HttpServletRequest

- Attribute

  HTTP 요청이 시작부터 끝날 때까지 유지되는 임시 저장소 기능

  - `setAttribute(name, value)` 

  - `getAttribute(name)` 

- 세션 관리

  - `getSession(create: true)`

- 헤더, 바디 등 정보도 불러올 수 있다.



### HTTP 요청 데이터

- **쿼리 파라미터 [GET]**

  - URL의 쿼리 파라미터에 데이터를 포함해서 전달

  - HTTP 메시지 바디를 사용하지 않기 때문에 <u>content-type이 없다</u>.

  - /url?<u>username=hello&age=20</u>

  - Servlet

    `HttpServletRequest` 객체에서 `getParameterValue()` 메소드를 통해 파라미터 값을 불러올 수 있다.

- **HTML Form [POST]**

  - 메시지 바디에 쿼리 파라미터 형식으로 전달

  - content-type은 `application/x-www-form-urlencoded` 이다.

  - username=hello&age=20

  - Servlet

    쿼리 파라미터와 유사한 방식으로 `getParameter()` 메소드를 통해 파라미터 값을 불러올 수 있다.

- **HTTP 메시지 바디**

  - HTTP API에서 주로 사용하며 **JSON**, XML, TEXT 형식으로 전달한다.
  
  - 메시지 바디를 문자열로 가져오는 방법
  
    ```java
    // request는 HttpServletRequest 객체
    ServletInputStream inputStream = request.getInputStream();
    String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
    ```
  
  - JSON 형식으로 구성된 요청을 객체로 변환하는 방법
  
    스프링에 기본으로 내장된 JSON 라이브러리인 jackson을 사용한 예제
  
    ```java
    // JSON을 파싱하여 만들 객체 클래스
    @Getter
    @Setter
    public class HelloData {
      private String userName;
      private int age;
    }
    ```
  
    ```java
    // messageBody는 ServletInputStream을 이용해 문자열로 변환된 상태
    ObjectMapper objectMapper = new ObjectMapper();
    HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);
    ```



### HTTP 응답 데이터

- `HttpServletResponse` 객체 기준
  - `setHeader(name, value)`를 이용해 헤더를 설정할 수 있다.
  - `setContentType()`, `setCharacterEncoding()`을 이용해 응답 데이터의 형식을 결정한다.	
  - `addCookie()`를 통해 쿠키를 삽입할 수 있다.
    - javax.servlet.http에 속한 Cookie 객체를 전달한다.
  - `setStatus()`를 이용해 응답 상태코드를 설정할 수 있다.
  - `sendRedirect()`를 이용해 해당 URI로 리다이렉션을 요청할 수 있다.
- HTTP 응답으로 HTML을 반환할 때는 content-type을 `text/html`로 지정한다.

- jackson을 사용해서 JSON 형태로 응답하는 방법
  - content-type을 `application-json`으로 지정한다.
  - ObjectMapper 객체의 `writeValueAsString()` 메소드를 이용해서 객체를 문자열로 변환시켜서 response에 추가한다.



## References

- [스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)