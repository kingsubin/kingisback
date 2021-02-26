# Validation
애플리케이션 개발 시, 데이터 유효성을 검사하는 것은 애플리케이션 전체에서 발생한다.
입력값 검증 실패에 대해 원인을 쉽게 파악하고 이해하기 쉽게 적절한 API 응답을 해야한다.
목표를 달성하기 위해 Java의 데이터 유효성 검사 표준 기술인 Bean Validation을 사용한다.

**문제점**


1. 애플리케이션 전체에 분산되어 있음
2. 코드 중복이 심하다.
3. 비즈니스 로직에 섞여있어 검사 로직 추적이 어렵고 복잡해진다.


**해결법**
Java 에서는 Bean Validation 이라는 데이터 유효성 검사 프레임워크를 제공한다.
Bean Validation은 문제들을 해결하기 위해 다양한 제약을 도메인 모델에 애노테이션으로 정의할 수 있게한다.
이 제약을 유효성 검사가 필요한 객체에 직접 정의하는 방법으로 기존 유효성 검사 로직의 문제를 해결한다.


**Spring에서 사용하기**
org.springframework.validation.Validator 는 spring 에서 제공하는 객체 검증용 인터페이스이다.
주로 spring mvc 에서 사용하지만 web 어떠한 계층과도 관계가 없으며 모든 계층에서 사용해도 좋다.


**Bean Validation**
자바 표준 스펙
애노테이션으로 빈에있는 데이터를 검증하는 기능


**Validator 인터페이스 메소드**
```java
 boolean supports(Class<?> class) {
	// 어떤 타입의 객체를 검증할 때 사용할 것인지 결정
}

 void validate(Object target, Errors errors) {
	// 실제 검증 로직
}
```


**Validatior 구현방법**
```java
 @Getter @Setter
 public class Event {
    Integer id;
    String title;
}
```


```java
 import org.springframework.validation.Errors;
 import org.springframework.validation.ValidationUtils;
 import org.springframework.validation.Validator;
 
 public class EventValidator implements Validator {
 
    @Override
    public boolean supports(Class<?> clazz) {
        return Event.class.equals(clazz);
    }
 
    @Override
    public void validate(Object target, Errors errors) {
        ValidationUtils.rejectIfEmptyOrWhitespace(errors, "title", "notempty", "Empty title is not allowed");
    }
}
```


validate() 는 ValidationUtils 를 사용하여 구현할 수 있다.


```java
@Component
public class AppRunner implements ApplicationRunner {
 
    @Override
    public void run(ApplicationArguments args) throws Exception {
        Event event = new Event();
 
        Errors errors = new BeanPropertyBindingResult(event, "event");
 
        EventValidator eventValidator = new EventValidator();
        eventValidator.validate(event, errors);
 
        System.out.println("hasErrors(): " + errors.hasErrors());
 
        errors.getAllErrors().forEach( e -> {
            System.out.println("=== Error Code ===");
            Arrays.stream(e.getCodes()).forEach(System.out::println);
            System.out.println(e.getDefaultMessage());
        });
    }
    
    // hasErrors(): true
    // notempty.event.title
    // notempty.title
    // notempty.java.lang.String
    // notempty
    // Empty title is not allowed
}

```


Errors 인스턴스 생성시 사용한 구현체 BeanPropertyBindingResult 는 spring mvc 사용시 자동으로 생성되어 전달하므로 실제로 이 구현체를 직접 사용할 경우는 거의 없다.

validate 메소드 구현시 ValidationUtils 만 사용해야하는 것은 아니고 아래와 같이 사용할 수 있다.
rejectValue() 의 파라미터로는 fileds, errormessage, default errormessage 가 들어간다.


```java
public class EventValidator implements Validator {
 
    @Override
    public boolean supports(Class<?> clazz) {
        return Event.class.equals(clazz);
    }⁄
 
    @Override
    public void validate(Object target, Errors errors) {
        Event event = (Event) target;
        if (event.getTitle() == null) {
            errors.rejectValue("title", "notempty", "Empty title is not allowed");
        }
    }
}
```

Spring 에서는 Validator 를 직접 구현하기보다는 spring 이 제공하는 LocalValidatorFactoryBean 을 사용하는데 이 클래스는 Bean Validation 애노테이션을 지원한다.
에러 발생시 각 에러마다 LocalValidatorFactoryBean 이 에러코드와 디폴트메시지를 제공해준다.

```java
@Notempty
@Min
@Email
...
```

Bean Validation 애노테이션으로 검증할 수 있는 것들은 Validator 를 직접 구현하지 않고도 LocalValidationFactoryBean 을 사용해서 validation 처리를 할 수 있다.
복잡한 비즈니스 로직으로 validation 해야하는 경우에 Validator 를 구현한다.

* * *

스프링에서의 Validation 사용 튜토리얼
[validation getting started](https://spring.io/guides/gs/validating-form-input/)




