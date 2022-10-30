# AOP

## 1. AOP란

핵시 로직에 영향을 미치지 않고 공통 모듈을 효과적으로 사용하는 방법이다.

AOP가 사용되는 경우는 다음과 같다.

1. 간단한 메소드 성능 검사

DB에 많은 양의 데이터를 넣고 빼는 등의 배치 작업에 대한 시간 측정

2. 트랜잭션 처리

try-catch 코드 사용은 소스코드를 더욱 복잡하게 만든다.

3. 예외 반환

구조가 좋지 않은 예외가 발생했을 때, 잘 정의되어 있는 예외 계층 구조로 변환하고 싶은 경우

4. 아키텍처 검증 -> 예제를 못 찾음

5. 기타 ( 로깅, 보안 )

## 2. 예외 반환

예외 발생 시 ResponseBody를 특정한 객체 타입으로 미리 지정한 응답코드와 함께 반환하는 AOP.

ExceptionResponse.class
```` bash
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class ExceptionResponse {

  private Integer status; // 응답코드
  private String message; // 에러 메세지
}
````

클라이언트에게 반환되는 ResponseBody의 형태이다.
이때, 각 필드에 대한 getter 메소드를 제공해줘야 한다. 객체를 JSON 형태의 ResponseBody로 매핑하는데 MappingJackson2HttpMessageConverter가 사용된다. 이때 객체의 정보를 가져오기 위해 getter를 사용한다.

ApiException.class

```` bash
@Getter
public class ApiException extends RuntimeException {

  private final ApiExceptionEnum error;

  public ApiException(ApiExceptionEnum e) {
    super(e.getMessage());
    this.error = e;
  }
}
````

런타임 시에 발생하는 오류를 정의하는 객체이다.

ApiExceptionEnum.class

```` bash
@Getter
@ToString
public enum ApiExceptionEnum {

  // General Exception
  BAD_REQUEST_EXCEPTION(HttpStatus.BAD_REQUEST, "요청 변수를 확인해주세요."),
  UNAUTHORIZED_EXCEPTION(HttpStatus.UNAUTHORIZED, "인증이 실패하였습니다."),
  ACCESS_DENIED_EXCEPTION(HttpStatus.FORBIDDEN, "제한된 접근입니다."),
  NOT_FOUND_EXCEPTION(HttpStatus.NOT_FOUND, "요청한 자원이 없습니다."),
  DUPLICATION_VALUE_EXCEPTION(HttpStatus.CONFLICT, "이미 존재하는 값입니다."),
  INTERNAL_SERVER_ERROR(HttpStatus.INTERNAL_SERVER_ERROR, "서버가 응답하지 않습니다."),

  ;

  private final HttpStatus httpStatus;
  private final Integer status;
  private final String message;

  ApiExceptionEnum(HttpStatus httpStatus, String message) {
    this.httpStatus = httpStatus;
    this.status = httpStatus.value();
    this.message = message;
  }
}

````

enum으로 만들어 사용자 정의 오류를 만들어 사용한다.

GlobalExceptionHandler.class

```` bash
@Slf4j
@RestControllerAdvice
public class GlobalExceptionHandler {

  @ExceptionHandler(ApiException.class)
  public ResponseEntity<ExceptionResponse> exceptionHandler(ApiException e) {
    ApiExceptionEnum exception = e.getError();
    log.error(exception.getHttpStatus().getReasonPhrase(), e);

    return ResponseEntity.status(exception.getStatus()).body(
        ExceptionResponse.builder().status(exception.getStatus()).message(exception.getMessage())
            .build()
    );
  }

  @ExceptionHandler(ConstraintViolationException.class)
  public ResponseEntity<ExceptionResponse> validationExceptionHandler(
      ConstraintViolationException e) {
    return ResponseEntity.status(HttpStatus.BAD_REQUEST.value()).body(
        ExceptionResponse.builder().status(HttpStatus.BAD_REQUEST.value()).message(e.getMessage())
            .build()
    );
  }

  @ExceptionHandler(NullPointerException.class)
  public ResponseEntity<ExceptionResponse> notFoundExceptionHandler(
      ConstraintViolationException e) {
    return ResponseEntity.status(HttpStatus.NOT_FOUND).body(
        ExceptionResponse.builder().status(HttpStatus.NOT_FOUND.value())
            .message(e.getMessage()).build());
  }

}

````

ExceptionHandlerExceptionResolver

스프링 디스패처 서블릿의 기본 예외 핸들링 전략 중 하나이다. `@ExceptionHandler` 어노테이션이 붙은 메소드에서 예외처리를 해준다. 이 어노테이션은 controller 혹은 ControllerAdvice에 붙일 수 있다.

해당 프로젝트에서는 RestControllerAdvice에 붙여 모든 컨트롤러에서 발생하는 예외에 대해 공통적으로 처리한다.

`@RestControllerAdvice`는 컨트롤러 클래스에 공통된 기능을 적용하기 위해 AOP를 사용하는 어노테이션이다.

https://hyunminh.github.io/exception-handling/