### AOP (Aspect Oriented Programming)

관점 지향 프로그래밍이라고 불리며, 핵심은 관심사의 분리입니다.  
비즈니스 로직과 별개로 자주 반복되는 코드가 존재하는데 이러한 코드는 핵심 기능은 아니지만 모든 곳에서 필요합니다.  
이런 공통 관심사(=횡단 관심사, Cross-Cutting Concern)를 모듈화하여 관리하는 것이 AOP입니다.

공통 기능 예시

- 로깅, 보안, 트랜잭션

<br />

### 왜 사용할까?

컨트롤러나 서비스와 같은 클래스에서 핵심 비즈니스 로직 이외에 다음과 같은 공통 관심사를 가질 수 있습니다.

- 트랜잭션 처리
- 로깅
- 예외 처리

이러한 로직을 모든 메서드에 반복하여 작성하면 다음과 같은 문제가 발생합니다.

- 중복 코드 발생
- 핵심 로직 가독성 저하
- 유지 보수성이 떨어짐

AOP를 사용하면 공통 관심사를 모듈화하고, 필요한 메서드나 클래스에 자동 적용할 수 있습니다.

<br />

### AOP 적용 전 코드

```java

@Service
public class UserService {
    public void create() {
        long start = System.currentTimeMillis();

        /* 주문 처리 로직 */

        long end = System.currentTimeMillis();
        System.out.println("처리 시간 : " + (end - start) + "ms");
    }
}
```

메서드마다 시간 측정 코드를 작성해야 합니다.

<br />

### AOP 적용 후 코드

```java

@Aspect
@Component
public class LoggingAspect {

    @Around("execution(* com.example.service..*(..))")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();

        Object result = joinPoint.proceed(); // 실제 메서드 실행

        long end = System.currentTimeMillis();
        System.out.println(joinPoint.getSignature() + " 처리 시간: " + (end - start) + "ms");

        return result;
    }
}

@Service
public class UserService {
    public void create() {
        /* 주문 처리 로직 */
    }
}

```

핵심 로직과 공통 관심사(시간 측정 코드)가 분리되어 중복 코드가 사라지고, 가독성이 좋아졌습니다.

AOP 핵심 용어

- Aspect : 공통 관심사를 모듈화 한 것 -> LoggingAspect
- Pointcut : Advice가 적용될 지점을 나타내는 표현식-> execution(* com.example.service..*(..))
- Advice : Aspect가 수행하는 실제 작업 -> @Around : 메서드 실행 전후 모두 제어
- JoinPoint : Advice가 실행될 수 있는 지점 -> 메서드 실행, 예외 발생 등

<br />

### 결론

AOP는 핵심 비즈니스 로직과는 별도로 여러 클래스에 흩어져 있는 공통 관심사를 모듈화함으로써, 중복 코드를 제거하고 코드의 가독성과 유지 보수성을 향상시킬 수 있습니다.

<br />

#### 참고 자료

- ChatGPT 대화 내용