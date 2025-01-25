### 옵저버 패턴 (Observer Pattern)

옵저버 패턴은 객체 간의 1:N 관계를 정의하여, 한 객체의 상태가 변경될 때 이를 구독하는(관찰하는) 다른 객체들에게 자동으로 알림을 전달하는 디자인 패턴입니다.

#### 구성 요소

- 주체 (Subject) : 이벤트를 발행하는 역할을 수행 (스프링에서 ApplicationEventPublisher)
- 옵저버 (Observer) : 이벤트를 구독하고, 특정 기능을 수행 (스프링에서 @EventListener)
- 이벤트 (Event) : 전달할 데이터를 포함한 객체

<br />

### 옵저버 패턴 적용

#### 이벤트 발행

```java
applicationEventPublisher.publishEvent(SmtpAuthenticationFailureEvent.create(errorMessage));
```

- eventPublisher.publishEvent() : 이벤트를 발행하는 주체의 역할로 구독 중인 리스너에게 이벤트 전달

<br />

#### 이벤트 리스너

이벤트가 발행되었을 때 이를 수신하여 특정 기능을 수행합니다.

```java

@Component
@RequiredArgsConstructor
public class SmtpAuthenticationFailureListener {

    private final SlackService slackService;

    @EventListener
    public void onSmtpAuthenticationFailure(SmtpAuthenticationFailureEvent event) {
        slackService.send(event.getErrorMessage());
    }
}
```

- @EventListener : 특정 이벤트(SmtpAuthenticationFailureEvent)를 구독하는 옵저버
- onSmtpAuthenticationFailure(): 이벤트 수신 후 Slack 알림 발송

<br />

#### 이벤트 객체

리스너가 필요한 데이터를 포함하고 있습니다.

```java

@Getter
@NoArgsConstructor(access = AccessLevel.PRIVATE)
public class SmtpAuthenticationFailureEvent {

    private String errorMessage;

    public static SmtpAuthenticationFailureEvent create(String errorMessage) {
        SmtpAuthenticationFailureEvent object = new SmtpAuthenticationFailureEvent();
        object.errorMessage = errorMessage;
        return object;
    }
}
```

<br />

### 옵저버 패턴의 장단점

#### 장점

- 결합도 감소
    - 서비스 간 결합도를 낮추고, 코드의 유연성을 높임
- 관심사의 분리
    - 코드의 관심사 분리를 통해 가독성 및 관리가 용이
- 확장성 및 유연성 증가
    - 같은 이벤트에 새로운 리스너 추가 시 기존 로직 수정 없이 확장 가능
        - 예) SMTP 인증 실패 시, 기존 슬랙 메시지 발송에 추가로 로깅 기능도 확장 가능

#### 단점

- 디버깅의 어려움
    - 이벤트 흐름을 파악하기 어려울 수 있음
    - 이벤트가 여러 곳에서 발생될 경우 디버깅이 복잡할 수 있음
- 일관성 문제
    - 이벤트가 비동기 처리될 경우 트랜잭션 경계에서 벗어나 일관성이 깨질 수 있음
