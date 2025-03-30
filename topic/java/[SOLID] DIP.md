### 의존성 역전 원칙 (Dependency Inversion Principle)

상위 모듈이 하위 모듈을 직접 의존해서는 안 되며, 둘 다 추상화에 의존해야 한다는 원칙입니다.

- 상위 모듈 : 핵심 비즈니스 로직을 포함하는 모듈 (Service)
- 하위 모듈 : 실제 구현을 담당하는 모듈 (Repository, Component)

<br />

### 왜 중요한가?

상위 모듈이 하위 모듈을 직접 의존하게 되면 유지보수가 어렵고 확장성이 떨어지는 문제가 발생합니다.

<br />

### 의존성 역전 원칙을 적용했을 때의 장점

상위 모듈, 하위 모듈 둘 다 추상화에 의존하면 다음과 같은 장점이 있습니다.

- 유연성 향상 : 상위 모듈이 구현체가 아닌 추상화에 의존하므로 다른 구현체로 쉽게 변경 가능
- 유지 보수성 향상 : 특정 구현체 수정 시, 상위 모듈을 수정할 필요가 없음
- 확장성 : 새로운 기능(구현체) 추가 시, 상위 모듈을 수정할 필요가 없음
- 테스트 용이 : 단위 테스트가 쉬워짐

<br />

### 전통적인 계층형 구조 (DIP 위반)

```text
Business Layer (Service) → Data Access Layer (Repository 구현체)
```

- Business Layer 소스 코드가 Data Access Layer 구체 클래스를 import
- Business Layer(서비스)가 Data Access Layer(리포지토리)의 구체적인 구현체에 의존하고 있음

<br />

### DIP 적용 후 구조

```text
Business Layer (Service) → (추상화 인터페이스) ← Data Access Layer (Repository 구현체)
```

- Data Access Layer 소스 코드가 Business Layer의 인터페이스를 import
- Business Layer는 추상화 인터페이스(UserRepository)에 의존
    - 더 이상 구현체를 직접 의존하지 않음
- Data Access Layer는 추상화 인터페이스(UserRepository)를 구현
    - 구현체가 인터페이스를 의존하게 됨

<br />

### 의존성이 역전됐다는 의미

상위 모듈이 하위 모듈의 구체적인 구현체에 의존하지 않게 된다는 의미입니다.  
즉, 하위 계층이 상위 계층에서 정의한 인터페이스에 의존하기 때문에 이를 의존성 역전이라고 합니다.

DIP 적용 후에도 서비스는 인터페이스를 의존하므로 "Business Layer → (추상화 인터페이스)" 구조는 변함없지만, "구현체가 추상화에 의존하는 구조"가 되면서 의존성의 주체가 바뀌었다고 이해할 수
있습니다.

<br />

### 결론

의존성 역전 원칙은 상위 모듈이 하위 모듈의 변경에 영향을 받지 않도록 설계하는 원칙입니다. 이를 통해 확장성과 유지 보수성이 뛰어난 시스템을 구출할 수 있습니다.

<br />

#### 참고 자료

- ChatGPT 대화 내용
