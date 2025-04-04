### IoC (Inversion of Control)

객체의 생성과 의존성 관리를 개발자가 직접 하는 것이 아니라, 스프링 컨테이너가 대신 관리하는 개념입니다.  
전통적인 프로그래밍에서는 개발자가 객체의 생성, 의존성 주입, 생명 주기 관리를 직접 하였지만 IoC가 적용된 방식에서는 이러한 제어권이 컨테이너로 넘어갑니다.

<br />

### IoC 컨테이너의 역할

- Bean 등록 및 객체 생성
    - @Component, @Service, @Repository, @Controller 등의 어노테이션을 사용하여 Bean 정의
    - 정의된 Bean을 읽고, 객체 생성
- 객체 간의 의존성 주입
    - DI를 통해 객체 간 의존성 주입
- 생명주기 관리
    - 객체의 초기화와 소멸을 관리

<br />

### IoC와 DI 관계

IoC의 핵심적인 구현 방식이 DI입니다.

<br />

### IoC의 장점

DI의 장점과 동일합니다.

- 객체 간 결합도를 낮춰 유연성 및 유지 보수성 향상
- 테스트 코드 작성 용이 (Mocking 가능)
- 의존성을 외부에서 주입받아 코드 재사용성 향상

<br />

### IoC를 적용하지 않은 경우 (직접 객체 생성)

```java
public class MemberService {
    private final MemberRepository memberRepository; // 인터페이스

    // MemberService 내부에서 직접 객체를 생성 (강한 결합)
    public MemberService() {
        this.memberRepository = new MemberRepositoryImpl(); // 구현
    }
}
```

- MemberRepository의 구현체가 변경되면 MemberService도 수정해야 함
- 테스트 시 MemberRepository를 Mock 객체로 대체하기 어려움

<br />

### IoC를 적용한 경우 (DI 적용)

```java

@Service
public class MemberService {
    private final MemberRepository memberRepository;

    // 의존성 주입 (DI) - 외부에서 객체를 주입
    @Autowired
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

- MemberService가 MemberRepository의 구체적인 구현을 몰라도 됨
- MemberRepository의 구현체를 쉽게 변경할 수 있음
- 테스트 시 Mock 객체를 쉽게 주입할 수 있음

<br />

### 결론

스프링 IoC는 객체 생성, 의존성 주입, 생명주기 관리를 담당하여 개발자가 비즈니스 로직에 좀 더 집중할 수 있도록 도와줍니다. 이를 통해 유연성, 테스트 용이성, 재사용성을 높일 수 있습니다.

<br />

#### 참고 자료

- ChatGPT 대화 내용