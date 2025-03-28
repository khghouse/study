### 인터페이스 분리 원칙 (Interface Segregation Principle)

클라이언트가 사용하지 않는 메서드에 의존하지 않도록 인터페이스를 분리해야 한다는 원칙입니다.  
즉, 큰 인터페이스를 작은 인터페이스 여러 개로 분리하는 것을 권장합니다.

<br />

### 왜 중요한가?

거대한 인터페이스일수록 구현체에서 사용하지 않는 메서드를 의존하는 경우가 생길 수 있습니다.    
불필요한 의존성이 발생한다면 메서드를 강제 구현해야 합니다.

<br />

### 인터페이스 분리 원칙을 지키지 않은 코드

```java
interface PaymentMethod {
    void process();

    void refund();
}

class CreditCardPayment implements PaymentMethod {
    public void process() { /* 구현 */ }

    public void refund() { /* 구현 */ }
}

class CouponPayment implements PaymentMethod {
    public void process() { /* 구현 */ }

    public void refund() { /* 쿠폰 결제는 환불 불필요 */ }
}
```

- CouponPayment 클래스는 불필요한 메서드를 강제 구현하고 있음
- 클래스의 책임이 모호해짐

<br />

### 인터페이스 분리 원칙을 준수한 코드

```java
interface PaymentMethod {
    void process();
}

interface Refundable {
    void refund();
}

class CreditCardPayment implements PaymentMethod, Refundable {
    public void process() { /* 구현 */ }

    public void refund() { /* 구현 */ }
}

class CouponPayment implements PaymentMethod {
    public void process() { /* 구현 */ }
}
```

개선된 코드

- 각 클래스가 필요한 인터페이스만 구현
- 코드가 명확해지고, 결합도가 낮아짐

<br />

### 결론

구현체를 구현할 때, 불필요한 메서드를 강제로 구현하고 있다면 인터페이스가 여러 책임을 가지고 있는지 확인해 볼 시점입니다.  
인터페이스 분리 원칙을 적용하면 불필요한 의존성을 제거하고, 코드의 의도를 더 명확히 표현할 수 있으며, 새로운 기능 추가도 수월해집니다.

<br />

#### 참고 자료

- ChatGPT 대화 내용
- [Readable Code: 읽기 좋은 코드를 작성하는 사고법](https://www.inflearn.com/course/readable-code-%EC%9D%BD%EA%B8%B0%EC%A2%8B%EC%9D%80%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1%EC%82%AC%EA%B3%A0%EB%B2%95)
    - 참고 챕터 : 섹션 4. 객체 지향 패러다임 : ISP: Interface Segregation Principle