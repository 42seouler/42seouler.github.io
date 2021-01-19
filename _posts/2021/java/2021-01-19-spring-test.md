---
title: Spring 계층 단위로 테스트 하는 방법.

layout: single

author_profile: true

read_time: false

comments: true

share: true

related: true

categories:
- Java

tag:
- spring

description: Spring 테스트 방법을 꾸준히 업데이트 할 예정.

meta_keywords: Java, Spring

last_modified_at: '2021-01-19 21:00:00 +08000'

toc: true

toc_sticky: true

toc_label: 목차
popular: true
---
<br>

저는 Spring 테스트 자료를 찾아보면서 같은 계층의 테스트라도 다른 어노테이션을 사용하거나
또는 여러개를 사용 하는 것을 보면서 "왜?" 라는 의문이 들었습니다.  
어노테이션에 대해 정확히 알고 쓰기 위해 이 글을 작성하게 되었습니다.
어노테이션에 관한 것은 API 문서 또는 Spring docs에서 주로 찾아 볼 수 있었습니다.  
그렇다고 이해하기 쉬운 것은 아니었던 것 같습니다.

스프링은 단위 또는 통합하여 테스트 할 수 있습니다.
단위 테스트는 애플리케이션 코드가 외부 종속성을 올바르게 작동하는지 확인하지 않습니다.
단일 구성 요소에 초첨을 맞추기 때문에 외부 종속성을 모킹 합니다.
통합 테스트는 전체 애플리케이션을 범위에 포함하거나 특성 구성 요소만 포함 합니다.

## Controller 계층 테스트 하는 방법

Spring은 들어오는 HTTP 요청을 처리하고 컨트롤러에 전달합니다.  
이렇게하면 거의 전체 스택이 사용되며 실제 HTTP 요청을 처리하는 것과 똑같은   
방식으로 코드가 호출되지만 서버 시작 비용은 없습니다

Controller를 테스트하기에 앞서 MockMvc를 미리 코드 상단에 복붙하겠습니다.

```java
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.springframework.test.web.servlet.setup.MockMvcBuilders.*;
```

### 단위 테스트

전체 Spring 애플리케이션 컨텍스트가 시작되지만 서버는 시작하지 않습니다.

#### @WebMvcTest(TestController.class)
- 서버를 시작 하지 않고 MVC 요청을 모의 요청 및 응답 객체를 통해 수행합니다.
- Controller만 초기화하고 필요한 나머지 종속성을 제공해야 합니다.

#### @AutoConfigureMockMvc
- 자동구성요소 (서블릿 필터)등을 적용 할때 추가로 사용한다.

아래와 같은 종속성을 갖고 있는 Controller가 있다고 가정 합니다.  
이 서비스 레이어는 통합테스트에서도 그대로 사용 한다고 가정 합니다.

```java
@Service
public class TestServiceImpl {
	public String greet() {
		return "Hello, World";
	}
}
```

Controller 부분을 제외하고는 모두 Mock 객체를 활용 해야합니다.  
그렇기 때문에 MockBean를 통해 서비스를 종속성을 주입 할 수 있습니다.

```java
@WebMvcTest(TestController.class)
@AutoConfigureMockMvc
public class TestRestController {
 
  @Autowired
  private MockMvc mockMvc;
  
  @MockBean
  TestServiceImpl testService;
  
  @Test
  public void hello() throws Exception {
      when(testService.greet()).thenReturn("Hello, Mock");
      mockMvc.perform(get("/")).andDo(print()).andExpect(status().isOk())
  }
 
}
```

testService는 Mock객체 이므로 호출 되었을 때 객체의 동작을 정의 해줘야 합니다.  
위에서는 서비스의 greet를 호출하면 hello, Mock이라는 스트링을 반환하는 동작을 만들었습니다.  
그리고 그것을 Mocito에서는 stub이라고 합니다.
stub까지 준비 되었다면 MockMvc를 통해 Spring Mvc를 테스트하면 됩니다.  
주의 해야 할 점은 Mockmvc의 행위를 수행하기 전에 stub까지 완료 되어야 한다는 것 입니다.  
즉 함수의 호출 순서에 유의하여 테스트 코드를 작성해야 한다는 것 입니다.

### 통합 테스트

### @SpringBootTest
- 임베디드 서버를 시작하고 애플리케이션 컨텍스트를 완전히 초기화합니다.
- @Autowired 주석을 사용하요 테스트 클레스에 종속성을 삽입 할 수 있습니다.
- Mock객체를 사용하지 않고 통합 테스트 할 때 사용하는 어노테이션입니다.

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
@AutoConfigureMockMvc
public class TestRestController {
 
  @Autowired
  private MockMvc mockMvc;
  
  @Autowired
  TestServiceImpl testService;
  
  @Test
  public void hello() throws Exception {
      mockMvc.perform(get("/")).andDo(print()).andExpect(status().isOk())
  }
}
```

통합테스트에서는 when으로 시작하는 Mockito가 보이지 않습니다.  
왜냐하면 SpringBootTest를 사용해서 모든 빈을 applicationcontext에 등록하여 사용하기 때문입니다.  
즉 내가 만든 하위 레이어를 사용해 테스트 하기 때문에 테스트코드가 단순하며 별로의 추가 사항은 없습니다.

Controller의 테스트는 서버를 시작하는 비용이 없습니다.
1. Slice테스트를 통해 Controller부분의 로직만 검증
2. Integration테스트를 통해 하위 레이어를 포함하여 검증

너무 많은 Mocking이 필요한 경우 SpringBootTest를 사용 할수도 있고  
Controller부분만 검증하면 되서 Slice테스트 한다고 합니다.  
테스트방법에 정답은 없다고 하니 상황에 맞추어 선택하면 될 것이라고 생각합니다.

> 공식 문서  
[@AutoConfigureMockMvc](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-testing)  
[MockMvc class](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/MockMvc.html)  
[웹 레이어 테스트](https://spring.io/guides/gs/testing-web/)  

> 참고 자료  
[Mockmvc 상세 설명](https://itmore.tistory.com/entry/MockMvc-%EC%83%81%EC%84%B8%EC%84%A4%EB%AA%85?category=302779)  
[Mockmvc 메서드 상세 설명](https://ktko.tistory.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81Spring-MockMvc-%ED%85%8C%EC%8A%A4%ED%8A%B8)  

웹 계층만 등록되기 때문에 Service, Repository 계층의 의존성은 Mock객체를 활용해야합니다.  
그렇기 때문에 복잡한 로직이 컨트롤러에 있다면 통합 테스트를 고려 하는것도 좋을것 같습니다.

## Service 계층 테스트 하는 방법

JUnit, Mockito 사용하여 Spring서비스 계층을 단위 테스트를 작성합니다.

### 단위 테스트

#### @ExtendWith(MockitoExtension.class)

- junit4의 @Runwith 어노테이션은 위의 Extendwith로 대체 되었습니다.  
  호환성을 위해 Runwith로 사용 가능 하다고 합니다.
- Mock 객체를 생성하기 위해 필요한 어노테이션 입니다.  

userRepository에 종속성을 갖는  Service를 테스트 하기위해 작성 되었습니다.

```java
@ExtendWith(MockitoExtension.class)
class UserServiceImplTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserServiceImpl userService;
	
    public void create() throws Exception {
        //given
        User user = new User("seouler");
        //when
        when(userRepository.save(any(User.class))).willReturn(Optional.of(user));
        User saveUser = userService.create(user);
        //then
        Assertions.assertEquals(user.getName(), saveUser.getName());
    }
}
```

하위 Repository를 사용하지 않기 때문에 @Mock 활용해서 목 객체를 만듭니다.  
그리고 servicer의 로직 중 Repository를 사용하는 부분을 Stub해주시면 됩니다.

> 공식 문서  
[Extendwith(MocitoExtension.class) 설명](https://www.javadoc.io/doc/org.mockito/mockito-junit-jupiter/3.0.0/org/mockito/junit/jupiter/MockitoExtension.html)  
[junit5 extendwith](https://junit.org/junit5/docs/snapshot/user-guide/index.html#extensions)  

> 참고 자료  
[Mockito 기본 사용법](https://cornswrold.tistory.com/366)  


## Repository 계층 테스트 하는 방법

#### @DataJpaTest
- 전체 자동 구성이 비활성화되고 대신 JPA 테스트와 관련된 구성 만 적용됩니다.  
- @DataJpaTest는 트랜잭션이며 각 테스트가 끝날 때 롤백됩니다.  

EntityManagerJPA 테스트에서 사용 하기 위한 대안 입니다.

```java
@DataJpaTest
class ExampleRepositoryTests {

    @Autowired
    private TestEntityManager entityManager;

    @Autowired
    private UserRepository repository;

    @Test
    void testExample() throws Exception {
        this.entityManager.persist(new User("sboot", "1234"));
        User user = this.repository.findByUsername("sboot");
        assertThat(user.getUsername()).isEqualTo("sboot");
        assertThat(user.getVin()).isEqualTo("1234");
    }

}
```
repository의 메서드를 테스트하기에 앞서 persist를 한다든지 entityManager의 역활을 수행할 수 있습니다.

> 공식 문서  
[DataJpaTest docs](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/autoconfigure/orm/jpa/DataJpaTest.html)  
[DataJpa 활용 docs](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features)  


> 읽어 봐야 할 문서들  
[스프링부트 Test 어노테이션들](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-test-auto-configuration.html#test-auto-configuration) 
