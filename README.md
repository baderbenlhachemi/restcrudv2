# restcrudv2

RESTful API developed using Java and Spring Boot, with a focus on global exception handling using @ControllerAdvice.

## Exception Handling

![image](https://github.com/baderbenlhachemi/restcrudv2/assets/88034249/09d84b3b-1d98-4395-8ac8-a7b264720f4d)


The project includes custom exception handling for scenarios where a student is not found. The `StudentNotFoundException` is used for this purpose.

```java
public class StudentNotFoundException extends RuntimeException {
    // constructors
}
```

The `StudentRestExceptionHandler` class handles these exceptions and returns a custom `StudentErrorResponse` object in the response body.

```java
@ControllerAdvice
public class StudentRestExceptionHandler {
    // exception handler methods
}
```

### StudentNotFoundException

The `StudentNotFoundException` is a custom exception class that extends `RuntimeException`. It is thrown when a student with a given ID is not found in the list of students.

```java
package com.example.restcrudv2.exception;

public class StudentNotFoundException extends RuntimeException {

    public StudentNotFoundException(String message) {
        super(message);
    }

    public StudentNotFoundException(String message, Throwable cause) {
        super(message, cause);
    }

    public StudentNotFoundException(Throwable cause) {
        super(cause);
    }

}
```

### StudentErrorResponse

The `StudentErrorResponse` is a custom response body that is returned when an exception is thrown. It contains the HTTP status, error message, and the timestamp of when the error occurred.

```java
package com.example.restcrudv2.exception;

public class StudentErrorResponse {

    private int status;
    private String message;
    private long timeStamp;

    // constructors, getters and setters
}
```

### StudentRestExceptionHandler

The `StudentRestExceptionHandler` is a class annotated with `@ControllerAdvice` which allows it to handle exceptions across the whole application. It contains methods annotated with `@ExceptionHandler` to handle specific exceptions.

```java
package com.example.restcrudv2.exception;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

@ControllerAdvice
public class StudentRestExceptionHandler {

    // Exception handler method
    // Type of response body is StudentErrorResponse
    // Catches StudentNotFoundException
    @ExceptionHandler
    public ResponseEntity<StudentErrorResponse> handleException(StudentNotFoundException exc) {
        // create a StudentErrorResponse
        StudentErrorResponse error = new StudentErrorResponse();

        error.setStatus(404);
        error.setMessage(exc.getMessage());
        error.setTimeStamp(System.currentTimeMillis());

        // return ResponseEntity
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }

    // Add another exception handler to catch any exception (catch all)
    @ExceptionHandler
    public ResponseEntity<StudentErrorResponse> handleException(Exception exc) {
        StudentErrorResponse error = new StudentErrorResponse();

        error.setStatus(HttpStatus.BAD_REQUEST.value());
        error.setMessage(exc.getMessage());
        error.setTimeStamp(System.currentTimeMillis());

        return new ResponseEntity<>(error, HttpStatus.BAD_REQUEST);
    }

}
```

In the `StudentRestExceptionHandler`, there are two methods to handle exceptions:

- `handleException(StudentNotFoundException exc)`: This method handles `StudentNotFoundException`. It creates a `StudentErrorResponse` with a status of 404, the message from the exception, and the current timestamp. It then returns a `ResponseEntity` with the `StudentErrorResponse` and a status of `HttpStatus.NOT_FOUND`.

- `handleException(Exception exc)`: This method is a catch-all for any other exceptions that might be thrown. It creates a `StudentErrorResponse` with a status of 400, the message from the exception, and the current timestamp. It then returns a `ResponseEntity` with the `StudentErrorResponse` and a status of `HttpStatus.BAD_REQUEST`.
