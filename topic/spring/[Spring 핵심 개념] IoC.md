### IoC (Inversion of Control)

객체의 생성과 의존성 관리를 개발자가 직접 하는 것이 아니라, 스프링 컨테이너가 대신 관리하는 개념입니다.  
전통적인 프로그래밍에서는 개발자가 객체의 생성, 의존성 주입, 생명 주기 관리를 직접 하였지만 IoC가 적용된 방식에서는 이러한 제어권이 컨테이너로 넘어갑니다.

<br />

### IoC 컨테이너의 역할

- 객체(Bean) 생성
    - @Component, @Service, @Repository, @Controller 등의 어노테이션으로 선언된 클래스를 객체로 생성
- 객체 간의 의존성 주입
    - DI를 통해 객체 간 의존성 주입
- 생명주기 관리
    - 객체의 초기화와 소멸을 관리

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

### IoC를 적용한 경우

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