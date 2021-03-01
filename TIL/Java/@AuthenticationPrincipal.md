
##Principal##
로그인한 사용자의 정보를 파라미터로 받고 싶을때 Principal 아래와 같이 객체로 받아서 사용할 수 있다.

Principal 객체는 java.security 패키지에 속해있는 인터페이스이며 
getName() 을 통하여 name 정보를 참조할 수 있다.

```java
@GetMapping()
public String index (Model model, Principal principal) {
	if (principal == null) {
    	model.addAtrribute("msg", "Hello");
    } else {
    	model.addAtrribute("msg", "Hello" + principal.getName());
    }
    
    return "index"
}
```

##@AuthenticationPrincipal##
다른 방법을 찾아보니 @AuthenticationPrincipal 이 존재한다.
이 애노테이션은 파라미터와 애노테이션 타입에 적용가능하며 런타임시 작동한다.
안에 설명을 보면 Authentication.getPrincipal() 메소드 인자로 사용한다고 적혀있다.
```java
@Target({ ElementType.PARAMETER, ElementType.ANNOTATION_TYPE })
@Retention(RetentionPolicy.RUNTIME)
```
파라미터와 애노테이션 타입에 적용가능하며 런타임시 작동한다.

```java
@GetMapping()
public String index (@Authentication User user) {
	if (principal == null) {
    	model.addAtrribute("msg", "Hello");
    } else {
    	model.addAtrribute("msg", "Hello" + user.getUsername());
    }

    return "index";
}
```

```
Then the user can specify an annotation that looks like:
@AuthenticationPrincipal(expression = "customUser")
```
       
애노테이션 사용시 UserDetailsService 에서 Return 한 객체를 파라미터로 직접 받아올 수 있다.

##현재 Account 에 대한 애노테이션 만들기##
다른 사람이 이렇게 사용한걸 보고 뭔지 모르겠어서 찾아보았다.

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.PARAMETER)
@AuthenticationPrincipal(expression = "#this == 'anonymousUser' ? null : account")
public @interface CurrentAccount {
}
```
```java
@Getter
public class UserAccount extends User {

    private Account account;

    public UserAccount(Account account) {
        super(account.getNickname(), account.getPassword(), List.of(new SimpleGrantedAuthority("ROLE_LEVEL_1")));
        this.account = account;
    }
}
```

- 현재 참조중인 객체가 AnnonymousAuthenticationFilter 에 의해 생성된 Authentication 인 경우 null 반환, 아니면 User 를 상속받은 UserAccount 를 반환한다.
- Account 객체에서 UserDetails를 구현하여 사용하여도 되지만 직접 리턴하는 방법은 도메인 클래스에 영향을 미치니 추천하지 않고 위와 같이 UserAccount 객체를 따로 만듬.

```java
@GetMapping()
public String index (@CurrentAccount Account account) {
   if (account == null) {
    	model.addAtrribute("msg", "Hello");
    } else {
    	model.addAtrribute("msg", "Hello" + account.getUsername());
    }
	return "index";
}
```

