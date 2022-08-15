# 🙀 exception
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

웹 애플리케이션은 사용자 요청별로 별도의 스레드가 할당되고, 서블릿 컨테이너 안에서 실행된다.
애플리케이션에서 예외가 발생했는데, `try ~ catch` 로 예외를 잡아서 처리하면 아무런 문제가 없다.
만약 예외를 처리하지 못하면???

```text
WAS (여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
```
`Exception` 의 경우에는 서버 내부에서 처리할 수 없는 오류가 발생한 것으로 생각해서 `HTTP 상태코드 500`을 반환한다.

`HttpServletResponse`의 `sendError()`

서블릿 컨테이너에게 오류가 발생했다는 것을 전달 할 수 있다.
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

`response.sendError()` 를 호출하면 `response` 내부에는 오류가 발생했다는 상태를 저장해둔다.

그리고 서블릿 컨테이너는 고객에게 응답 전에 `response` 에 `sendError()` 가 호출되었는지 확인한다.

**호출되었다면 설정한 오류 코드에 맞추어 기본 오류 페이지를 보여준다.**

---
### 오류화면 제공
### 오류페이지 작동 원리
### 필터
### 인터셉터
## 스프링부트
### 오류 페이지1
### 오류 페이지2


