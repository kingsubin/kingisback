# Forward, Redirect

forward 와 redirect 을 언제마다 사용하는지 헷갈려서 찾아보았다.

웹에서는 작업중인 페이지를 이동하기 위해서는 2가지 페이지 전환기능을 제공한다.

**Forward 방식**

페이지의 이동만 존재한다.  
실제로 웹 브라우저는 다른 페이지로 이동했음을 알 수 없다.  
웹 브라우저에서는 최초 호출한 URL 표시되고, 이동한 페이지의 URL 정보는 확인할 수 없다.  
현재 실행중인 페이지와 forward에 의해 호출될 페이지는 Request, Response 객체를 공유하고 있다.  
요청 정보를 그대로 전달하기에 사용자가 최초로 요청한 요청정보는 다음 URL 에서도 유효하다.

ex) 게시판의 글쓰기나 회원가입 응답 페이지에서 새로고침을 한다면, 요청정보가 그대로 살아있기에 요청이 여러 번 등록될 수 있음.  
그래서 시스템에 변화가 생기지 않는 단순 조회 요청의 경우 forward 로 응답하는것이 맞음.

**Redirect 방식**  
다른 페이지로 이동하라고 명령을 내린다.  
그러면 웹 브라우저는 URL 을 지시된 주소로 바꾸고 해당 주소로 이동함.  
새로운 페이지에서는 Request, Response 객체가 새롭게 생성됨.

ex) 위와같은 글쓰기나 회원가입의 응답페이지에서 새로고침을 누른다고해도, 처음의 요청정보는 존재하지 않으므로 여러 번 등록을 막을 수 있음.  
그래서 시스템에 변화가 생기는 요청에는 redirect를 쓰는것이 맞음.

**why redirect ?**  
REST API Naming Convention 에 따르면 GET/ POST 역할이 구분 되어있음  
GET :: 리소스를 요청만 함  
POST :: 리소스를 생성하는 요청  
따라서 POST는 생성만 하고 끝나야함.

return에서 그냥 login.html 로 이동해도 작동은 하지만 그렇게 하지말라는 것  
클라이언트한테 리다이렉트할 주소를 주고, 클라이언트는 다시 그 주소로 GET 요청을 보내는 것이다.

**회원가입후 redirect 예제**

```java
@GetMapping("/account/sign-up")
public String signUpForm(Model model) {
    model.addAttribute(new AccountSignupDto());
    return "sign-up";
}

@PostMapping("/account/sign-up")
public String signUpSubmit(final AccountSignupDto signupForm){
    accountService.processNewAccount(signupForm);
    return "redirect:/account/login";
}

@GetMapping("/account/login")
public String loginForm(Model model) {
    model.addAttribute(new AccountLoginDto());
    return "login";
}
```

---

[참조](https://mangkyu.tistory.com/51)