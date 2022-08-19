# 👻 exception
## MVC 2편(김영한)
**목표: 예외 처리와, 오류 페이지를 구현하고 이해하자.**

## 서블릿 예외 처리
### 시작
서블릿은 2가지 방식으로 예외 처리를 지원한다.
* Exception
* response.sendError(HTTP 상태 코드, 오류 메시지)

### Exception(예외)
**자바 직접 실행**

자바의 메인 메서드를 직접 실행하는 경우 `main` 이라는 이름의 스레드 실행한다.
실행 도중에 예외를 잡지 못하고 처음 실행한 `main()` 메서드를 넘어서 예외가 발생하면, 예외 정보를 남기고 스레드는 종료
1. 예외 발생
2. main() 예외 처리 X
3. 스레드 종료

**웹애플리케이션**

**웹 애플리케이션은 사용자 요청별로 별도의 스레드가 할당**되고, 서블릿 컨테이너 안에서 실행된다.
애플리케이션에서 예외가 발생했는데, `try ~ catch` 로 예외를 잡아서 처리하면 아무런 문제가 없다.
만약 예외를 처리하지 못하면???

```text
WAS (여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
```
`Exception` 의 경우에는 서버 내부에서 처리할 수 없는 오류가 발생한 것으로 생각해서 `HTTP 상태코드 500`을 반환한다.
<br><br>

#### HttpServletResponse 의 `sendError()`

**서블릿 컨테이너에게 오류가 발생했다는 것을 전달** 할 수 있다.
* `response.sendError(HTTP 상태 코드)`
* `response.sendError(HTTP 상태 코드, 오류 메시지)`
---

```java
@GetMapping("/error-404")
public void error404(HttpServletResponse response) throws IOException {
    response.sendError(404, "404 오류!");
}
@GetMapping("/error-500")
public void error500(HttpServletResponse response) throws IOException {
    response.sendError(500);
}
```

```text
WAS(sendError 호출 기록 확인) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(response.sendError())
```
1. `response.sendError()` 호출
2. `response` 내부에는 오류가 발생했다는 상태를 저장
3. 서블릿 컨테이너는 고객에게 응답 전에 `response` 에 `sendError()` 가 호출되었는지 확인
4. 호출되었다면 설정한 오류 코드에 맞추어 기본 오류 페이지를 보여준다.

<img alt="img.png" src="img.png"/>

---
### 오류화면 제공
서블릿 컨테이너가 제공하는 기본 예외 처리 화면은.. 좀....😅

서블릿이 제공하는 오류 화면 기능을 사용해보자!

서블릿은 Exception(예외)가 발생해서 서블릿 밖으로 전달되거나 또는 response.sendError()가 호출 되었을 때 깍각의 상황에 맞춘 오류 처리 기능을 제공한다.

스프링 부트가 제공하는 기능을 활용해서 서블릿 오류 페이지를 등록한다.

```java
@Component
public class WebServerCustomizer implements WebServerFactoryCustomizer<ConfigurableWebServerFactory> {
    @Override
    public void customize(ConfigurableWebServerFactory factory) {
        ErrorPage errorPage404 = new ErrorPage(HttpStatus.NOT_FOUND, "/error-page/404");
        ErrorPage errorPage500 = new ErrorPage(HttpStatus.INTERNAL_SERVER_ERROR, "/error-page/500");
        ErrorPage errorPageEx = new ErrorPage(RuntimeException.class, "/error-page/500");

        factory.addErrorPages(errorPage404, errorPage500, errorPageEx);
    }
}
```
* `response.sendError(404)` : `errorPage404` 호출
* `response.sendError(500)` : `errorPage500` 호출
* `RuntimeException` 또는 그 자식 타입의 예외: `errorPageEx` 호출

헤당 오류를 처리할 컨트롤러를 작성한다.

예를 들어서 `RuntimeException` 예외가
발생하면 `errorPageEx` 에서 지정한 `/error-page/500` 이 호출된다.

```java
@Slf4j
@Controller
public class ErrorPageController {
    
     @RequestMapping("/error-page/404")
     public String errorPage404(HttpServletRequest request, HttpServletResponse response) {
         log.info("errorPage 404");
         return "error-page/404";
     }
     
     @RequestMapping("/error-page/500")
     public String errorPage500(HttpServletRequest request, HttpServletResponse response) {
         log.info("errorPage 500");
         return "error-page/500";
     }
}
```
**오류 처리 View**

![img_1.png](img_1.png)

### 오류페이지 작동 원리
서블릿은 `Exception(예외)`가 발생해서 서블릿 밖으로 전달되거나 또는 `response.sendError()`가 호출되었을 때
설정된 오류페이지를 찾는다.

**예외 발생 흐름**
```text
WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
```

**sendError 흐름**
```text
WAS(sendError 호출 기록 확인) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(response.sendError())
```

**오류 페이지 요청 흐름**
```text
WAS(/error-page/500) 다시 요청 -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러(/error-page/500) -> View
```

**예외 발생과 오류 페이지 요청 흐름**
```text
1. WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
2. WAS `/error-page/500` 다시 요청 -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러(/errorpage/500) -> View
```

정리하면 다음과 같다..
1. 예외가 발생해서 WAS까지 전파
2. WAS는 오류 페이지 경로를 찾아서 내부에서 오류 페이지를 호출한다. 
3. 2번으로 오류 페이지 경로로 필터, 서블릿, 인터셉터, 컨트롤러가 모두 다시 호출
<br><br>

**ErrorPageController - 오류 출력**

WAS는 오류페이지를 단순히 다시 요청만 하는 것이 아니라, 오류 정보를 `request`, `attribute`에 추가해서 넘겨준다.
 
필요하면 오류 페이지에 이렇게 전달된 오류 정보를 사용할수 있다.!
```java
@Slf4j
@Controller
public class ErrorPageController {

    @RequestMapping("/error-page/404")
    public String errorPage404(HttpServletRequest request, HttpServletResponse response) {
        log.info("errorPage 404");
        printErrorInfo(request);
        
        return "error-page/404";
    }

    @RequestMapping("/error-page/500")
    public String errorPage500(HttpServletRequest request, HttpServletResponse response) {
        log.info("errorPage 500");
        printErrorInfo(request);
        
        return "error-page/500";
    }

    @RequestMapping(value = "/error-page/500", produces = MediaType.APPLICATION_JSON_VALUE)
    public ResponseEntity<Map<String, Object>> errorPage500Api(
            HttpServletRequest request, HttpServletResponse response) {
        log.info("API errorPage500");

        Map<String, Object> result = new HashMap<>();
        Exception ex = (Exception) request.getAttribute(RequestDispatcher.ERROR_EXCEPTION);
        result.put("status", request.getAttribute(RequestDispatcher.ERROR_STATUS_CODE));
        result.put("message", ex.getMessage());

        Integer statusCode = (Integer) request.getAttribute(RequestDispatcher.ERROR_STATUS_CODE);

        return new ResponseEntity<>(result, HttpStatus.valueOf(statusCode));
    }

    private void printErrorInfo(HttpServletRequest request) {
        /**
         * request.attribute에 서버가 담아준 정보
         * 
         * javax.servlet.error.exception : 예외
         * javax.servlet.error.exception_type : 예외 타입
         * javax.servlet.error.message : 오류 메시지
         * javax.servlet.error.request_uri : 클라이언트 요청 URI
         * javax.servlet.error.servlet_name : 오류가 발생한 서블릿 이름
         * javax.servlet.error.status_code : HTTP 상태 코드
         */
        log.info("ERROR_EXCEPTION: ex=", request.getAttribute(RequestDispatcher.ERROR_EXCEPTION));
        log.info("ERROR_EXCEPTION_TYPE: {}", request.getAttribute(RequestDispatcher.ERROR_EXCEPTION_TYPE));
        log.info("ERROR_MESSAGE: {}", request.getAttribute(RequestDispatcher.ERROR_MESSAGE)); //ex의 경우 NestedServletException 스프링이 한번 감싸서 반환
        log.info("ERROR_REQUEST_URI: {}", request.getAttribute(RequestDispatcher.ERROR_REQUEST_URI));
        log.info("ERROR_SERVLET_NAME: {}", request.getAttribute(RequestDispatcher.ERROR_SERVLET_NAME));
        log.info("ERROR_STATUS_CODE: {}", request.getAttribute(RequestDispatcher.ERROR_STATUS_CODE));
        log.info("dispatchType={}", request.getDispatcherType());
    }
}
```

### 필터
예외 처리에 따른 필터에 대해서 알아보자!

**예외 발생과 오류 페이지 요청 흐름**
```text
1. WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
2. WAS `/error-page/500` 다시 요청 -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러(/errorpage/500) -> View
```
위에서 살펴본것 처럼 **오류가 발생**하면 오류 페이지를 출력하기 위해 **WAS 내부에서 다시 한번 호출이 발생**한다. 

이때 **필터, 서블릿, 인터셉터도 모두 다시 호출**된다. 

그런데 로그인 인증 체크 같은 경우를 생각해보면, 이미 한번 필터나, 인터셉터에서 로그인 체크를 완료했다. 
따라서 서버 내부에서 오류 페이지를 호출한다고 해서 해당 필터나 인터셉트가 한번 더 호출되는 것은 매우 비효율적이다.

결국 **`클라이언트로 부터 발생한 정상 요청`인지, 아니면 `오류 페이지를 출력하기 위한 내부 요청`인지 구분할 수 있어야 한다.** 

`서블릿`은 이런 문제를 해결하기 위해 `DispatcherType` 이라는 추가 정보를 제공한다.
<br><br>

**DispatcherType**
```java
public enum DispatcherType {
     FORWARD, //서블릿에서 다른 서블릿이나 JSP를 호출 할때
     INCLUDE, //서블릿에서 다른ㄹ 서블릿이나 JSP의 결과를 포함할 때
     REQUEST, //클라이언트 요청
     ASYNC,   //서블릿 비동기 호출
     ERROR    //오류 요청
}
```

**DispatcherType 활용**
```java
@Slf4j
public class LogFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        log.info("log filter init");
    }
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        String requestURI = httpRequest.getRequestURI();
        String uuid = UUID.randomUUID().toString();

        try {
            //로그를 출력하는 부분에 request.getDispatcherType() 을 추가
            log.info("REQUEST [{}][{}][{}]", uuid, request.getDispatcherType(), requestURI);
            chain.doFilter(request, response);
        } catch (Exception e) {
            log.info("EXCEPTION {}", e.getMessage());
            throw e;
        } finally {
            //로그를 출력하는 부분에 request.getDispatcherType() 을 추가
            log.info("RESPONSE [{}][{}][{}]", uuid, request.getDispatcherType(), requestURI);
        }
    }
    @Override
    public void destroy() {
        log.info("log filter destroy");
    }
}
```

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    
    @Bean
    public FilterRegistrationBean logFilter() {
        FilterRegistrationBean<Filter> filterRegistrationBean = new FilterRegistrationBean<>();
        filterRegistrationBean.setFilter(new LogFilter());
        filterRegistrationBean.setOrder(1);
        filterRegistrationBean.addUrlPatterns("/*");
        // DispatcherType.REQUEST, DispatcherType.ERROR
        // 이렇게 두 가지를 모두 넣으면 클라이언트 요청은 물론이고, 오류 페이지 요청에서도 필터가 호출
        filterRegistrationBean.setDispatcherTypes(DispatcherType.REQUEST, DispatcherType.ERROR);

        return filterRegistrationBean;
    }
}
```
`filterRegistrationBean.setDispatcherTypes();`

아무것도 넣지 않으면 **기본 값이 `DispatcherType.REQUEST`** 이다. 
즉 **클라이언트의 요청이 있는 경우에만 필터가 적용된다.**

특별히 오류 페이지 경로도 필터를 적용할 것이 아니면, 기본 값을 그대로 사용하면 된다.
물론 **오류 페이지 요청 전용 필터를 적용하고 싶으면 `DispatcherType.ERROR`** 만 지정


### 인터셉터
**인터셉터는** 서블릿이 제공하는 기능이 아니라 스프링이 제공하는 기능이다. 
따라서 **`DispatcherType` 과 무관하게 항상 호출**된다.

대신에 인터셉터는 다음과 같이 요청 경로에 따라서 추가하거나 제외하기 쉽게 되어 있기 때문에, 
이러한 설정을 사용해서 **오류 페이지 경로를 `excludePathPatterns` 를 사용해서 빼주면 된다.**

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    //@Bean
    public FilterRegistrationBean logFilter() {
        FilterRegistrationBean<Filter> filterRegistrationBean = new FilterRegistrationBean<>();
        filterRegistrationBean.setFilter(new LogFilter());
        filterRegistrationBean.setOrder(1);
        filterRegistrationBean.addUrlPatterns("/*");
        filterRegistrationBean.setDispatcherTypes(DispatcherType.REQUEST, DispatcherType.ERROR);

        return filterRegistrationBean;
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LogInterceptor())
                .order(1)
                .addPathPatterns("/**")
                //여기에서 /error-page/** 를 제거하면 error-page/500 같은 내부 호출의 경우에도 인터셉터가 호출
                .excludePathPatterns("/css/**", "/*.ico", "/error", "/error-page/**"); //오류페이지 경로
    }
}
```

```java
@Slf4j
public class LogInterceptor implements HandlerInterceptor {
    public static final String LOG_ID = "logId";

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String requestURI = request.getRequestURI();
        String uuid = UUID.randomUUID().toString();
        log.info("REQUEST [{}] [{}] [{}] [{}] [{}]", uuid, request.getDispatcherType(), requestURI, handler);

        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        log.info("postHandle [{}]", modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        String requestURI = request.getRequestURI();
        String logId = (String)request.getAttribute(LOG_ID);

        log.info("RESPONSE [{}][{}][{}]", logId, request.getDispatcherType(),
                requestURI);
        if (ex != null) {
            log.error("afterCompletion error!!", ex);
        }
    }
}
```

전체 흐름 정리하면 다음과 같다.

**`/hello` 정상 요청**
```text
WAS(/hello, dispatchType=REQUEST) -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러 -> View
```

**`/error-ex` 오류 요청**
* **필터**는 `DispatchType` 으로 중복 호출 제거 ( `dispatchType=REQUEST` )
* **인터셉터**는 경로 정보로 중복 호출 제거( `excludePathPatterns("/error-page/**")` )
```text
1. WAS(/error-ex, dispatchType=REQUEST) -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러
2. WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
3. WAS 오류 페이지 확인
4. WAS(/error-page/500, dispatchType=ERROR) -> 필터(x) -> 서블릿 -> 인터셉터(x) -> 컨트롤러(/error-page/500) -> View
```

## 스프링부트
### BasicErrorController1
앞에서 우리는 예외 처리 페이지를 만들기 위해서 복잡한 과정을 거쳤다.
1. WebServerCustomizer 구현
2. 예외 종류에 따라서 ErrorPage 구현
3. 예외 처리용 컨트롤러 ErrorPageController 구현

### 💫 놀랍게도 스프링은 이러한 모든 기능을 기본으로 제공한다.!! 😍
* `ErrorPage` 를 자동으로 등록한다. 이때 `/error` 라는 경로로 기본 오류 페이지를 설정한다.
  * `new ErrorPage("/error")` , **상태코드와 예외를 설정하지 않으면 기본 오류 페이지로 사용**된다.
  * **서블릿 밖으로 예외**가 발생하거나, **`response.sendError(...)` 가 호출**되면 모든 오류는 **`/error` 를 호출**하게 된다.
* `BasicErrorController` 라는 스프링 컨트롤러를 자동으로 등록한다.
  * `ErrorPage` 에서 등록한 /error 를 매핑해서 처리하는 컨트롤러다.
> `ErrorMvcAutoConfiguration` 이라는 클래스가 오류 페이지를 자동으로 등록하는 역할

스프링 부트가 자동 등록한 `BasicErrorController` 는 이 경로(`/error`)를 기본으로 받는다.

`BasicErrorController` 는 기본적인 로직이 모두 개발되어 있다..!!

개발자는 오류 페이지 화면만 `BasicErrorController` 가 제공하는 룰과 **우선순위에 따라서 등록하면
된다.**

정적 HTML이면 정적 리소스, 뷰 템플릿을 사용해서 동적으로 오류 화면을 만들고 싶으면 뷰 템플릿 경로에 오류 페이지 파일을 만들어서 넣어두기만 하면 된다.
<br><br>

**뷰 선택 우선순위**

`BasicErrorController` 의 처리 순서

_(구체적인 것이 덜 구체적인 것보다 우선순위 높음!)_
1. 뷰 템플릿
  * `resources/templates/error/500.html`
  * `resources/templates/error/5xx.html`
2. 정적 리소스( `static` , `public` )
  * `resources/static/error/400.html`
  * `resources/static/error/404.html`
  * `resources/static/error/4xx.html`
3. 적용 대상이 없을 때 뷰 이름( `error` )
  * `resources/templates/error.html`

![img_2.png](img_2.png)

### BasicErrorController2
`BasicErrorController` 가 제공하는 기본 정보들에 대해서 알아보자.

`BasicErrorController` 컨트롤러는 다음 정보를 `model` 에 담아서 뷰에 전달한다. 
뷰 템플릿은 이 값을 활용해서 출력할 수 있다.

```java
* timestamp: Fri Feb 05 00:00:00 KST 2021
* status: 400
* error: Bad Request
* exception: org.springframework.validation.BindException
* trace: 예외 trace
* message: Validation failed for object='data'. Error count: 1
* errors: Errors(BindingResult)
* path: 클라이언트 요청 경로 (`/hello`)
```

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="utf-8">
</head>
<body>
<div class="container" style="max-width: 600px">
  <div class="py-5 text-center">
    <h2>500 오류 화면 스프링 부트 제공..!</h2>
  </div>
  <div>
    <p>오류 화면 입니다.</p>
  </div>
  <ul>
    <li>오류 정보</li>
    <ul>
      <li th:text="|timetamp: ${timestamp}|"></li>
      <li th:text="|path: ${path}|"></li>
      <li th:text="|status: ${status}|"></li>
      <li th:text="|message: ${message}|"></li>
      <li th:text="|error: ${error}|"></li>
      <li th:text="|exception: ${exception}|"></li>
      <li th:text="|trace: ${trace}|"></li>
    </ul>
  </ul>

  <hr class="my-4">
</div> <!-- /container -->
</body>
</html>
```

오류 관련 정보들을 고객에게 노출하는 것은 좋지 않다.

그래서 `BasicErrorController` 오류 컨트롤러에서 다음 오류 정보를 `model` 에 포함할지 여부 선택할 수 있다.
```properties
server.error.include-exception=true
server.error.include-message=on_param
server.error.include-stacktrace=on_param
server.error.include-binding-errors=on_param

# 오류 처리 화면을 못 찾을 시, 스프링 whitelabel 오류 페이지 적용
server.error.whitelabel.enabled=true
```
* `never` : 사용하지 않음
* `always` :항상 사용
* `on_param` : 파라미터가 있을 때 사용

## API 예외 처리
### 시작
앞서 서블릿 예외처리에 대해서 배웠다. 

그렇다면.
API 예외 처리는 어떻게 해야할까?

HTML 페이지의 경우 4xx, 5xx와 같은 오류 페이지만 있으면 대부분의 문제를 해결할 수 있다.
그런데 API의 경우에는 생각할 내용이 훨씬 많다..!

왜냐하면, 오류페이지의 경우 단순히 고객에게 오류페이지만 보여주고 끝이지만
**API는 각 상황에 맞는 오류 응답 스펙을 정하고, JSON으로 데이터를 내려주어야 한다.**

먼저 서블릿 오류 페이지 방식을 사용해서 API예외를 처리해 보자..!🤗
```java
@Component
public class WebServerCustomizer implements WebServerFactoryCustomizer<ConfigurableWebServerFactory> {
  @Override
  public void customize(ConfigurableWebServerFactory factory) {
    ErrorPage errorPage404 = new ErrorPage(HttpStatus.NOT_FOUND, "/error-page/404");
    ErrorPage errorPage500 = new ErrorPage(HttpStatus.INTERNAL_SERVER_ERROR, "/error-page/500");
    ErrorPage errorPageEx = new ErrorPage(RuntimeException.class, "/error-page/500");

    factory.addErrorPages(errorPage404, errorPage500, errorPageEx);
  }
}
```
`WAS`에 예외가 전달되거나, `response.sendError()` 가 호출되면 위에 등록한 예외 페이지 경로가 호출된다.
<br><br>

**ApiExceptionController - API 예외 컨트롤러**

예외 테스트를 위해 URL에 전달된 id 의 값이 ex 이면 예외가 발생하도록 코드를 심어두었다.

`HTTP Header`에 `Accept` 가 `application/json` 인 것을 반드시 확인`!!!!!!!!!!`
```java
@Slf4j
@RestController
public class ApiExceptionController {

    @GetMapping("/api/members/{id}")
    public MemberDto getMember(@PathVariable("id") String id) {
        if(id.equals("ex")) {
            throw new RuntimeException("잘못된 사용자");
        }
        if(id.equals("bad")) {
            throw new IllegalArgumentException("잘못된 입력 값");
        }
        if(id.equals("user-ex")) {
            throw new UserException("사용자 오류");
        }
        return new MemberDto(id, "hello "+id);
    }

    @Data
    @AllArgsConstructor
    static class MemberDto {
        private String memberId;
        private String name;
    }
}
```

이렇게 코드를 작성한뒤 API를 요청하면,

* 정상의 경우 
  * API로 JSON 형식으로 데이터가 정상반환 된다.
* 오류인 경우
  * HTML 오류 페이지 반환

우리는 웹 브라우저아닌 이상 HTML을 직접 받아서 할 수 있는 것을 별로 없다..
따라서 **`JSON 응답`을 할 수 있도록 수정해야 한다.**

**ErrorPageController - API 응답 추가**
```java
@Slf4j
@Controller
public class ErrorPageController {
    
    @RequestMapping(value = "/error-page/500", produces = MediaType.APPLICATION_JSON_VALUE)
    public ResponseEntity<Map<String, Object>> errorPage500Api(
            HttpServletRequest request, HttpServletResponse response) {
      log.info("API errorPage500");
  
      Map<String, Object> result = new HashMap<>();
      Exception ex = (Exception) request.getAttribute(RequestDispatcher.ERROR_EXCEPTION);
      result.put("status", request.getAttribute(RequestDispatcher.ERROR_STATUS_CODE));
      result.put("message", ex.getMessage());
  
      Integer statusCode = (Integer) request.getAttribute(RequestDispatcher.ERROR_STATUS_CODE);
  
      return new ResponseEntity<>(result, HttpStatus.valueOf(statusCode));
    }
}
```
`produces = MediaType.APPLICATION_JSON_VALUE` 의 뜻은 클라이언트가 요청하는 HTTP Header의
Accept 의 값이 application/json 일 때 해당 메서드가 호출된다는 것이다. 
결국 클라어인트가 받고 싶은 미디어타입이 JSON 이에 이 컨트롤러의 메서드가 호출된다.

`http://localhost:8080/api/members/ex`

![img_3.png](img_3.png)

**동작 순서**
```text
1. WAS(/api/members/ex, Accept: application/json) -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러
2. WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(RuntimeException 예외발생)
3. WAS 오류 페이지 확인
4. WAS(/error-page/500) -> 필터(x) -> 서블릿 -> 인터셉터(x) -> 컨트롤러(/error-page/500, HTTP 메시지 컨버터(ReturnValueHandler))
```
### 스프링 부트 기본 오류 처리
API 예외 처리도 스프링 부트가 제공하는 기본 오류 방식을 사용할 수 있다.

스프링 부트가 제공하는 `BasicErrorController` 코드를 보자.
<br><br>

**BasicErrorController**
```java
@Controller
@RequestMapping("${server.error.path:${error.path:/error}}")
public class BasicErrorController extends AbstractErrorController {

    private final ErrorProperties errorProperties;
  
    public BasicErrorController(ErrorAttributes errorAttributes, ErrorProperties errorProperties,
                                List<ErrorViewResolver> errorViewResolvers) {
        super(errorAttributes, errorViewResolvers);
        Assert.notNull(errorProperties, "ErrorProperties must not be null");
        this.errorProperties = errorProperties;
    }
  
    @RequestMapping(produces = MediaType.TEXT_HTML_VALUE)
    public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
        HttpStatus status = getStatus(request);
        Map<String, Object> model = Collections
                .unmodifiableMap(getErrorAttributes(request, getErrorAttributeOptions(request, MediaType.TEXT_HTML)));
        response.setStatus(status.value());
        ModelAndView modelAndView = resolveErrorView(request, response, status, model);
        
        return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
    }
  
    @RequestMapping
    public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
        HttpStatus status = getStatus(request);
        
        if (status == HttpStatus.NO_CONTENT) {
            return new ResponseEntity<>(status);
        }
        Map<String, Object> body = getErrorAttributes(request, getErrorAttributeOptions(request, MediaType.ALL));
        
        return new ResponseEntity<>(body, status);
    }
  
    @ExceptionHandler(HttpMediaTypeNotAcceptableException.class)
    public ResponseEntity<String> mediaTypeNotAcceptable(HttpServletRequest request) {
        HttpStatus status = getStatus(request);
        
        return ResponseEntity.status(status).build();
    }
}
```
`@RequestMapping("${server.error.path:${error.path:/error}}")`

`/error` 동일한 경로를 처리하는 `errorHtml()`, `error()` 두 메서드를 확인할 수 있다.

* `errorHtml()` : `produces = MediaType.TEXT_HTML_VALUE` 클라이언트 요청의 Accept 해더 값이 `text/html` 인 경우에는 `errorHtml()`을 호출해서 `view`를 제공한다.
* `error()` : 그외 경우에 호출되고 `ResponseEntity` 로 `HTTP Body` 에 `JSON 데이터` 를 반환한다.

<br><br>

**스프링 부트의 예외 처리**

앞서 학습했듯이 스프링 부트의 기본 설정은 오류 발생시 `/error` 를 오류 페이지로 요청한다.
`BasicErrorController` 는 이 경로를 기본으로 받는다. ( `server.error.path 로 수정 가능, 기본 경로 /error` )

`GET http://localhost:8080/api/members/ex`

**주의**

`BasicErrorController` 를 사용하도록 `WebServerCustomizer` 의 `@Component` 를 주석처리 하자.

![img_4.png](img_4.png)

스프링 부트는 `BasicErrorController` 가 제공하는 기본 정보들을 활용해서 오류 API를 생성해준다.

다음 옵션들을 설정하면 더 자세한 오류 정보를 추가할 수 있다.
```properties
server.error.include-binding-errors=always
server.error.include-exception=true
server.error.include-message=always
server.error.include-stacktrace=always
```

**API 예외 처리는 @ExceptionHandler 를 사용하자!**

`BasicErrorController` 는 HTML 페이지를 제공하는 경우에는 매우 편리하다.
`4xx`, `5xx` 등등 모두 잘 처리해준다. 

그런데 API 오류 처리는 다른 차원의 이야기이다. API 마다, 각각의 컨트롤러나 예외마다 서로 다른 응답 결과를 출력해야 할 수도 있다. 

예를 들어서 회원과 관련된 API에서 예외가 발생할 때 응답과, 상품과 관련된 API에서 발생하는 예외에 따라 그 결과가 달라질 수 있다.
결과적으로 매우 세밀하고 복잡하다. 

따라서 이 방법은 HTML 화면을 처리할 때 사용하고, API 오류 처리는 `@ExceptionHandler` 를 사용

* 정리
  * HTML 예외처리 -> `BasicErrorController`
  * API 예외처리 -> `@ExceptionHandler`

### HandlerExceptionResolver 시작
> 오류 메시지, 형식등을 API 마다 다르게 처리하고 싶다.!

예외가 발생해서 서블릿을 넘어 WAS 까지 예외가 절단되면 HTTP 상태코드가 500으로 처리된다.

발생하는 예외에 따라서 400, 404 등등 다른 상태코드로 처리하고 싶다.

즉, 오류 메시지, 형식등을 API 마다 다르게 처리하고 싶다.!

예를 들어서 `IllegalArgumentException을` 처리하지 못해서 
컨트롤러 밖으로 넘어가는 일이 발생하면 HTTP 상태코드를 400으로 처리하고 싶다.

**ApiExceptionController - 수정**
```java
    @GetMapping("/api/members/{id}")
    public MemberDto getMember(@PathVariable("id") String id) {
        if(id.equals("ex")) {
            throw new RuntimeException("잘못된 사용자");
        }
        if(id.equals("bad")) {
            throw new IllegalArgumentException("잘못된 입력 값");
        }
        return new MemberDto(id, "hello "+id);
    }
```
<br>

실행하면 상태코드가 500인 것을 확인 할 수 있다.
```json
{
   "status": 500,
   "error": "Internal Server Error",
   "exception": "java.lang.IllegalArgumentException",
   "path": "/api/members/bad"
}
```
<br><br>

**HandlerExceptionResolver**

> 컨트롤러 밖으로 던져진 예외 해결해줘!!

스프링 MVC는 컨트롤러(핸들러) 밖으로 예외가 던져진 경우 예외를 해결하고, 동작을 새로 정의할 수 있는 방법을 제공한다..!
**컨트롤러 밖으로 던져진 예외를 해결하고, 동작 방식을 변경하고 싶으면 `HandlerExceptionResolver (ExceptionResolver)` 를 사용**하면 된다.

**ExceptionResolver 사용전**
![img_5.png](img_5.png)

**ExceptionResolver 적용 후**
![img_6.png](img_6.png)

**`ExceptionResolver` 적용해도 `postHandle()`은 호출되지 않는다.**

**HandlerExceptionResolver - 인터페이스**
```java
public interface HandlerExceptionResolver {
   ModelAndView resolveException(
           HttpServletRequest request, HttpServletResponse response, 
            Object handler, Exception ex);
}
```
* `handler` : 핸들러(컨트롤러) 정보
* `Exception ex` : 핸들러(컨트롤러)에서 발생한 발생한 예외

`HandlerExceptionResolver` 인터페이스를 상속받아서 `MyHandlerExceptionResolver를` 구현하자.

**MyHandlerExceptionResolver**
```java
@Slf4j
public class MyHandlerExceptionResolver implements HandlerExceptionResolver {
  @Override
  public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
    //ex의 클래스 타입 확인
    if (ex instanceof IllegalArgumentException) {
      try {
        log.info("IllegalArgumentException resolver to 400");
        response.sendError(HttpServletResponse.SC_BAD_REQUEST, ex.getMessage());
        return new ModelAndView();
      } catch (IOException e) {
        log.error("resolver ex", e);
      }
    }

    return null;
  }
}
```
* `HandlerExceptionResolver` 가 `ModelAndView` 를 반환하는 이유는 
마치 `try ~ catch` 를 하듯이, `Exception` 을 처리해서 정상 흐름 처럼 변경하는 것이 목적이다.
  * `IllegalArgumentException` 이 발생하면 `response.sendError(400)` 를 호출해서 HTTP
    상태 코드를 400으로 지정하고, 빈 ModelAndView 를 반환
<br><br>

**`HandlerExceptionResolver` 의 반환 값에 따른 `DispatcherServlet` 의 동작 방식**

* `빈 ModelAndView`: `new ModelAndView()` 처럼 빈 `ModelAndView` 를 반환하면 뷰를 렌더링 하지 않고, 정상 흐름으로 서블릿이 리턴
* `ModelAndView 지정`: `ModelAndView` 에 `View`, `Model` 등의 정보를 지정해서 반환하면 뷰를 렌더링 한다.
* `null`: : `null` 을 반환하면, 다음 `ExceptionResolver` 를 찾아서 실행한다. 만약 처리할 수 있는
  `ExceptionResolver` 가 없으면 예외 처리가 안되고, 기존에 발생한 예외를 서블릿 밖으로 던진다.

### HandlerExceptionResolver 활용

### ExceptionResolver1
### ExceptionResolver2
### @ExceptionHandler
### @ControllerAdvice
