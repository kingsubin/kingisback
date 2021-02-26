# @InitBinder 

- Spring Validator 사용 시 @Valid 애노테이션으로 검증이 필요한 객체를 가져오기 전에 수행할 메소드를 지정해주는 애노테이션.

- Binding ?
파라미터의 수집

```java
@InitBinder
public void initBinder(WebDataBinder webDataBinder){

    // 무시할 필드명 명시
    webDataBinder.setDisallowedFields("id");

	// 허용할 필드명 명시
	webDataBinder.setAllowedFields("id");

    // 특정 Formatter 등록
    webDataBinder.addCustomFormatter();

    // 특정 CustomEditor 등록
    webDataBinder.registerCustomEditor(Date.class, new CustomDateEditor(new ISO8601DateFormat(), false));

    // 특정 Validator 등록
    webDataBinder.addValidators(new EventValidator());
}
```

```java
// passwordForm 객체에만 PasswordFormValidator 가 적용
@InitBinder("passwordForm")
public void passwordFormInitBinder(WebDataBinder webDataBinder) { 
    webDataBinder.addValidators(new PasswordFormValidator());
}
```
