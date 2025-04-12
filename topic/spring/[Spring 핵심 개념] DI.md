### DI (Dependency Injection)

객체 간의 의존성을 개발자가 직접 코드에서 관리하는 것이 아니라, 스프링 컨테이너가 외부에서 주입해 주는 방식입니다.

<br />

### 왜 사용할까?

DI를 적용하면 객체 간의 결합도를 낮추고, 유연성과 확장성을 높일 수 있고 테스트가 용이합니다.

<br />

### DI의 장점

- 객체 간 결합도를 낮춰 유연성 및 유지 보수성 향상
- 테스트 코드 작성 용이 (Mocking 가능)
- 의존성을 외부에서 주입받아 코드 재사용성 향상

<br />

### DI를 사용하지 않은 경우 (결합도가 높은 코드)

객체 간의 의존성을 직접 관리해야 하므로 결합도가 높아집니다

```java
public class MemberService {
    private final MemberRepository memberRepository; // 인터페이스

    // MemberService 내부에서 직접 객체를 생성 (강한 결합)
    public MemberService() {
        this.memberRepository = new MemberRepositoryImpl(); // 구현체
    }
}
```

문제점

- MemberRepository의 구현체가 변경되면 MemberService도 수정해야 함 -> 유연성 및 유지 보수성이 떨어짐
- 테스트 시 MemberRepository를 Mock 객체로 대체하기 어려움

<br />

### DI를 적용한 경우 (결합도가 낮은 코드)

객체 간의 의존성을 외부에서 주입받아 결합도를 낮출 수 있습니다.

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

DI 적용 시 이점

- MemberService에서 MemberRepository 구현체를 몰라도 됨 -> 유연성 및 유지 보수성 향상
    - MemberRepository가 바뀌어도 수정 불필요
- 테스트 시 UserRepository를 Mock 객체로 쉽게 대체 가능 -> 단위 테스트가 쉬워짐

<br />

### 세 가지 DI 방법

#### 1. 생성자 주입 (Constructor Injection)

생성자를 통해 의존성을 주입하는 방식입니다. 가장 권장되는 방법으로 다음과 같은 장점이 있습니다.

- 불변성 유지 가능
    - final 키워드를 사용할 수 있어서, 한 번 주입된 의존성을 변경할 수 없음
- 순환 참조 방지
    - 애플리케이션 실행 시점에 오류 발생
    - 필드, 세터 주입은 런타임까지 오류가 발생하지 않을 수 있음
- 테스트 용이
    - 생성자를 통해 의존성을 주입받기 때문에 Mock 객체를 주입하기 쉬움

```java

@Service
public class MemberService {
    private final MemberRepository memberRepository;

    // 생성자 주입
    @Autowired
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

#### 2. 필드 주입 (Field Injection)

필드에 직접 @Autowired를 사용하여 의존성을 주입하는 방식입니다. 권장되지 않는 방법입니다.

- 의존성이 불변이 아님
- 순환 참조 발생 우려
- 테스트 시 Mock 객체 주입의 어려움

```java

@Service
public class MemberService {
    @Autowired
    private MemberRepository memberRepository;
}
```

#### 3. 세터 주입 (Setter Injection)

setter 메서드를 통해 의존성을 주입하는 방식입니다. 선택적으로 의존성을 주입할 수 있는 특징이 있습니다.

```java

@Service
public class MemberService {
    private MemberRepository memberRepository;

    @Autowired
    public void setMemberRepository(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

<br />

### 결론

- DI는 객체 간 의존성을 스프링 컨테이너에서 주입해 주는 방식을 말합니다.
- DI를 적용하면 객체 간 결합도를 낮추고, 유연성 및 유지 보수성을 향상시킬 수 있으며 테스트하기 좋은 코드가 됩니다.
- 생성자 주입을 사용하면 안전하고 유지 보수하기 좋은 코드가 됩니다.

<br />

#### 참고 자료

- ChatGPT 대화 내용