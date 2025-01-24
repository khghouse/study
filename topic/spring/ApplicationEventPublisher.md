### ApplicationEventPublisher

스프링에서 제공하는 이벤트 발행 인터페이스로 느슨한 결합과 확장성을 제공합니다.  
이벤트는 이벤트를 발행하는 주체(Publisher)와 이벤트를 처리하는 주체(Listener)로 나뉩니다.

<br />

### 이벤트 발행

이벤트 객체

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

이벤트 발행(발생)

- publishEvent() 메소드를 호출하여 이벤트를 발생시킬 수 있습니다.
- publishEvent()에 전달한 **이벤트 객체의 타입**이 리스너 메서드의 **매개변수 타입**과 일치하거나 상속 관계일 경우, 해당 리스너 메서드가 실행됩니다.
    - 즉, 스프링의 이벤트 기반 처리는 리스너 메서드 이름과 관계없이 일치하는 타입의 메서드가 호출됩니다.

```java

@Service
@RequiredArgsConstructor
public class MailService {

    private final JavaMailSender javaMailSender;
    private final ApplicationEventPublisher eventPublisher;

    public void send(..) {
        try {
            javaMailSender.send(message);
        } catch (MailAuthenticationException e) { // SMTP 서버 인증 실패
            String errorMessage = "SMTP 서버 인증에 실패했습니다.";
            eventPublisher.publishEvent(SmtpAuthenticationFailureEvent.create(errorMessage)); // SMTP 인증 실패 이벤트 발행
        }
    }
}
```

<br />

### 이벤트 처리

이벤트 수신

- 리스너는 이벤트를 수신하고 처리하는 역할을 합니다.
- @EventListener 어노테이션을 통해 핸들러를 구현합니다. 정해진 이벤트(SmtpAuthenticationFailureEvent)가 발생하면 해당 메서드가 실행됩니다.

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

실제 처리 로직

```java

@Service
public class SlackService {
    public void send(String message) { ..}
}
```

<br />

### ApplicationEventPublisher를 사용하는 이유

1. 내부 컴포넌트 간 결합도 감소
2. 동기 및 비동기 처리 지원
3. 확장성 및 유지보수성 향상

<br />

#### 1. 내부 컴포넌트 간 결합도 감소

서비스 간 직접적인 의존을 제거하고 느슨한 결합을 구현합니다. 또한 순환 참조를 방지할 수 있습니다.

```java

@Service
@RequiredArgsConstructor
public class MailService {

    private final SlackService slackService;

    public void send(..) {
        // SMTP 인증 실패 이벤트 발행
        slackService.send("SMTP 서버 인증에 실패했습니다.");
    }

}

@Service
@RequiredArgsConstructor
public class MailService {

    private final ApplicationEventPublisher eventPublisher;

    public void send(..) {
        // SMTP 인증 실패 이벤트 발행
        eventPublisher.publishEvent(SmtpAuthenticationFailureEvent.create("SMTP 서버 인증에 실패했습니다."));
    }
}
```

#### 2. 동기 및 비동기 처리 지원

기본적으로 동기 처리지만 @Async 어노테이션을 사용하면 비동기 이벤트 처리가 가능합니다.

```java

@SpringBootApplication
@EnableAsync // 비동기 기능 활성화
public class MailApplication {

    public static void main(String[] args) {
        SpringApplication.run(MailApplication.class, args);
    }
}

    @Async // 별도의 스레드에서 비동기 처리
    @EventListener
    public void onSmtpAuthenticationFailure(SmtpAuthenticationFailureEvent event) {
        slackService.send(event.getErrorMessage());
    }
```

#### 3. 확장성 및 유지보수성 향상

기존 로직을 수정하지 않고, 새로운 기능을 추가하기 쉽습니다. 또한 코드의 관심사가 명확하게 분리되어 결합도가 낮아지고 유지 보수성이 향상됩니다.

```java
// 기존 로직 유지 : SMTP 인증 실패 이벤트 발행
eventPublisher.publishEvent(SmtpAuthenticationFailureEvent.create(errorMessage));

// 새로운 요구사항 추가 -> SMTP 인증 실패 시 로그 등록 -> 리스너 추가
@Component
public class LoggingListener {

    @EventListener
    public void logRegistration(SmtpAuthenticationFailureEvent event) {
        logService.add(..);
    }
}
```

<br />

#### 참고 자료

- https://devoong2.tistory.com/entry/Spring-Spring-Event-%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%B9%84%EB%8F%99%EA%B8%B0-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EC%B2%98%EB%A6%AC
- https://velog.io/@jeongmin78/Spring-Boot-ApplicationEventPublisher%EB%A5%BC-%ED%86%B5%ED%95%9C-Event-%EC%B2%98%EB%A6%AC
