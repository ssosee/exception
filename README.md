# π» exception
## MVC 2νΈ(κΉμν)
**λͺ©ν: μμΈ μ²λ¦¬μ, μ€λ₯ νμ΄μ§λ₯Ό κ΅¬ννκ³  μ΄ν΄νμ.**

## μλΈλ¦Ώ μμΈ μ²λ¦¬
### μμ
μλΈλ¦Ώμ 2κ°μ§ λ°©μμΌλ‘ μμΈ μ²λ¦¬λ₯Ό μ§μνλ€.
* Exception
* response.sendError(HTTP μν μ½λ, μ€λ₯ λ©μμ§)

### Exception(μμΈ)
**μλ° μ§μ  μ€ν**

μλ°μ λ©μΈ λ©μλλ₯Ό μ§μ  μ€ννλ κ²½μ° `main` μ΄λΌλ μ΄λ¦μ μ€λ λ μ€ννλ€.
μ€ν λμ€μ μμΈλ₯Ό μ‘μ§ λͺ»νκ³  μ²μ μ€νν `main()` λ©μλλ₯Ό λμ΄μ μμΈκ° λ°μνλ©΄, μμΈ μ λ³΄λ₯Ό λ¨κΈ°κ³  μ€λ λλ μ’λ£
1. μμΈ λ°μ
2. main() μμΈ μ²λ¦¬ X
3. μ€λ λ μ’λ£

**μΉμ νλ¦¬μΌμ΄μ**

**μΉ μ νλ¦¬μΌμ΄μμ μ¬μ©μ μμ²­λ³λ‘ λ³λμ μ€λ λκ° ν λΉ**λκ³ , μλΈλ¦Ώ μ»¨νμ΄λ μμμ μ€νλλ€.
μ νλ¦¬μΌμ΄μμμ μμΈκ° λ°μνλλ°, `try ~ catch` λ‘ μμΈλ₯Ό μ‘μμ μ²λ¦¬νλ©΄ μλ¬΄λ° λ¬Έμ κ° μλ€.
λ§μ½ μμΈλ₯Ό μ²λ¦¬νμ§ λͺ»νλ©΄???

```text
WAS (μ¬κΈ°κΉμ§ μ ν) <- νν° <- μλΈλ¦Ώ <- μΈν°μν° <- μ»¨νΈλ‘€λ¬(μμΈλ°μ)
```
`Exception` μ κ²½μ°μλ μλ² λ΄λΆμμ μ²λ¦¬ν  μ μλ μ€λ₯κ° λ°μν κ²μΌλ‘ μκ°ν΄μ `HTTP μνμ½λ 500`μ λ°ννλ€.
<br><br>

#### HttpServletResponse μ `sendError()`

**μλΈλ¦Ώ μ»¨νμ΄λμκ² μ€λ₯κ° λ°μνλ€λ κ²μ μ λ¬** ν  μ μλ€.
* `response.sendError(HTTP μν μ½λ)`
* `response.sendError(HTTP μν μ½λ, μ€λ₯ λ©μμ§)`
---

```java
@GetMapping("/error-404")
public void error404(HttpServletResponse response) throws IOException {
    response.sendError(404, "404 μ€λ₯!");
}
@GetMapping("/error-500")
public void error500(HttpServletResponse response) throws IOException {
    response.sendError(500);
}
```

```text
WAS(sendError νΈμΆ κΈ°λ‘ νμΈ) <- νν° <- μλΈλ¦Ώ <- μΈν°μν° <- μ»¨νΈλ‘€λ¬(response.sendError())
```
1. `response.sendError()` νΈμΆ
2. `response` λ΄λΆμλ μ€λ₯κ° λ°μνλ€λ μνλ₯Ό μ μ₯
3. μλΈλ¦Ώ μ»¨νμ΄λλ κ³ κ°μκ² μλ΅ μ μ `response` μ `sendError()` κ° νΈμΆλμλμ§ νμΈ
4. νΈμΆλμλ€λ©΄ μ€μ ν μ€λ₯ μ½λμ λ§μΆμ΄ κΈ°λ³Έ μ€λ₯ νμ΄μ§λ₯Ό λ³΄μ¬μ€λ€.

<img alt="img.png" src="img.png"/>

---
### μ€λ₯νλ©΄ μ κ³΅
μλΈλ¦Ώ μ»¨νμ΄λκ° μ κ³΅νλ κΈ°λ³Έ μμΈ μ²λ¦¬ νλ©΄μ.. μ’....π

μλΈλ¦Ώμ΄ μ κ³΅νλ μ€λ₯ νλ©΄ κΈ°λ₯μ μ¬μ©ν΄λ³΄μ!

μλΈλ¦Ώμ Exception(μμΈ)κ° λ°μν΄μ μλΈλ¦Ώ λ°μΌλ‘ μ λ¬λκ±°λ λλ response.sendError()κ° νΈμΆ λμμ λ κΉκ°μ μν©μ λ§μΆ μ€λ₯ μ²λ¦¬ κΈ°λ₯μ μ κ³΅νλ€.

μ€νλ§ λΆνΈκ° μ κ³΅νλ κΈ°λ₯μ νμ©ν΄μ μλΈλ¦Ώ μ€λ₯ νμ΄μ§λ₯Ό λ±λ‘νλ€.

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
* `response.sendError(404)` : `errorPage404` νΈμΆ
* `response.sendError(500)` : `errorPage500` νΈμΆ
* `RuntimeException` λλ κ·Έ μμ νμμ μμΈ: `errorPageEx` νΈμΆ

ν€λΉ μ€λ₯λ₯Ό μ²λ¦¬ν  μ»¨νΈλ‘€λ¬λ₯Ό μμ±νλ€.

μλ₯Ό λ€μ΄μ `RuntimeException` μμΈκ°
λ°μνλ©΄ `errorPageEx` μμ μ§μ ν `/error-page/500` μ΄ νΈμΆλλ€.

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
**μ€λ₯ μ²λ¦¬ View**

![img_1.png](img_1.png)

### μ€λ₯νμ΄μ§ μλ μλ¦¬
μλΈλ¦Ώμ `Exception(μμΈ)`κ° λ°μν΄μ μλΈλ¦Ώ λ°μΌλ‘ μ λ¬λκ±°λ λλ `response.sendError()`κ° νΈμΆλμμ λ
μ€μ λ μ€λ₯νμ΄μ§λ₯Ό μ°Ύλλ€.

**μμΈ λ°μ νλ¦**
```text
WAS(μ¬κΈ°κΉμ§ μ ν) <- νν° <- μλΈλ¦Ώ <- μΈν°μν° <- μ»¨νΈλ‘€λ¬(μμΈλ°μ)
```

**sendError νλ¦**
```text
WAS(sendError νΈμΆ κΈ°λ‘ νμΈ) <- νν° <- μλΈλ¦Ώ <- μΈν°μν° <- μ»¨νΈλ‘€λ¬(response.sendError())
```

**μ€λ₯ νμ΄μ§ μμ²­ νλ¦**
```text
WAS(/error-page/500) λ€μ μμ²­ -> νν° -> μλΈλ¦Ώ -> μΈν°μν° -> μ»¨νΈλ‘€λ¬(/error-page/500) -> View
```

**μμΈ λ°μκ³Ό μ€λ₯ νμ΄μ§ μμ²­ νλ¦**
```text
1. WAS(μ¬κΈ°κΉμ§ μ ν) <- νν° <- μλΈλ¦Ώ <- μΈν°μν° <- μ»¨νΈλ‘€λ¬(μμΈλ°μ)
2. WAS `/error-page/500` λ€μ μμ²­ -> νν° -> μλΈλ¦Ώ -> μΈν°μν° -> μ»¨νΈλ‘€λ¬(/errorpage/500) -> View
```

μ λ¦¬νλ©΄ λ€μκ³Ό κ°λ€..
1. μμΈκ° λ°μν΄μ WASκΉμ§ μ ν
2. WASλ μ€λ₯ νμ΄μ§ κ²½λ‘λ₯Ό μ°Ύμμ λ΄λΆμμ μ€λ₯ νμ΄μ§λ₯Ό νΈμΆνλ€. 
3. 2λ²μΌλ‘ μ€λ₯ νμ΄μ§ κ²½λ‘λ‘ νν°, μλΈλ¦Ώ, μΈν°μν°, μ»¨νΈλ‘€λ¬κ° λͺ¨λ λ€μ νΈμΆ
<br><br>

**ErrorPageController - μ€λ₯ μΆλ ₯**

WASλ μ€λ₯νμ΄μ§λ₯Ό λ¨μν λ€μ μμ²­λ§ νλ κ²μ΄ μλλΌ, μ€λ₯ μ λ³΄λ₯Ό `request`, `attribute`μ μΆκ°ν΄μ λκ²¨μ€λ€.
 
νμνλ©΄ μ€λ₯ νμ΄μ§μ μ΄λ κ² μ λ¬λ μ€λ₯ μ λ³΄λ₯Ό μ¬μ©ν μ μλ€.!
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
         * request.attributeμ μλ²κ° λ΄μμ€ μ λ³΄
         * 
         * javax.servlet.error.exception : μμΈ
         * javax.servlet.error.exception_type : μμΈ νμ
         * javax.servlet.error.message : μ€λ₯ λ©μμ§
         * javax.servlet.error.request_uri : ν΄λΌμ΄μΈνΈ μμ²­ URI
         * javax.servlet.error.servlet_name : μ€λ₯κ° λ°μν μλΈλ¦Ώ μ΄λ¦
         * javax.servlet.error.status_code : HTTP μν μ½λ
         */
        log.info("ERROR_EXCEPTION: ex=", request.getAttribute(RequestDispatcher.ERROR_EXCEPTION));
        log.info("ERROR_EXCEPTION_TYPE: {}", request.getAttribute(RequestDispatcher.ERROR_EXCEPTION_TYPE));
        log.info("ERROR_MESSAGE: {}", request.getAttribute(RequestDispatcher.ERROR_MESSAGE)); //exμ κ²½μ° NestedServletException μ€νλ§μ΄ νλ² κ°μΈμ λ°ν
        log.info("ERROR_REQUEST_URI: {}", request.getAttribute(RequestDispatcher.ERROR_REQUEST_URI));
        log.info("ERROR_SERVLET_NAME: {}", request.getAttribute(RequestDispatcher.ERROR_SERVLET_NAME));
        log.info("ERROR_STATUS_CODE: {}", request.getAttribute(RequestDispatcher.ERROR_STATUS_CODE));
        log.info("dispatchType={}", request.getDispatcherType());
    }
}
```

### νν°
μμΈ μ²λ¦¬μ λ°λ₯Έ νν°μ λν΄μ μμλ³΄μ!

**μμΈ λ°μκ³Ό μ€λ₯ νμ΄μ§ μμ²­ νλ¦**
```text
1. WAS(μ¬κΈ°κΉμ§ μ ν) <- νν° <- μλΈλ¦Ώ <- μΈν°μν° <- μ»¨νΈλ‘€λ¬(μμΈλ°μ)
2. WAS `/error-page/500` λ€μ μμ²­ -> νν° -> μλΈλ¦Ώ -> μΈν°μν° -> μ»¨νΈλ‘€λ¬(/errorpage/500) -> View
```
μμμ μ΄ν΄λ³Έκ² μ²λΌ **μ€λ₯κ° λ°μ**νλ©΄ μ€λ₯ νμ΄μ§λ₯Ό μΆλ ₯νκΈ° μν΄ **WAS λ΄λΆμμ λ€μ νλ² νΈμΆμ΄ λ°μ**νλ€. 

μ΄λ **νν°, μλΈλ¦Ώ, μΈν°μν°λ λͺ¨λ λ€μ νΈμΆ**λλ€. 

κ·Έλ°λ° λ‘κ·ΈμΈ μΈμ¦ μ²΄ν¬ κ°μ κ²½μ°λ₯Ό μκ°ν΄λ³΄λ©΄, μ΄λ―Έ νλ² νν°λ, μΈν°μν°μμ λ‘κ·ΈμΈ μ²΄ν¬λ₯Ό μλ£νλ€. 
λ°λΌμ μλ² λ΄λΆμμ μ€λ₯ νμ΄μ§λ₯Ό νΈμΆνλ€κ³  ν΄μ ν΄λΉ νν°λ μΈν°μνΈκ° νλ² λ νΈμΆλλ κ²μ λ§€μ° λΉν¨μ¨μ μ΄λ€.

κ²°κ΅­ **`ν΄λΌμ΄μΈνΈλ‘ λΆν° λ°μν μ μ μμ²­`μΈμ§, μλλ©΄ `μ€λ₯ νμ΄μ§λ₯Ό μΆλ ₯νκΈ° μν λ΄λΆ μμ²­`μΈμ§ κ΅¬λΆν  μ μμ΄μΌ νλ€.** 

`μλΈλ¦Ώ`μ μ΄λ° λ¬Έμ λ₯Ό ν΄κ²°νκΈ° μν΄ `DispatcherType` μ΄λΌλ μΆκ° μ λ³΄λ₯Ό μ κ³΅νλ€.
<br><br>

**DispatcherType**
```java
public enum DispatcherType {
     FORWARD, //μλΈλ¦Ώμμ λ€λ₯Έ μλΈλ¦Ώμ΄λ JSPλ₯Ό νΈμΆ ν λ
     INCLUDE, //μλΈλ¦Ώμμ λ€λ₯ΈγΉ μλΈλ¦Ώμ΄λ JSPμ κ²°κ³Όλ₯Ό ν¬ν¨ν  λ
     REQUEST, //ν΄λΌμ΄μΈνΈ μμ²­
     ASYNC,   //μλΈλ¦Ώ λΉλκΈ° νΈμΆ
     ERROR    //μ€λ₯ μμ²­
}
```

**DispatcherType νμ©**
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
            //λ‘κ·Έλ₯Ό μΆλ ₯νλ λΆλΆμ request.getDispatcherType() μ μΆκ°
            log.info("REQUEST [{}][{}][{}]", uuid, request.getDispatcherType(), requestURI);
            chain.doFilter(request, response);
        } catch (Exception e) {
            log.info("EXCEPTION {}", e.getMessage());
            throw e;
        } finally {
            //λ‘κ·Έλ₯Ό μΆλ ₯νλ λΆλΆμ request.getDispatcherType() μ μΆκ°
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
        // μ΄λ κ² λ κ°μ§λ₯Ό λͺ¨λ λ£μΌλ©΄ ν΄λΌμ΄μΈνΈ μμ²­μ λ¬Όλ‘ μ΄κ³ , μ€λ₯ νμ΄μ§ μμ²­μμλ νν°κ° νΈμΆ
        filterRegistrationBean.setDispatcherTypes(DispatcherType.REQUEST, DispatcherType.ERROR);

        return filterRegistrationBean;
    }
}
```
`filterRegistrationBean.setDispatcherTypes();`

μλ¬΄κ²λ λ£μ§ μμΌλ©΄ **κΈ°λ³Έ κ°μ΄ `DispatcherType.REQUEST`** μ΄λ€. 
μ¦ **ν΄λΌμ΄μΈνΈμ μμ²­μ΄ μλ κ²½μ°μλ§ νν°κ° μ μ©λλ€.**

νΉλ³ν μ€λ₯ νμ΄μ§ κ²½λ‘λ νν°λ₯Ό μ μ©ν  κ²μ΄ μλλ©΄, κΈ°λ³Έ κ°μ κ·Έλλ‘ μ¬μ©νλ©΄ λλ€.
λ¬Όλ‘  **μ€λ₯ νμ΄μ§ μμ²­ μ μ© νν°λ₯Ό μ μ©νκ³  μΆμΌλ©΄ `DispatcherType.ERROR`** λ§ μ§μ 


### μΈν°μν°
**μΈν°μν°λ** μλΈλ¦Ώμ΄ μ κ³΅νλ κΈ°λ₯μ΄ μλλΌ μ€νλ§μ΄ μ κ³΅νλ κΈ°λ₯μ΄λ€. 
λ°λΌμ **`DispatcherType` κ³Ό λ¬΄κ΄νκ² ν­μ νΈμΆ**λλ€.

λμ μ μΈν°μν°λ λ€μκ³Ό κ°μ΄ μμ²­ κ²½λ‘μ λ°λΌμ μΆκ°νκ±°λ μ μΈνκΈ° μ½κ² λμ΄ μκΈ° λλ¬Έμ, 
μ΄λ¬ν μ€μ μ μ¬μ©ν΄μ **μ€λ₯ νμ΄μ§ κ²½λ‘λ₯Ό `excludePathPatterns` λ₯Ό μ¬μ©ν΄μ λΉΌμ£Όλ©΄ λλ€.**

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
                //μ¬κΈ°μμ /error-page/** λ₯Ό μ κ±°νλ©΄ error-page/500 κ°μ λ΄λΆ νΈμΆμ κ²½μ°μλ μΈν°μν°κ° νΈμΆ
                .excludePathPatterns("/css/**", "/*.ico", "/error", "/error-page/**"); //μ€λ₯νμ΄μ§ κ²½λ‘
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

μ μ²΄ νλ¦ μ λ¦¬νλ©΄ λ€μκ³Ό κ°λ€.

**`/hello` μ μ μμ²­**
```text
WAS(/hello, dispatchType=REQUEST) -> νν° -> μλΈλ¦Ώ -> μΈν°μν° -> μ»¨νΈλ‘€λ¬ -> View
```

**`/error-ex` μ€λ₯ μμ²­**
* **νν°**λ `DispatchType` μΌλ‘ μ€λ³΅ νΈμΆ μ κ±° ( `dispatchType=REQUEST` )
* **μΈν°μν°**λ κ²½λ‘ μ λ³΄λ‘ μ€λ³΅ νΈμΆ μ κ±°( `excludePathPatterns("/error-page/**")` )
```text
1. WAS(/error-ex, dispatchType=REQUEST) -> νν° -> μλΈλ¦Ώ -> μΈν°μν° -> μ»¨νΈλ‘€λ¬
2. WAS(μ¬κΈ°κΉμ§ μ ν) <- νν° <- μλΈλ¦Ώ <- μΈν°μν° <- μ»¨νΈλ‘€λ¬(μμΈλ°μ)
3. WAS μ€λ₯ νμ΄μ§ νμΈ
4. WAS(/error-page/500, dispatchType=ERROR) -> νν°(x) -> μλΈλ¦Ώ -> μΈν°μν°(x) -> μ»¨νΈλ‘€λ¬(/error-page/500) -> View
```

## μ€νλ§λΆνΈ
### BasicErrorController1
μμμ μ°λ¦¬λ μμΈ μ²λ¦¬ νμ΄μ§λ₯Ό λ§λ€κΈ° μν΄μ λ³΅μ‘ν κ³Όμ μ κ±°μ³€λ€.
1. WebServerCustomizer κ΅¬ν
2. μμΈ μ’λ₯μ λ°λΌμ ErrorPage κ΅¬ν
3. μμΈ μ²λ¦¬μ© μ»¨νΈλ‘€λ¬ ErrorPageController κ΅¬ν

### π« λλκ²λ μ€νλ§μ μ΄λ¬ν λͺ¨λ  κΈ°λ₯μ κΈ°λ³ΈμΌλ‘ μ κ³΅νλ€.!! π
* `ErrorPage` λ₯Ό μλμΌλ‘ λ±λ‘νλ€. μ΄λ `/error` λΌλ κ²½λ‘λ‘ κΈ°λ³Έ μ€λ₯ νμ΄μ§λ₯Ό μ€μ νλ€.
  * `new ErrorPage("/error")` , **μνμ½λμ μμΈλ₯Ό μ€μ νμ§ μμΌλ©΄ κΈ°λ³Έ μ€λ₯ νμ΄μ§λ‘ μ¬μ©**λλ€.
  * **μλΈλ¦Ώ λ°μΌλ‘ μμΈ**κ° λ°μνκ±°λ, **`response.sendError(...)` κ° νΈμΆ**λλ©΄ λͺ¨λ  μ€λ₯λ **`/error` λ₯Ό νΈμΆ**νκ² λλ€.
* `BasicErrorController` λΌλ μ€νλ§ μ»¨νΈλ‘€λ¬λ₯Ό μλμΌλ‘ λ±λ‘νλ€.
  * `ErrorPage` μμ λ±λ‘ν /error λ₯Ό λ§€νν΄μ μ²λ¦¬νλ μ»¨νΈλ‘€λ¬λ€.
> `ErrorMvcAutoConfiguration` μ΄λΌλ ν΄λμ€κ° μ€λ₯ νμ΄μ§λ₯Ό μλμΌλ‘ λ±λ‘νλ μ­ν 

μ€νλ§ λΆνΈκ° μλ λ±λ‘ν `BasicErrorController` λ μ΄ κ²½λ‘(`/error`)λ₯Ό κΈ°λ³ΈμΌλ‘ λ°λλ€.

`BasicErrorController` λ κΈ°λ³Έμ μΈ λ‘μ§μ΄ λͺ¨λ κ°λ°λμ΄ μλ€..!!

κ°λ°μλ μ€λ₯ νμ΄μ§ νλ©΄λ§ `BasicErrorController` κ° μ κ³΅νλ λ£°κ³Ό **μ°μ μμμ λ°λΌμ λ±λ‘νλ©΄
λλ€.**

μ μ  HTMLμ΄λ©΄ μ μ  λ¦¬μμ€, λ·° ννλ¦Ώμ μ¬μ©ν΄μ λμ μΌλ‘ μ€λ₯ νλ©΄μ λ§λ€κ³  μΆμΌλ©΄ λ·° ννλ¦Ώ κ²½λ‘μ μ€λ₯ νμ΄μ§ νμΌμ λ§λ€μ΄μ λ£μ΄λκΈ°λ§ νλ©΄ λλ€.
<br><br>

**λ·° μ ν μ°μ μμ**

`BasicErrorController` μ μ²λ¦¬ μμ

_(κ΅¬μ²΄μ μΈ κ²μ΄ λ κ΅¬μ²΄μ μΈ κ²λ³΄λ€ μ°μ μμ λμ!)_
1. λ·° ννλ¦Ώ
  * `resources/templates/error/500.html`
  * `resources/templates/error/5xx.html`
2. μ μ  λ¦¬μμ€( `static` , `public` )
  * `resources/static/error/400.html`
  * `resources/static/error/404.html`
  * `resources/static/error/4xx.html`
3. μ μ© λμμ΄ μμ λ λ·° μ΄λ¦( `error` )
  * `resources/templates/error.html`

![img_2.png](img_2.png)

### BasicErrorController2
`BasicErrorController` κ° μ κ³΅νλ κΈ°λ³Έ μ λ³΄λ€μ λν΄μ μμλ³΄μ.

`BasicErrorController` μ»¨νΈλ‘€λ¬λ λ€μ μ λ³΄λ₯Ό `model` μ λ΄μμ λ·°μ μ λ¬νλ€. 
λ·° ννλ¦Ώμ μ΄ κ°μ νμ©ν΄μ μΆλ ₯ν  μ μλ€.

```java
* timestamp: Fri Feb 05 00:00:00 KST 2021
* status: 400
* error: Bad Request
* exception: org.springframework.validation.BindException
* trace: μμΈ trace
* message: Validation failed for object='data'. Error count: 1
* errors: Errors(BindingResult)
* path: ν΄λΌμ΄μΈνΈ μμ²­ κ²½λ‘ (`/hello`)
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
    <h2>500 μ€λ₯ νλ©΄ μ€νλ§ λΆνΈ μ κ³΅..!</h2>
  </div>
  <div>
    <p>μ€λ₯ νλ©΄ μλλ€.</p>
  </div>
  <ul>
    <li>μ€λ₯ μ λ³΄</li>
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

μ€λ₯ κ΄λ ¨ μ λ³΄λ€μ κ³ κ°μκ² λΈμΆνλ κ²μ μ’μ§ μλ€.

κ·Έλμ `BasicErrorController` μ€λ₯ μ»¨νΈλ‘€λ¬μμ λ€μ μ€λ₯ μ λ³΄λ₯Ό `model` μ ν¬ν¨ν μ§ μ¬λΆ μ νν  μ μλ€.
```properties
server.error.include-exception=true
server.error.include-message=on_param
server.error.include-stacktrace=on_param
server.error.include-binding-errors=on_param

# μ€λ₯ μ²λ¦¬ νλ©΄μ λͺ» μ°Ύμ μ, μ€νλ§ whitelabel μ€λ₯ νμ΄μ§ μ μ©
server.error.whitelabel.enabled=true
```
* `never` : μ¬μ©νμ§ μμ
* `always` :ν­μ μ¬μ©
* `on_param` : νλΌλ―Έν°κ° μμ λ μ¬μ©

## API μμΈ μ²λ¦¬
### μμ
μμ μλΈλ¦Ώ μμΈμ²λ¦¬μ λν΄μ λ°°μ λ€. 

κ·Έλ λ€λ©΄.
API μμΈ μ²λ¦¬λ μ΄λ»κ² ν΄μΌν κΉ?

HTML νμ΄μ§μ κ²½μ° 4xx, 5xxμ κ°μ μ€λ₯ νμ΄μ§λ§ μμΌλ©΄ λλΆλΆμ λ¬Έμ λ₯Ό ν΄κ²°ν  μ μλ€.
κ·Έλ°λ° APIμ κ²½μ°μλ μκ°ν  λ΄μ©μ΄ ν¨μ¬ λ§λ€..!

μλνλ©΄, μ€λ₯νμ΄μ§μ κ²½μ° λ¨μν κ³ κ°μκ² μ€λ₯νμ΄μ§λ§ λ³΄μ¬μ£Όκ³  λμ΄μ§λ§
**APIλ κ° μν©μ λ§λ μ€λ₯ μλ΅ μ€νμ μ νκ³ , JSONμΌλ‘ λ°μ΄ν°λ₯Ό λ΄λ €μ£Όμ΄μΌ νλ€.**

λ¨Όμ  μλΈλ¦Ώ μ€λ₯ νμ΄μ§ λ°©μμ μ¬μ©ν΄μ APIμμΈλ₯Ό μ²λ¦¬ν΄ λ³΄μ..!π€
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
`WAS`μ μμΈκ° μ λ¬λκ±°λ, `response.sendError()` κ° νΈμΆλλ©΄ μμ λ±λ‘ν μμΈ νμ΄μ§ κ²½λ‘κ° νΈμΆλλ€.
<br><br>

**ApiExceptionController - API μμΈ μ»¨νΈλ‘€λ¬**

μμΈ νμ€νΈλ₯Ό μν΄ URLμ μ λ¬λ id μ κ°μ΄ ex μ΄λ©΄ μμΈκ° λ°μνλλ‘ μ½λλ₯Ό μ¬μ΄λμλ€.

`HTTP Header`μ `Accept` κ° `application/json` μΈ κ²μ λ°λμ νμΈ`!!!!!!!!!!`
```java
@Slf4j
@RestController
public class ApiExceptionController {

    @GetMapping("/api/members/{id}")
    public MemberDto getMember(@PathVariable("id") String id) {
        if(id.equals("ex")) {
            throw new RuntimeException("μλͺ»λ μ¬μ©μ");
        }
        if(id.equals("bad")) {
            throw new IllegalArgumentException("μλͺ»λ μλ ₯ κ°");
        }
        if(id.equals("user-ex")) {
            throw new UserException("μ¬μ©μ μ€λ₯");
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

μ΄λ κ² μ½λλ₯Ό μμ±νλ€ APIλ₯Ό μμ²­νλ©΄,

* μ μμ κ²½μ° 
  * APIλ‘ JSON νμμΌλ‘ λ°μ΄ν°κ° μ μλ°ν λλ€.
* μ€λ₯μΈ κ²½μ°
  * HTML μ€λ₯ νμ΄μ§ λ°ν

μ°λ¦¬λ μΉ λΈλΌμ°μ μλ μ΄μ HTMLμ μ§μ  λ°μμ ν  μ μλ κ²μ λ³λ‘ μλ€..
λ°λΌμ **`JSON μλ΅`μ ν  μ μλλ‘ μμ ν΄μΌ νλ€.**

**ErrorPageController - API μλ΅ μΆκ°**
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
`produces = MediaType.APPLICATION_JSON_VALUE` μ λ»μ ν΄λΌμ΄μΈνΈκ° μμ²­νλ HTTP Headerμ
Accept μ κ°μ΄ application/json μΌ λ ν΄λΉ λ©μλκ° νΈμΆλλ€λ κ²μ΄λ€. 
κ²°κ΅­ ν΄λΌμ΄μΈνΈκ° λ°κ³  μΆμ λ―Έλμ΄νμμ΄ JSON μ΄μ μ΄ μ»¨νΈλ‘€λ¬μ λ©μλκ° νΈμΆλλ€.

`http://localhost:8080/api/members/ex`

![img_3.png](img_3.png)

**λμ μμ**
```text
1. WAS(/api/members/ex, Accept: application/json) -> νν° -> μλΈλ¦Ώ -> μΈν°μν° -> μ»¨νΈλ‘€λ¬
2. WAS(μ¬κΈ°κΉμ§ μ ν) <- νν° <- μλΈλ¦Ώ <- μΈν°μν° <- μ»¨νΈλ‘€λ¬(RuntimeException μμΈλ°μ)
3. WAS μ€λ₯ νμ΄μ§ νμΈ
4. WAS(/error-page/500) -> νν°(x) -> μλΈλ¦Ώ -> μΈν°μν°(x) -> μ»¨νΈλ‘€λ¬(/error-page/500, HTTP λ©μμ§ μ»¨λ²ν°(ReturnValueHandler))
```
### μ€νλ§ λΆνΈ κΈ°λ³Έ μ€λ₯ μ²λ¦¬
API μμΈ μ²λ¦¬λ μ€νλ§ λΆνΈκ° μ κ³΅νλ κΈ°λ³Έ μ€λ₯ λ°©μμ μ¬μ©ν  μ μλ€.

μ€νλ§ λΆνΈκ° μ κ³΅νλ `BasicErrorController` μ½λλ₯Ό λ³΄μ.
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

`/error` λμΌν κ²½λ‘λ₯Ό μ²λ¦¬νλ `errorHtml()`, `error()` λ λ©μλλ₯Ό νμΈν  μ μλ€.

* `errorHtml()` : `produces = MediaType.TEXT_HTML_VALUE` ν΄λΌμ΄μΈνΈ μμ²­μ Accept ν΄λ κ°μ΄ `text/html` μΈ κ²½μ°μλ `errorHtml()`μ νΈμΆν΄μ `view`λ₯Ό μ κ³΅νλ€.
* `error()` : κ·ΈμΈ κ²½μ°μ νΈμΆλκ³  `ResponseEntity` λ‘ `HTTP Body` μ `JSON λ°μ΄ν°` λ₯Ό λ°ννλ€.

<br><br>

**μ€νλ§ λΆνΈμ μμΈ μ²λ¦¬**

μμ νμ΅νλ―μ΄ μ€νλ§ λΆνΈμ κΈ°λ³Έ μ€μ μ μ€λ₯ λ°μμ `/error` λ₯Ό μ€λ₯ νμ΄μ§λ‘ μμ²­νλ€.
`BasicErrorController` λ μ΄ κ²½λ‘λ₯Ό κΈ°λ³ΈμΌλ‘ λ°λλ€. ( `server.error.path λ‘ μμ  κ°λ₯, κΈ°λ³Έ κ²½λ‘ /error` )

`GET http://localhost:8080/api/members/ex`

**μ£Όμ**

`BasicErrorController` λ₯Ό μ¬μ©νλλ‘ `WebServerCustomizer` μ `@Component` λ₯Ό μ£Όμμ²λ¦¬ νμ.

![img_4.png](img_4.png)

μ€νλ§ λΆνΈλ `BasicErrorController` κ° μ κ³΅νλ κΈ°λ³Έ μ λ³΄λ€μ νμ©ν΄μ μ€λ₯ APIλ₯Ό μμ±ν΄μ€λ€.

λ€μ μ΅μλ€μ μ€μ νλ©΄ λ μμΈν μ€λ₯ μ λ³΄λ₯Ό μΆκ°ν  μ μλ€.
```properties
server.error.include-binding-errors=always
server.error.include-exception=true
server.error.include-message=always
server.error.include-stacktrace=always
```

**API μμΈ μ²λ¦¬λ @ExceptionHandler λ₯Ό μ¬μ©νμ!**

`BasicErrorController` λ HTML νμ΄μ§λ₯Ό μ κ³΅νλ κ²½μ°μλ λ§€μ° νΈλ¦¬νλ€.
`4xx`, `5xx` λ±λ± λͺ¨λ μ μ²λ¦¬ν΄μ€λ€. 

κ·Έλ°λ° API μ€λ₯ μ²λ¦¬λ λ€λ₯Έ μ°¨μμ μ΄μΌκΈ°μ΄λ€. API λ§λ€, κ°κ°μ μ»¨νΈλ‘€λ¬λ μμΈλ§λ€ μλ‘ λ€λ₯Έ μλ΅ κ²°κ³Όλ₯Ό μΆλ ₯ν΄μΌ ν  μλ μλ€. 

μλ₯Ό λ€μ΄μ νμκ³Ό κ΄λ ¨λ APIμμ μμΈκ° λ°μν  λ μλ΅κ³Ό, μνκ³Ό κ΄λ ¨λ APIμμ λ°μνλ μμΈμ λ°λΌ κ·Έ κ²°κ³Όκ° λ¬λΌμ§ μ μλ€.
κ²°κ³Όμ μΌλ‘ λ§€μ° μΈλ°νκ³  λ³΅μ‘νλ€. 

λ°λΌμ μ΄ λ°©λ²μ HTML νλ©΄μ μ²λ¦¬ν  λ μ¬μ©νκ³ , API μ€λ₯ μ²λ¦¬λ `@ExceptionHandler` λ₯Ό μ¬μ©

* μ λ¦¬
  * HTML μμΈμ²λ¦¬ -> `BasicErrorController`
  * API μμΈμ²λ¦¬ -> `@ExceptionHandler`

### HandlerExceptionResolver μμ
> μ€λ₯ λ©μμ§, νμλ±μ API λ§λ€ λ€λ₯΄κ² μ²λ¦¬νκ³  μΆλ€.!

μμΈκ° λ°μν΄μ μλΈλ¦Ώμ λμ΄ WAS κΉμ§ μμΈκ° μ λ¨λλ©΄ HTTP μνμ½λκ° 500μΌλ‘ μ²λ¦¬λλ€.

λ°μνλ μμΈμ λ°λΌμ 400, 404 λ±λ± λ€λ₯Έ μνμ½λλ‘ μ²λ¦¬νκ³  μΆλ€.

μ¦, μ€λ₯ λ©μμ§, νμλ±μ API λ§λ€ λ€λ₯΄κ² μ²λ¦¬νκ³  μΆλ€.!

μλ₯Ό λ€μ΄μ `IllegalArgumentExceptionμ` μ²λ¦¬νμ§ λͺ»ν΄μ 
μ»¨νΈλ‘€λ¬ λ°μΌλ‘ λμ΄κ°λ μΌμ΄ λ°μνλ©΄ HTTP μνμ½λλ₯Ό 400μΌλ‘ μ²λ¦¬νκ³  μΆλ€.

**ApiExceptionController - μμ **
```java
@GetMapping("/api/members/{id}")
public MemberDto getMember(@PathVariable("id") String id) {
    if(id.equals("ex")) {
        throw new RuntimeException("μλͺ»λ μ¬μ©μ");
    }
    if(id.equals("bad")) {
        throw new IllegalArgumentException("μλͺ»λ μλ ₯ κ°");
    }
    return new MemberDto(id, "hello "+id);
}
```
<br>

μ€ννλ©΄ μνμ½λκ° 500μΈ κ²μ νμΈ ν  μ μλ€.

![img_7.png](img_7.png)

<br><br>

**HandlerExceptionResolver**

> μ»¨νΈλ‘€λ¬ λ°μΌλ‘ λμ Έμ§ μμΈ ν΄κ²°ν΄μ€!!

μ€νλ§ MVCλ μ»¨νΈλ‘€λ¬(νΈλ€λ¬) λ°μΌλ‘ μμΈκ° λμ Έμ§ κ²½μ° μμΈλ₯Ό ν΄κ²°νκ³ , λμμ μλ‘ μ μν  μ μλ λ°©λ²μ μ κ³΅νλ€..!
**μ»¨νΈλ‘€λ¬ λ°μΌλ‘ λμ Έμ§ μμΈλ₯Ό ν΄κ²°νκ³ , λμ λ°©μμ λ³κ²½νκ³  μΆμΌλ©΄ `HandlerExceptionResolver (ExceptionResolver)` λ₯Ό μ¬μ©**νλ©΄ λλ€.

**ExceptionResolver μ¬μ©μ **
![img_5.png](img_5.png)

**ExceptionResolver μ μ© ν**
![img_6.png](img_6.png)

**`ExceptionResolver` μ μ©ν΄λ `postHandle()`μ νΈμΆλμ§ μλλ€.**

**HandlerExceptionResolver - μΈν°νμ΄μ€**
```java
public interface HandlerExceptionResolver {
   ModelAndView resolveException(
           HttpServletRequest request, HttpServletResponse response, 
            Object handler, Exception ex);
}
```
* `handler` : νΈλ€λ¬(μ»¨νΈλ‘€λ¬) μ λ³΄
* `Exception ex` : νΈλ€λ¬(μ»¨νΈλ‘€λ¬)μμ λ°μν λ°μν μμΈ

μμΈκ° λ°μνλ©΄ WASκΉμ§ μμΈκ° λμ Έμ§κ³ , WASμμ μ€λ₯ νμ΄μ§ μ λ³΄λ₯Ό μ°Ύμμ λ€μ `/error`λ₯Ό νΈμΆνλ κ³Όμ μ μκ°ν΄λ³΄λ©΄ λλ¬΄ λ³΅μ‘νλ€...
`ExceptionResolver` λ₯Ό νμ©νλ©΄ μμΈκ° λ°μνμ λ μ΄λ° λ³΅μ‘ν κ³Όμ  μμ΄ λ¬Έμ λ₯Ό ν΄κ²° ν μ μλ€.

`HandlerExceptionResolver` μΈν°νμ΄μ€λ₯Ό μμλ°μμ `MyHandlerExceptionResolverλ₯Ό` κ΅¬ννμ.

**MyHandlerExceptionResolver**
```java
@Slf4j
public class MyHandlerExceptionResolver implements HandlerExceptionResolver {
  @Override
  public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
    //exμ ν΄λμ€ νμ νμΈ
    if (ex instanceof IllegalArgumentException) {
      try {
        log.info("IllegalArgumentException resolver to 400");
        //μν μ½λλ₯Ό 400μΌλ‘~
        response.sendError(HttpServletResponse.SC_BAD_REQUEST, ex.getMessage());
        //μ μ νλ¦ μ²λΌ λ³κ²½(λΉ ModelAndView λ°ν)
        return new ModelAndView();
      } catch (IOException e) {
        log.error("resolver ex", e);
      }
    }
    /**
     * nullμ λ°ννλ©΄ λ€μ ExceptionResolver λ₯Ό μ°Ύμμ μ€ν!
     * λ§μ½ λ€μ ExceptionResolver κ° μμΌλ©΄ μμΈμ²λ¦¬κ° μλκ³ , 
     * κΈ°μ‘΄μ λ°μν μμΈλ₯Ό μλΈλ¦Ώ λ°μΌλ‘~
     */
    return null;
  }
}
```
β­οΈ οΈ`HandlerExceptionResolver` κ° `ModelAndView` λ₯Ό λ°ννλ μ΄μ λ 
λ§μΉ `try ~ catch` λ₯Ό νλ―μ΄, `Exception` μ μ²λ¦¬ν΄μ **μ μ νλ¦ μ²λΌ λ³κ²½νλ κ²μ΄ λͺ©μ **μ΄λ€.
  * `IllegalArgumentException` μ΄ λ°μνλ©΄ `response.sendError(400)` λ₯Ό νΈμΆν΄μ HTTP
    μν μ½λλ₯Ό 400μΌλ‘ μ§μ νκ³ , λΉ `ModelAndView` λ₯Ό λ°ν
<br><br>

**β­οΈ `HandlerExceptionResolver` μ λ°ν κ°μ λ°λ₯Έ `DispatcherServlet` μ λμ λ°©μ**

* `λΉ ModelAndView`: `new ModelAndView()` μ²λΌ λΉ `ModelAndView` λ₯Ό λ°ννλ©΄ λ·°λ₯Ό λ λλ§ νμ§ μκ³ , μ μ νλ¦μΌλ‘ μλΈλ¦Ώμ΄ λ¦¬ν΄
* `ModelAndView μ§μ `: `ModelAndView` μ `View`, `Model` λ±μ μ λ³΄λ₯Ό μ§μ ν΄μ λ°ννλ©΄ λ·°λ₯Ό λ λλ§ νλ€.
* `null`: `null` μ λ°ννλ©΄, λ€μ `ExceptionResolver` λ₯Ό μ°Ύμμ μ€ννλ€. λ§μ½ μ²λ¦¬ν  μ μλ
  `ExceptionResolver` κ° μμΌλ©΄ μμΈ μ²λ¦¬κ° μλκ³ , κΈ°μ‘΄μ λ°μν μμΈλ₯Ό μλΈλ¦Ώ λ°μΌλ‘ λμ§λ€.

### HandlerExceptionResolver νμ©
* **μμΈ μν μ½λ λ³ν**
  * μμΈλ₯Ό `response.sendError(xxx)` νΈμΆλ‘ λ³κ²½ν΄μ μλΈλ¦Ώμμ μν μ½λμ λ°λ₯Έ μ€λ₯λ₯Ό μ²λ¦¬νλλ‘ μμ
  * μ΄ν **WAS**λ μλΈλ¦Ώ μ€λ₯ νμ΄μ§λ₯Ό μ°Ύμμ λ΄λΆ νΈμΆ, μλ₯Ό λ€μ΄μ μ€νλ§ λΆνΈκ° κΈ°λ³ΈμΌλ‘ μ€μ ν `/error` κ° νΈμΆλ¨
* **λ·° ννλ¦Ώ μ²λ¦¬**
  * `ModelAndView` μ κ°μ μ±μμ μμΈμ λ°λ₯Έ μλ‘μ΄ μ€λ₯ νλ©΄ λ·° λ λλ§ ν΄μ κ³ κ°μκ² μ κ³΅
* **API μλ΅ μ²λ¦¬**
  * `response.getWriter().println("hello");` μ²λΌ HTTP μλ΅ λ°λμ μ§μ  λ°μ΄ν°λ₯Ό λ£μ΄μ£Όλ
  κ²λ κ°λ₯νλ€. μ¬κΈ°μ **JSON μΌλ‘ μλ΅νλ©΄ API μλ΅ μ²λ¦¬λ₯Ό ν  μ μλ€.**

μ μ΄μ  500μ 400μΌλ‘ λ°κΏλ³΄μ~

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

  //μΆκ°
  @Override
  public void extendHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
    resolvers.add(new MyHandlerExceptionResolver());
  }
}
```
`configureHandlerExceptionResolvers(..)` λ₯Ό μ¬μ©νλ©΄ μ€νλ§μ΄ κΈ°λ³ΈμΌλ‘ λ±λ‘νλ
`ExceptionResolver` κ° μ κ±°λλ―λ‘ μ£Όμ, `extendHandlerExceptionResolvers` λ₯Ό μ¬μ©νμ.

**HTTP μν μ½λ 500μμ 400μΌλ‘ λ°λκ²**μ νμΈ ν  μ μλ€.

![img_8.png](img_8.png)


**API μμΈ μ²λ¦¬ - `HandlerExceptionResolver` νμ©**

λ¨Όμ  μ¬μ©μ μ μ μμΈλ₯Ό νλ μΆκ°νμ.!
```java
public class UserException extends RuntimeException {
    public UserException() {
        super();
    }

    public UserException(String message) {
        super(message);
    }

    public UserException(String message, Throwable cause) {
        super(message, cause);
    }

    public UserException(Throwable cause) {
        super(cause);
    }

    protected UserException(String message, Throwable cause, boolean enableSuppression, boolean writableStackTrace) {
        super(message, cause, enableSuppression, writableStackTrace);
    }
}
```

**ApiExceptionController - μμΈ μΆκ°**
```java
@Slf4j
@RestController
public class ApiExceptionController {

  @GetMapping("/api/members/{id}")
  public MemberDto getMember(@PathVariable("id") String id) {
    if (id.equals("ex")) {
      throw new RuntimeException("μλͺ»λ μ¬μ©μ");
    }
    if (id.equals("bad")) {
      throw new IllegalArgumentException("μλͺ»λ μλ ₯ κ°");
    }
    //μΆκ°(API μμΈ μ²λ¦¬)
    if (id.equals("user-ex")) {
      throw new UserException("μ¬μ©μ μ€λ₯");
    }
    return new MemberDto(id, "hello " + id);
  }

  @Data
  @AllArgsConstructor
  static class MemberDto {
    private String memberId;
    private String name;
  }
}
```

μ΄μ  μ΄ μμΈλ₯Ό μ²λ¦¬νλ `UserHandlerExceptionResolver` λ₯Ό λ§λ€μ΄λ³΄μ.

**UserHandlerExceptionResolver**
```java
@Slf4j
public class UserHandlerExceptionResolver implements HandlerExceptionResolver {

    private final ObjectMapper objectMapper = new ObjectMapper();

    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        try{
            if(ex instanceof UserException) {
                log.info("UserException resolver to 400");
                String acceptHeader = request.getHeader("accept");
                response.setStatus(HttpServletResponse.SC_BAD_REQUEST);
              /**
               * HTTP μμ²­ ν΄λμ ACCEPT κ°μ΄ application/json μ΄λ©΄ JSONμΌλ‘ μ€λ₯λ₯Ό λ΄λ €μ£Όκ³ , 
               * κ·Έ μΈ κ²½μ°μλ error/500μ μλ HTML μ€λ₯ νμ΄μ§λ₯Ό λ³΄μ¬μ€λ€
               */
              if("application/json".equals(acceptHeader)) {
                    Map<String, Object> errorResult = new HashMap<>();
                    errorResult.put("ex", ex.getMessage());
                    errorResult.put("message", ex.getMessage());

                    String result = objectMapper.writeValueAsString(errorResult); //json to String

                    response.setContentType(MediaType.APPLICATION_JSON_VALUE);
                    response.setCharacterEncoding("utf-8");
                    response.getWriter().write(result);

                    return new ModelAndView(); //μμΈλ λ¨Ήμ΄λ²λ¦¬κ³  μ μ νΈμΆλ¨
                }
                else {
                    return new ModelAndView("error/500");
                }
            }
        } catch (IOException e) {
            log.error("resolver ex", e);
        }

        return null;
    }
}
```
HTTP μμ²­ ν΄λμ ACCEPT κ°μ΄ `application/json` μ΄λ©΄ JSONμΌλ‘ μ€λ₯λ₯Ό λ΄λ €μ£Όκ³ , κ·Έ μΈ κ²½μ°μλ
`error/500`μ μλ HTML μ€λ₯ νμ΄μ§λ₯Ό λ³΄μ¬μ€λ€.

**WebConfig**
```java
@Override
public void extendHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
    resolvers.add(new MyHandlerExceptionResolver());
    //μΆκ°
    resolvers.add(new UserHandlerExceptionResolver());
}
```

**ACCEPT : application/json**

![img_9.png](img_9.png)

**ACCEPT : text/html**

![img_10.png](img_10.png)

**μ λ¦¬**
* `ExceptionResolver` λ₯Ό μ¬μ©νλ©΄ μ»¨νΈλ‘€λ¬μμ μμΈκ° λ°μν΄λ `ExceptionResolver` μμ μμΈλ₯Ό
μ²λ¦¬ν΄λ²λ¦°λ€.
* λ°λΌμ μμΈκ° λ°μν΄λ μλΈλ¦Ώ μ»¨νμ΄λκΉμ§ μμΈκ° μ λ¬λμ§ μκ³ , μ€νλ§ MVCμμ μμΈ μ²λ¦¬λ λμ΄
λλ€.
* κ²°κ³Όμ μΌλ‘ WAS μμ₯μμλ μ μ μ²λ¦¬κ° λ κ²μ΄λ€. μ΄λ κ² **μμΈλ₯Ό μ΄κ³³μμ λͺ¨λ μ²λ¦¬ν  μ μλ€λ κ²μ΄
ν΅μ¬**μ΄λ€.


* μλΈλ¦Ώ μ»¨νμ΄λκΉμ§ μμΈκ° μ¬λΌκ°λ©΄ λ³΅μ‘νκ³  μ§μ λΆνκ² μΆκ° νλ‘μΈμ€κ° μ€νλλ€. 
* λ°λ©΄μ `ExceptionResolver` λ₯Ό μ¬μ©νλ©΄ μμΈμ²λ¦¬κ° μλΉν κΉλν΄μ§λ€.

**κ·Έλ°λ° μ§μ  ExceptionResolver λ₯Ό κ΅¬ννλ €κ³  νλ μλΉν λ³΅μ‘νλ€. π­**

### μ€νλ§μ΄ μ κ³΅νλ ExceptionResolver1
μ€νλ§ λΆνΈκ° κΈ°λ³ΈμΌλ‘ μ κ³΅νλ `ExceptionResolver` λ λ€μκ³Ό κ°λ€.

`HandlerExceptionResolverComposite` μ λ€μ μμλ‘ λ±λ‘
1. `ExceptionHandlerExceptionResolver`
2. `ResponseStatusExceptionResolver`
3. `DefaultHandlerExceptionResolver` -> μ°μ  μμκ° κ°μ₯ λ?μ

**π ExceptionHandlerExceptionResolver**

`@ExceptionHandler` μ μ²λ¦¬νλ€. 
API μμΈ μ²λ¦¬λ λλΆλΆ μ΄ κΈ°λ₯μΌλ‘ ν΄κ²°νλ€.

**ResponseStatusExceptionResolver**

HTTP μν μ½λλ₯Ό μ§μ ν΄μ€λ€.

e.g) `@ResponseStatus(value = HttpStatus.NOT_FOUND)`

**DefaultHandlerExceptionResolver**

μ€νλ§ λ΄λΆ **κΈ°λ³Έ μμΈλ₯Ό μ²λ¦¬**νλ€.
<br><br>

λ¨Όμ  κ°μ₯ μ¬μ΄ `ResponseStatusExceptionResolver` λΆν° μμλ΄μλ€~

**ResponseStatusExceptionResolver**
`ResponseStatusExceptionResolver` λ μμΈμ λ°λΌμ HTTP μν μ½λλ₯Ό μ§μ ν΄μ£Όλ μ­ν μ νλ€.

* `@ResponseStatus` κ° λ¬λ €μλ μμΈ
* `ResponseStatusException` μμΈ

`reason` μ `MessageSource` μμ μ°Ύλ κΈ°λ₯λ μ κ³΅

```properties
error.bad=μλͺ»λ μμ²­ μ€λ₯ μλλ€. λ©μμ§ μ¬μ©
```

```java
@ResponseStatus(code = HttpStatus.BAD_REQUEST, reason = "error.bad")
public class BadRequestException extends RuntimeException {
    //ResponseStatusExceptionResolver μ κ±Έλ¦Ό
}
```
`BadRequestException` μμΈκ° μ»¨νΈλ‘€λ¬ λ°μΌλ‘ λμ΄κ°λ©΄ `ResponseStatusExceptionResolver` μμΈκ°
ν΄λΉ μ λΈνμ΄μμ νμΈν΄μ μ€λ₯ μ½λλ₯Ό `HttpStatus.BAD_REQUEST (400)` μΌλ‘ λ³κ²½νκ³ , λ©μμ§λ λ΄λλ€.

`ResponseStatusExceptionResolver` μ½λλ₯Ό νμΈν΄λ³΄λ©΄ κ²°κ΅­ `response.sendError(statusCode, resolvedReason)` λ₯Ό νΈμΆνλ κ²μ νμΈν  μ μλ€.

`sendError(400)` λ₯Ό νΈμΆνκΈ° λλ¬Έμ WASμμ λ€μ μ€λ₯ νμ΄μ§( `/error` )λ₯Ό λ΄λΆ μμ²­νλ€.

```java
@GetMapping("/api/response-status-ex1")
public String responseStatusEx1() {
        throw new BadRequestException();
}
```

![img_11.png](img_11.png)

**ResponseStatusException**

`@ResponseStatus` λ κ°λ°μκ° μ§μ  λ³κ²½ν  μ μλ μμΈμλ μ μ©ν  μ μλ€. 
(μ λΈνμ΄μμ μ§μ  λ£μ΄μΌ νλλ°, λ΄κ° μ½λλ₯Ό μμ ν  μ μλ λΌμ΄λΈλ¬λ¦¬μ μμΈ μ½λ κ°μ κ³³μλ μ μ©ν  μ μλ€.)

μΆκ°λ‘ μ λΈνμ΄μμ μ¬μ©νκΈ° λλ¬Έμ **μ‘°κ±΄μ λ°λΌ λμ μΌλ‘ λ³κ²½νλ κ²λ μ΄λ ΅λ€.** 
μ΄λλ `ResponseStatusException` μμΈλ₯Ό μ¬μ©νλ©΄ λλ€.

```java
@GetMapping("/api/response-status-ex2")
public String responseStatusEx2() {
    throw new ResponseStatusException(HttpStatus.NOT_FOUND, "error.bad", new IllegalArgumentException());
}
```
![img_12.png](img_12.png)

### μ€νλ§μ΄ μ κ³΅νλ ExceptionResolver2
**DefaultHandlerExceptionResolver**

* `DefaultHandlerExceptionResolver` λ μ€νλ§ λ΄λΆμμ λ°μνλ μ€νλ§ μμΈλ₯Ό ν΄κ²°νλ€.
* λνμ μΌλ‘ νλΌλ―Έν° λ°μΈλ© μμ μ νμμ΄ λ§μ§ μμΌλ©΄ λ΄λΆμμ `TypeMismatchException` μ΄
λ°μνλ€.
  * μ΄ κ²½μ° μμΈκ° λ°μνκΈ° λλ¬Έμ κ·Έλ₯ λλ©΄ μλΈλ¦Ώ μ»¨νμ΄λκΉμ§ μ€λ₯κ° μ¬λΌκ°κ³ , κ²°κ³Όμ μΌλ‘ 500 μ€λ₯κ° λ°μνλ€.

κ·Έλ°λ° λ§μλλ€...

**νλΌλ―Έν° λ°μΈλ©μ λλΆλΆ ν΄λΌμ΄μΈνΈκ° HTTP μμ²­ μ λ³΄λ₯Ό μλͺ» νΈμΆν΄μ λ°μνλ λ¬Έμ **μ΄λ€.

* HTTP μμλ μ΄λ° κ²½μ° HTTP μν μ½λ 400μ μ¬μ©νλλ‘ λμ΄ μλ€.
* `DefaultHandlerExceptionResolver` λ μ΄κ²μ 500 μ€λ₯κ° μλλΌ **HTTP μν μ½λ 400 μ€λ₯**λ‘ λ³κ²½νλ€.
* μ€νλ§ λ΄λΆ μ€λ₯λ₯Ό μ΄λ»κ² μ²λ¦¬ν μ§ μ λ§μ λ΄μ©μ΄ μ μλμ΄ μλ€


**μ½λ νμΈ**
`DefaultHandlerExceptionResolver.handleTypeMismatch` λ₯Ό λ³΄λ©΄ λ€μκ³Ό κ°μ μ½λλ₯Ό νμΈν  μ μλ€.

`response.sendError(HttpServletResponse.SC_BAD_REQUEST) (400)`

κ²°κ΅­ `response.sendError()` λ₯Ό ν΅ν΄μ λ¬Έμ λ₯Ό ν΄κ²°νλ€.
**`sendError(400)` λ₯Ό νΈμΆνκΈ° λλ¬Έμ WASμμ λ€μ μ€λ₯ νμ΄μ§( `/error` )λ₯Ό λ΄λΆ μμ²­**νλ€
```java
/**
 * Handle the case when a {@link org.springframework.web.bind.WebDataBinder} conversion error occurs.
 * <p>The default implementation sends an HTTP 400 error, and returns an empty {@code ModelAndView}.
 * Alternatively, a fallback view could be chosen, or the TypeMismatchException could be rethrown as-is.
 * @param ex the TypeMismatchException to be handled
 * @param request current HTTP request
 * @param response current HTTP response
 * @param handler the executed handler
 * @return an empty ModelAndView indicating the exception was handled
 * @throws IOException potentially thrown from {@link HttpServletResponse#sendError}
 */
protected ModelAndView handleTypeMismatch(TypeMismatchException ex,
HttpServletRequest request, HttpServletResponse response, @Nullable Object handler) throws IOException {
  response.sendError(HttpServletResponse.SC_BAD_REQUEST);
  return new ModelAndView();
}
```
**ApiExceptionController - μΆκ°**
```java
@GetMapping("/api/default-handler-ex")
public String defaultException(@RequestParam Integer data) {
    return "ok";
}
```
`Integer data` μ λ¬Έμλ₯Ό μλ ₯νλ©΄ λ΄λΆμμ `TypeMismatchException` μ΄ λ°μνλ€.

![img_13.png](img_13.png)

**μ λ¦¬**

μ§κΈκΉμ§ λ€μ `ExceptionResolver` λ€μ λν΄ μμλ³΄μλ€.
1. `ExceptionHandlerExceptionResolver` λ€μ μκ°μ
2. `ResponseStatusExceptionResolver` HTTP μλ΅ μ½λ λ³κ²½
3. `DefaultHandlerExceptionResolver` μ€νλ§ λ΄λΆ μμΈ μ²λ¦¬

μ§κΈκΉμ§ HTTP μν μ½λλ₯Ό λ³κ²½νκ³ , μ€νλ§ λ΄λΆ μμΈμ μνμ½λλ₯Ό λ³κ²½νλ κΈ°λ₯λ μμλ³΄μλ€.

**κ·Έλ°λ° `HandlerExceptionResolver` λ₯Ό μ§μ  μ¬μ©νκΈ°λ λ³΅μ‘νλ€.** 

1. API μ€λ₯ μλ΅μ κ²½μ° `response` μ μ§μ  λ°μ΄ν°λ₯Ό λ£μ΄μΌ ν΄μ λ§€μ° λΆνΈνκ³  λ²κ±°λ‘­λ€.
2. `ModelAndView` λ₯Ό λ°νν΄μΌ νλ κ²λ APIμλ μ λ§μ§ μλλ€.

μ€νλ§μ μ΄ λ¬Έμ λ₯Ό ν΄κ²°νκΈ° μν΄ `@ExceptionHandler` λΌλ λ§€μ° νμ μ μΈ μμΈ μ²λ¦¬ κΈ°λ₯μ μ κ³΅νλ€.

π κ·Έκ²μ λ°λ‘λ°λ‘λ°λ‘λ°λ‘λ°λ‘ `ExceptionHandlerExceptionResolver` μ΄λ€.

### μ€νλ§μ΄ μ κ³΅νλ @ExceptionHandler
**π HTML νλ©΄ μ€λ₯ vs π‘ API μ€λ₯**

μΉ λΈλΌμ°μ μ `HTML νλ©΄`μ μ κ³΅ν  λλ μ€λ₯κ° λ°μνλ©΄ `BasicErrorController` λ₯Ό μ¬μ©νλκ²
νΈνλ€.

μ΄λλ λ¨μν `5xx`, `4xx` κ΄λ ¨λ μ€λ₯ νλ©΄μ λ³΄μ¬μ£Όλ©΄ λλ€. `BasicErrorController` λ μ΄λ° λ©μ»€λμ¦μ
λͺ¨λ κ΅¬νν΄λμλ€.

κ·Έλ°λ° APIλ κ° μμ€ν λ§λ€ μλ΅μ λͺ¨μλ λ€λ₯΄κ³ , μ€νλ λͺ¨λ λ€λ₯΄λ€. μμΈ μν©μ λ¨μν μ€λ₯ νλ©΄μ
λ³΄μ¬μ£Όλ κ²μ΄ μλλΌ, μμΈμ λ°λΌμ κ°κ° λ€λ₯Έ λ°μ΄ν°λ₯Ό μΆλ ₯ν΄μΌ ν  μλ μλ€. 
κ·Έλ¦¬κ³  κ°μ μμΈλΌκ³  ν΄λ μ΄λ€ μ»¨νΈλ‘€λ¬μμ λ°μνλκ°μ λ°λΌμ λ€λ₯Έ μμΈ μλ΅μ λ΄λ €μ£Όμ΄μΌ ν  μ μλ€. 

νλ§λλ‘ λ§€μ° μΈλ°ν μ μ΄κ° νμνλ€. 
μμ μ΄μΌκΈ°νμ§λ§, μλ₯Ό λ€μ΄μ **μν APIμ μ£Όλ¬Έ APIλ μ€λ₯κ° λ°μνμ λ μλ΅μ λͺ¨μμ΄ μμ ν λ€λ₯Ό μ μλ€.**

**κ²°κ΅­ μ§κΈκΉμ§ μ΄ν΄λ³Έ `BasicErrorController` λ₯Ό μ¬μ©νκ±°λ `HandlerExceptionResolver` λ₯Ό μ§μ 
κ΅¬ννλ λ°©μμΌλ‘ API μμΈλ₯Ό λ€λ£¨κΈ°λ μ½μ§ μλ€.**

**API μμΈμ²λ¦¬μ μ΄λ €μ΄ μ **
* `HandlerExceptionResolver` λ₯Ό λ μ¬λ € λ³΄λ©΄ `ModelAndView` λ₯Ό λ°νν΄μΌ νλ€. 
  * μ΄κ²μ API μλ΅μλ νμνμ§ μλ€.
* API μλ΅μ μν΄μ `HttpServletResponse` μ μ§μ  μλ΅ λ°μ΄ν°λ₯Ό λ£μ΄μ£Όμλ€. 
  * μ΄κ²μ λ§€μ° λΆνΈνλ€.
* μ€νλ§ μ»¨νΈλ‘€λ¬μ λΉμ νλ©΄ λ§μΉ κ³Όκ±° μλΈλ¦Ώμ μ¬μ©νλ μμ λ‘ λμκ° κ² κ°λ€.
* νΉμ  μ»¨νΈλ‘€λ¬μμλ§ λ°μνλ μμΈλ₯Ό λ³λλ‘ μ²λ¦¬νκΈ° μ΄λ ΅λ€. 

μλ₯Ό λ€μ΄μ **νμμ μ²λ¦¬νλ μ»¨νΈλ‘€λ¬μμ λ°μνλ `RuntimeException` μμΈ**μ **μνμ κ΄λ¦¬νλ μ»¨νΈλ‘€λ¬μμ λ°μνλ λμΌν`RuntimeException` μμΈ**λ₯Ό 
**μλ‘ λ€λ₯Έ λ°©μμΌλ‘ μ²λ¦¬**νκ³  μΆλ€λ©΄ μ΄λ»κ² ν΄μΌν κΉ?
<br><br>

**@ExceptionHandler**

μ€νλ§μ API μμΈ μ²λ¦¬ λ¬Έμ λ₯Ό ν΄κ²°νκΈ° μν΄ `@ExceptionHandler` λΌλ μ λΈνμ΄μμ μ¬μ©νλ λ§€μ°
νΈλ¦¬ν μμΈ μ²λ¦¬ κΈ°λ₯μ μ κ³΅νλλ°, μ΄κ²μ΄ λ°λ‘ `ExceptionHandlerExceptionResolver` μ΄λ€.
μ€νλ§μ `ExceptionHandlerExceptionResolver` λ₯Ό κΈ°λ³ΈμΌλ‘ μ κ³΅νκ³ , κΈ°λ³ΈμΌλ‘ μ κ³΅νλ
`ExceptionResolver` μ€μ μ°μ μμλ κ°μ₯ λλ€. 
μ€λ¬΄μμ API μμΈ μ²λ¦¬λ λλΆλΆ μ΄ κΈ°λ₯μ μ¬μ©νλ€.

μμ λ‘ μμλ³΄μ.

API μλ΅ κ°μ²΄ μ μ
```java
@Data
@AllArgsConstructor
public class ErrorResult {
    private String code;
    private String message;
}
```

**ApiExceptionV2Controller**
```java
@Slf4j
@RestController
public class ApiExceptionV2Controller {

    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ExceptionHandler(IllegalArgumentException.class)
    public ErrorResult illegalExHandler(IllegalArgumentException e) {
        log.error("[IllegalArgumentException] ex", e);
        return new ErrorResult("BAD", e.getMessage());
    }

    @ExceptionHandler
    public ResponseEntity<ErrorResult> userExHandler(UserException e) {
         log.error("[UserException] ex", e);
        ErrorResult errorResult = new ErrorResult("USER-EX", e.getMessage());
        return new ResponseEntity<>(errorResult, HttpStatus.BAD_REQUEST);
    }

    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    @ExceptionHandler
    public ErrorResult exHandler(Exception e) {
        log.error("[Exception] ex", e);
        return new ErrorResult("EX", "λ΄λΆ μ€λ₯");
    }

    @GetMapping("/api2/members/{id}")
    public ApiExceptionController.MemberDto getMember(@PathVariable("id") String id) {
        if(id.equals("ex")) {
            throw new RuntimeException("μλͺ»λ μ¬μ©μ");
        }
        if(id.equals("bad")) {
            throw new IllegalArgumentException("μλͺ»λ μλ ₯ κ°");
        }
        if(id.equals("user-ex")) {
            throw new UserException("μ¬μ©μ μ€λ₯");
        }
        return new ApiExceptionController.MemberDto(id, "hello "+id);
    }

    @Data
    @AllArgsConstructor
    static class MemberDto {
        private String memberId;
        private String name;
    }
}
```

**@ExceptionHandler μμΈ μ²λ¦¬ λ°©λ²**

`@ExceptionHandler` μ λΈνμ΄μμ μ μΈνκ³ , ν΄λΉ μ»¨νΈλ‘€λ¬μμ μ²λ¦¬νκ³  μΆμ μμΈλ₯Ό μ§μ ν΄μ£Όλ©΄ λλ€.
ν΄λΉ μ»¨νΈλ‘€λ¬μμ μμΈκ° λ°μνλ©΄ μ΄ λ©μλκ° νΈμΆλλ€.

**μ°Έκ³ λ‘ μ§μ ν μμΈ λλ κ·Έ μμΈμ μμ ν΄λμ€λ λͺ¨λ μ‘μ μ μλ€.**

μλ μ½λλ `IllegalArgumentException.class` λΏλ§ μλλΌ κ·Έ νμ μμ ν΄λμ€ κΉμ§ λͺ¨λ μ²λ¦¬ ν  μ μλ€.
```java
@ExceptionHandler(IllegalArgumentException.class)
public ErrorResult illegalExHandle(IllegalArgumentException e) {
        log.error("[exceptionHandle] ex", e);
        return new ErrorResult("BAD", e.getMessage());
}
```
<br><br>

**μ°μ μμ**

μ€νλ§μ μ°μ μμλ ν­μ μμΈν κ²μ΄ μ°μ κΆμ κ°μ§λ€. μλ₯Ό λ€μ΄μ λΆλͺ¨, μμ ν΄λμ€κ° μκ³  λ€μκ³Ό κ°μ΄
μμΈκ° μ²λ¦¬λλ€.

```java
@ExceptionHandler(λΆλͺ¨μμΈ.class)
public String λΆλͺ¨μμΈμ²λ¦¬()(λΆλͺ¨μμΈ e) {}

@ExceptionHandler(μμμμΈ.class)
public String μμμμΈμ²λ¦¬()(μμμμΈ e) {}
```
1. μμμμΈ λ°μ

μμ μμΈκ° λ°μνλ©΄ λΆλͺ¨, μμ λλ€ νΈμΆ λμμ΄λ€.
κ·Έλ°λ° λμ€ μμΈν κ²μ΄ μ°μ κΆμ κ°μ§λ―λ‘ **μμμμΈκ° νΈμΆ**λλ€.

2. λΆλͺ¨μμΈ λ°μ

λΆλͺ¨μμΈκ° νΈμΆλλ©΄ λΆλͺ¨μμΈλ§ νΈμΆ λμμ΄ λλ―λ‘ **λΆλͺ¨μμΈλ§ νΈμΆ**λλ€.

**λ€μν μμΈ**

λ€μκ³Ό κ°μ΄ **λ€μν μμΈλ₯Ό νλ²μ μ²λ¦¬**ν  μ μλ€.
```java
@ExceptionHandler({AException.class, BException.class})
public String ex(Exception e) {
    log.info("exception e", e);
}
```

**μμΈ μλ΅**

`@ExceptionHandler` μ μμΈλ₯Ό μλ΅ν  μ μλ€. **μλ΅νλ©΄ λ©μλ νλΌλ―Έν°μ μμΈκ° μ§μ **λλ€.
```java
@ExceptionHandler
public ResponseEntity<ErrorResult> userExHandle(UserException e) {}
```

**νλ¦¬λ―Έν°μ μλ΅**

`@ExceptionHandler` μλ λ§μΉ μ€νλ§μ μ»¨νΈλ‘€λ¬μ νλΌλ―Έν° μλ΅μ²λΌ λ€μν νλΌλ―Έν°μ μλ΅μ μ§μ ν  μ μλ€.

κ³΅μ λ©λ΄μΌ
* https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-annexceptionhandler-args

<br><br>

μ μ΄μ  μμμ μμ±ν μ½λλ€μ νν€μ Έ λ³΄μ~

**IllegalArgumentException μ²λ¦¬**
````java
@ResponseStatus(HttpStatus.BAD_REQUEST)
@ExceptionHandler(IllegalArgumentException.class)
public ErrorResult illegalExHandler(IllegalArgumentException e) {
    log.error("[IllegalArgumentException] ex", e);
    return new ErrorResult("BAD", e.getMessage());
}
````
**μ€ν νλ¦**

1. μ»¨νΈλ‘€λ¬λ₯Ό νΈμΆν κ²°κ³Ό `IllegalArgumentException` μμΈκ° μ»¨νΈλ‘€λ¬ λ°μΌλ‘ λμ Έμ§λ€.
2. μμΈκ° λ°μνμΌλ‘ `ExceptionResolver` κ° μλνλ€. 
3. κ°μ₯ μ°μ μμκ° λμ `ExceptionHandlerExceptionResolver` κ° μ€νλλ€.
4. `ExceptionHandlerExceptionResolver` λ ν΄λΉ μ»¨νΈλ‘€λ¬μ `IllegalArgumentException` μ μ²λ¦¬ν 
μ μλ `@ExceptionHandler` κ° μλμ§ νμΈνλ€.
5. `illegalExHandle()` λ₯Ό μ€ννλ€. 
   1. `@RestController` μ΄λ―λ‘ `illegalExHandle()` μλ `@ResponseBody` κ° μ μ©λλ€. 
   2. λ°λΌμ HTTP μ»¨λ²ν°κ° μ¬μ©λκ³ , μλ΅μ΄ λ€μκ³Ό κ°μ `JSON` μΌλ‘ λ°νλλ€.
6. `@ResponseStatus(HttpStatus.BAD_REQUEST)` λ₯Ό μ§μ νμΌλ―λ‘ HTTP μν μ½λ `400`μΌλ‘ μλ΅νλ€.

**UserException μ²λ¦¬**
```java
@ExceptionHandler
public ResponseEntity<ErrorResult> userExHandle(UserException e) {
   log.error("[exceptionHandle] ex", e);
   ErrorResult errorResult = new ErrorResult("USER-EX", e.getMessage());
   return new ResponseEntity<>(errorResult, HttpStatus.BAD_REQUEST);
}
```
* `@ExceptionHandler` μ μμΈλ₯Ό μ§μ νμ§ μμΌλ©΄ ν΄λΉ λ©μλ νλΌλ―Έν° μμΈλ₯Ό μ¬μ©νλ€. μ¬κΈ°μλ `UserException` μ μ¬μ©νλ€.
* `ResponseEntity` λ₯Ό μ¬μ©ν΄μ HTTP λ©μμ§ λ°λμ μ§μ  μλ΅νλ€. λ¬Όλ‘  HTTP μ»¨λ²ν°κ° μ¬μ©λλ€.
  * `ResponseEntity` λ₯Ό μ¬μ©νλ©΄ **HTTP μλ΅ μ½λλ₯Ό νλ‘κ·Έλλ°ν΄μ λμ μΌλ‘ λ³κ²½ν  μ μλ€.** μμ μ΄ν΄λ³Έ
  * `@ResponseStatus` λ μ λΈνμ΄μμ΄λ―λ‘ HTTP μλ΅ μ½λλ₯Ό λμ μΌλ‘ λ³κ²½ν  μ μλ€.

**Exception**
```java
@ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
@ExceptionHandler
public ErrorResult exHandle(Exception e) {
   log.error("[exceptionHandle] ex", e);
   return new ErrorResult("EX", "λ΄λΆ μ€λ₯");
}
```
* `throw new RuntimeException("μλͺ»λ μ¬μ©μ")` μ΄ μ½λκ° μ€νλλ©΄μ, μ»¨νΈλ‘€λ¬ λ°μΌλ‘ `RuntimeException` μ΄ λμ Έμ§λ€.
* `RuntimeException` μ `Exception` μ μμ ν΄λμ€μ΄λ€. λ°λΌμ μ΄ λ©μλκ° νΈμΆλλ€.
* `@ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)` λ‘ HTTP μν μ½λλ₯Ό `500` μΌλ‘ μλ΅νλ€.

**HTML μ€λ₯ νλ©΄**

`ModelAndView` λ₯Ό μ¬μ©ν΄μ μ€λ₯ νλ©΄(HTML)μ μλ΅νλλ° μ¬μ©ν  μλ μλ€
```java
@ExceptionHandler(ViewException.class)
public ModelAndView ex(ViewException e) {
   log.info("exception e", e);
   return new ModelAndView("error");
}
```
### μ€νλ§μ΄ μ κ³΅νλ @ControllerAdvice
`@ExceptionHandler` λ₯Ό μ¬μ©ν΄μ μμΈλ₯Ό κΉλνκ² μ²λ¦¬ν  μ μκ² λμμ§λ§, 
μ μ μ½λμ μμΈ μ²λ¦¬ μ½λκ° νλμ μ»¨νΈλ‘€λ¬μ μμ¬ μλ€. 

**`@ControllerAdvice` λλ `@RestControllerAdvice` λ₯Ό μ¬μ©νλ©΄ `μ μμ½λ`μ `μμΈμ²λ¦¬ μ½λ`λ₯Ό κΉλνκ² `λΆλ¦¬`ν  μ μλ€.**

```java
@Slf4j
@RestControllerAdvice
public class ExController {

    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ExceptionHandler(IllegalArgumentException.class)
    public ErrorResult illegalExHandler(IllegalArgumentException e) {
        log.error("[IllegalArgumentException] ex", e);
        return new ErrorResult("BAD", e.getMessage());
    }

    @ExceptionHandler
    public ResponseEntity<ErrorResult> userExHandler(UserException e) {
        log.error("[UserException] ex", e);
        ErrorResult errorResult = new ErrorResult("USER-EX", e.getMessage());
        return new ResponseEntity<>(errorResult, HttpStatus.BAD_REQUEST);
    }

    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    @ExceptionHandler
    public ErrorResult exHandler(Exception e) {
        log.error("[Exception] ex", e);
        return new ErrorResult("EX", "λ΄λΆ μ€λ₯");
    }
}
```
**ApiExceptionV2Controller μ½λμ μλ @ExceptionHandler λͺ¨λ μ κ±°**
```java
@Slf4j
@RestController
public class ApiExceptionV2Controller {

    @GetMapping("/api2/members/{id}")
    public ApiExceptionController.MemberDto getMember(@PathVariable("id") String id) {
        if(id.equals("ex")) {
            throw new RuntimeException("μλͺ»λ μ¬μ©μ");
        }
        if(id.equals("bad")) {
            throw new IllegalArgumentException("μλͺ»λ μλ ₯ κ°");
        }
        if(id.equals("user-ex")) {
            throw new UserException("μ¬μ©μ μ€λ₯");
        }
        return new ApiExceptionController.MemberDto(id, "hello "+id);
    }

    @Data
    @AllArgsConstructor
    static class MemberDto {
        private String memberId;
        private String name;
    }
}
```

**@ControllerAdvice**

* `@ControllerAdvice` λ λμμΌλ‘ μ§μ ν μ¬λ¬ μ»¨νΈλ‘€λ¬μ `@ExceptionHandler` , `@InitBinder` κΈ°λ₯μ λΆμ¬ν΄μ£Όλ μ­ν μ νλ€.
  * `@InitBinder` ???
    * `Controller`λ‘ λ€μ΄μ€λ μμ²­μ λν΄ μΆκ°μ μΈ μ€μ μ νκ³  μΆμλ μ¬μ©νλ€.
    * λͺ¨λ  μμ²­ μ μ InitBinderλ₯Ό μ μΈν λ©μλκ° μ€ν λλ€.
* `@ControllerAdvice` μ λμμ μ§μ νμ§ μμΌλ©΄ λͺ¨λ  μ»¨νΈλ‘€λ¬μ μ μ©λλ€. (κΈλ‘λ² μ μ©)
* `@RestControllerAdvice` λ `@ControllerAdvice` μ κ°κ³ , `@ResponseBody` κ° μΆκ°λμ΄ μλ€.

μ½κ² μ λ¦¬νλ©΄ `@Controller` ,`@RestController` μ μ°¨μ΄μ κ°κ³  ν  μ μλ€.

**λμ μ»¨νΈλ‘€λ¬ μ§μ  λ°©λ²**
```java
// Target all Controllers annotated with @RestController
@ControllerAdvice(annotations = RestController.class)
public class ExampleAdvice1 {}

// Target all Controllers within specific packages
@ControllerAdvice("org.example.controllers")
public class ExampleAdvice2 {}

// Target all Controllers assignable to specific classes
@ControllerAdvice(assignableTypes = {ControllerInterface.class,
AbstractController.class})
public class ExampleAdvice3 {}
```
**μ°Έκ³ **
* https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-anncontroller-advice

**μ λ¦¬**

`@ExceptionHandler` μ `@ControllerAdvice` λ₯Ό μ‘°ν©νλ©΄ μμΈλ₯Ό κΉλνκ² ν΄κ²°ν  μ μλ€.
