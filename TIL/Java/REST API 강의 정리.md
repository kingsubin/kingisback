# rest api 강의 짧게정리 
> - 응답 코드 구분 
> 200만 쓰는게 아니라 200, 201이런식으로 
> 모든 요청을 POST 로만 처리, 응답코드를 200 으로 하는건 안좋음

> - exception 던질때 
> 클래스에 @ResponseStatus(HttpStatus.NOT_FOUND) 이런식으로 해주면 500
> 번 에러로 나가는게 아니라 다른 에러로 보낼수있음 
> 서버측 에러가아니라 클라이언트측 오류라고 해줌 

> - Validation 처리 
> @Valid 에 걸렸을때는 ? 
> 400 BAD REQUEST 응답이오네 
> handleMethodArgumentNotValid 를 재정의 

> - xml 처리를 위해선 ?
> jackson-dataformat-xml 의존성 추가 
> header 에 (Accept, application/xml) 추가 

> - 필터링 
> 응답객체에서 password 와 같이 주기 싫은 정보는 ?
> 노출시키고 싶지않은 필드 @JsonIgnore 
> 클라이언트가 받았을때 @JsonIgnore 가 되어있는 필드는 오지않음.
> Class 위에 @JsonIgnoreProperties(value= {“password”, “sin”  }) 
> 이런식으로해도됨 

> - 필터링 개별사용자, 전체사용자 
> Class 위에 @JsonFilter(“xxx”)
> Controller 에서 따로 처리를 함
> SimpleBeanPropertyFilter, FilterProvider, MappingJacksonValue 클래스 사용
> **깊게는 무슨 내용이고 어떨때 어떻게 사용하는지 잘 모르겠음**

> - URI 를 이용한 REST API Version 관리
> 보통 uri 에 api/v1/user/ … 이런식인데 여기서 v1이 버전을 뜻하는거임 .
> 현재 버전이 어떤건지 사용자들이나 개발자들에게  사용 가이드를 알려주기위한 작업 
> (복사할때) BeanUtils.copyProperties(Object source, Object target)

> - Request Parameter 를 이용한 API Version 관리 
> @GetMapping(value = “xxx”, params = “version=1”)
> localhost:8080/users/1/?version=1, version=2  이런식으로 파라미터로 버전관리 

> - Header 를 이용한 API Version 관리 
> @GetMapping(value = “xxx”, headers = “X-API-VERSION=1”) (헤더값은 임의지정 )
> 요청시 headers 에 (X-API-VERSION, 1) 이런식으로 담아줘야함 

> - produces 를 이용한 버전 관리
> @GetMapping(value = “xxx”, produces = “application/vnd.company.appv1+json”)  (임의지정 ) mime type 을 이용한 방법 
> 요청시에는 headers 에  (Accept, application/vnd.company.appv1+json) 담아줘야함

> - 버전관리를 위해 중요한점 
> URI 값 지저분하거나 너무 과도한 정보를 표기하게 X
> 잘못된 헤더값 조심
> 적절한 용도에 따라서 웹브라우저에서 실행이 가능하도록 
> 1) URI Versioning, 2) Request Parameter versioning 을 말함. 
> 적절한 도움 문서를 작성해줘야함 API Documentation

---
## section 4 : spring boot api 사용
1. LEVEL3 을 위한 HATEOAS
2. Documentation 을 위한 Swagger API
3. Monitoring 을 위한 Actuator, HAL

> 21. HATEOAS
> Hypermedia As the Engine Of Application State
> 현재 리소스와 연관된 자원 상태 정보를 제공
> ex) 상세보기에서 삭제할수있는정보, 수정할수있는정보 같은걸 넣어줄수있음
> 하나의 리소스에서 파생할수있는 추가작업도 확인 가능 
> 개발자가 해야할 작업의 양이 늘어남
> 사용자 입장에서는 추가정보를 한번에 얻을수있다는 장점이 있음.


> 버전 마다 다르게 설정 

> 22. Swagger
> Documentation 작성을 위해 사용하는데 유용함

> 24. Actuator
> localhost:8080/actuator 이렇게만해도 바로 작동 가능 
> 더 많은 정보를 보기 위해서 설정 
> 그냥 이런게 있다 정도로 넘어감 
> 추가 상세 설정은 또 따로 해줘야함 

> 25. HAL 브라우저를 이용한 HATEOAS 기능 구현 
> HAL Browser ? 
> Hypertext Application Language
> “ HAL is a simple format that gives a consistent and easy way to hyperlink between resources in your API”
> 의존성 추가  -> 루트 페이지 접속