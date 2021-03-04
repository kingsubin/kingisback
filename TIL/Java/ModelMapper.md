# ModelMapper 

- 다른 코드에서 ModelMapper 라는것을 보았는데 뭔지 궁금해서 찾아보았다.

- - -

###왜 DTO 를 따로 만들어서 사용하는가 ?
Entity 클래스를 생성할때 자바빈 규약을 생각하면서 getter/setter 를 생성하는 경우가 있는데 이렇게 되면 해당 클래스의 인스턴스 값들이 언제 어디서 변해야하는지 구분할수가 없어, 차후 기능 변경시 복잡해진다.
절대로 테이블과 매핑되는 Entity 클래스를 Reqeust/ Response 클래스로 사용해서는 안된다.
Entity 클래스가 변경되면 여러 클래스에 영향을 끼치게 되는 반면 Request, Response 용 DTO 는 View 를 위한 클래스라 자주 변경이 필요하다.
View Layer 와 DB Layer 를 철저하게 역할 분리하는게 좋다.


###ModelMapper는 왜 쓰는가 ?
- 필드가 늘어나면 귀찮음. 
- 실수를 줄이려고

> ModelMapper의 목표는 오브젝트 매핑을 쉽게 만드는 것이다.
사람이 하는 것과 같이 규칙 기반으로 매핑방법을 자동으로 결정하는데,
이는 특정 케이스 핸들링을 위한 리팩토링 안전 API를 제공함으로써 가능하다.
> modelmapper.org

위에서 설명한 이유로인해 DTO 를 따로 만들어서 사용하는데 DTO -> Entity, Entity -> DTO 이렇게 컨버팅 하는게 자주 사용됨.
이렇게 자주 사용하다보면 필드 매핑을 잘못할 수 도있고 새로운 필드를 추가했을때 convert 하는 메소드에서 다시 신규 필드를 추가해줘야하는데 놓칠수도 있다.

##사용방법
[공식페이지](http://modelmapper.org/getting-started/)

- 의존성 추가

```java
compile group: 'org.modelmapper', name: 'modelmapper', version: '2.3.0'
```

```java
<dependency>
  <groupId>org.modelmapper</groupId>
  <artifactId>modelmapper</artifactId>
  <version>2.3.0</version>
</dependency>
```

- - -
설정에서 또 여러가지 자세한 설정 방법이 있는데 지금 필요한 정도만 쓰고 나중에 필요할때 다시 추가하겠다.
아래 블로그에는 설정관련해서도 정리가 잘 되어있다.

> 잘 정리된 글 
> [참조1](https://baek.dev/post/15/)
> [참조2](https://blog.deliwind.com/posts/235)
