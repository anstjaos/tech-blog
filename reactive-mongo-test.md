# Reactive MongoDB Integration Test
최근 개인 프로젝트 하나를 진행하고 있는데, Webflux + Reactive MongoDB 조합으로 Integration 테스트 코드 작성한 방법을 공유하려고 한다.

구글링하면 DataMongoTest 어노테이션과 embedded MongoDB 라이브러리를 통해서 테스트하는 예시는 많지만, 내 프로젝트에서는 정상 동작하지 않았다.

내 프로젝트에서 사용하는 기술 스택과 라이브러리 버전부터 정리해보자.

## 기술 스택, 라이브러리 버전

* Spring Boot 3.1.0
* spring-boot-starter-webflux
* reactor-test 3.5.4
* spring-boot-starter-data-mongodb-reactive
* de.flapdoodle.embed.mongo.spring30x

위 라이브러리 중 가장 중요한 것은 `de.flapdoodle.embed.mongo.spring30x` 이다.

대부분의 예시에서는 `de.flapdoodle.embed.mongo`를 많이 사용하는데, Spring Boot 3.0.0 이상부터는 위 라이브러리를 사용해야지 정상 동작한다.


## application.yml
`de.flapdoodle.embed.mongo.spring30x` 라이브러리를 추가했다면 application.yml 파일을 수정해야한다.

properties 파일을 사용해도 동일하게 추가해야한다.

```yml
de:
  flapdoodle:
    mongodb:
      embedded:
        version: 5.0.5
```

version의 값은 MongoDB의 버전이다. 원하는 버전으로 입력하면 된다.


## Entity
Integration 테스트에서 사용하기 위한 Entity를 하나 작성한다.

```java
@Document(collection = "user")
@Getter
public class UserEntity {

    @Id
    private String userId;
    private String nickname;
    private String password;
    private String phoneNumber;
    private Boolean autoLogin;

    public UserEntity() {}

    @Builder
    public UserEntity(String userId, String nickname, String password, String phoneNumber, Boolean autoLogin) {
        this.userId = userId;
        this.nickname = nickname;
        this.password = password;
        this.phoneNumber = phoneNumber;
        this.autoLogin = autoLogin;
    }
}
```

중요한 것은 @Id 어노테이션을 쓸 때 `org.springframework.data.annotation.Id`를 import 해야한다.

다른 Id 라이브러리인 `jakarta.persistence.Id`를 사용한다면 이후 만들 Repository의 findById가 정상 동작하지 않는다.


## Repository
이제 UserEntity를 persistence layer에서 사용하기 위해 Repository 인터페이스를 하나 정의한다.

```java
interface UserRepository extends ReactiveMongoRepository<UserEntity, String> {
}
```

처음에 말했듯이 이번 프로젝트에서는 ReactiveMongoRepository를 사용하였다.

## Test code
이제 작성한 내용을 바탕으로 테스트 코드를 작성해보자.

전체 코드는 다음과 같다.

```java
@DataMongoTest()
@ExtendWith(SpringExtension.class)
public class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    private final String USER_ID = "user-id";

    @Test
    void saveUser_success() {
        var user = createUserEntity();

        StepVerifier.create(userRepository.deleteAll().then(userRepository.save(user)))
                .expectNextMatches(userEntity -> userEntity.getUserId().equals(user.getUserId())
                        && userEntity.getNickname().equals(user.getNickname())
                        && userEntity.getPassword().equals(user.getPassword())
                        && userEntity.getPhoneNumber().equals(user.getPhoneNumber())
                        && userEntity.getAutoLogin().equals(user.getAutoLogin()))
                .verifyComplete();
    }

    @Test
    void readUser_success() {
        var user = createUserEntity();

        Publisher<UserEntity> setup = userRepository.save(user);
        Mono<UserEntity> find = userRepository.findById(USER_ID);

        Publisher<UserEntity> composite = Mono.from(setup).then(find);

        StepVerifier.create(composite)
                .assertNext(userEntity -> {
                    assertEquals(userEntity.getNickname(), user.getNickname());
                })
                .verifyComplete();
    }

    private UserEntity createUserEntity() {
        return UserEntity.builder()
                .userId(USER_ID)
                .nickname("nickname")
                .password("password")
                .phoneNumber("phone-number")
                .autoLogin(true)
                .build();
    }
}
```

## 마무리
사실 테스트 코드는 볼만한 부분이 없다.

중요한 것은 Spring boot 3.0.0 이상에서는 `de.flapdoodle.embed.mongo.spring30x` 라이브러리를 쓴다는 것이 중요하다.

이 부분을 몰라서 삽질을 하루 정도 했기 때문에 혹시나 이 글을 본다면 도움이 되기를 바란다.