### 단일 책임 원칙 (Single Responsibility Principle)

하나의 클래스는 단 하나의 책임(= 변경 이유)만 가져야 한다는 원칙입니다.
예를 들어 UserService는 사용자 관리라는 하나의 책임을 가져야 합니다.

<br />

### 왜 중요한가?

하나의 클래스가 여러 책임을 갖는다면 다음과 같은 문제점이 발생합니다.

- 유지 보수의 어려움 : 특정 기능을 변경할 때, 다른 기능에 영향을 줄 수 있음
- 재사용성 저하 : 특정 기능을 재사용하려면 불필요한 코드까지 포함된 클래스를 사용해야 함
- 테스트하기 어려움 : 특정 기능만 테스트하기 어려움
- 코드 가독성 저하 : 한 클래스에 너무 많은 기능이 포함되면 코드가 길어지고 가독성이 떨어짐

<br />

### 단일 책임 원칙을 적용했을 때의 장점

- 유지보수가 용이: 특정 기능을 변경할 때, 다른 기능에 영향을 줄 가능성이 낮아짐
- 재사용성 증가 : 하나의 책임만 수행하는 클래스는 다양한 곳에서 독립적으로 재사용 가능
- 테스트 용이 : 단위 테스트가 쉬워짐
- 코드 가독성 향상 : 클래스의 역할이 명확해져 코드를 이해하기 쉬워짐

<br />

### 단일 책임 원칙을 지키지 않은 코드

```java
public class UserService {
    public void createUser(User user) {
        // 1. DB 저장
        userRepository.save(user);

        // 2. 이메일 전송 (직접 구현)
        SmtpClient client = new SmtpClient();
        client.connect("smtp.example.com");
        client.send(
                "welcome@example.com",
                user.getEmail(),
                "Welcome!",
                "회원가입을 환영합니다!"
        );

        // 3. 로깅 (직접 구현)
        File logFile = new File("user_activity.log");
        logFile.append(user.getName() + "가 가입했습니다.");
    }
}
```

DB 저장, 이메일 전송, 로깅 등 너무 많은 책임을 가집니다.

<br />

### 단일 책임 원칙을 적용한 코드

```java
public class UserService {
    private final UserRepository userRepository;
    private final EmailService emailService;
    private final UserLogger userLogger;

    public UserService(UserRepository userRepository, EmailService emailService, UserLogger userLogger) {
        this.userRepository = userRepository;
        this.emailService = emailService;
        this.userLogger = userLogger;
    }

    public void createUser(User user) {
        userRepository.save(user);
        emailService.sendWelcomeEmail(..);  // 이메일 전송 위임
        userLogger.logActivity(..); // 로깅 위임
    }
}

```

이메일 전송 로직을 변경해야 한다면 emailService만 수정하면 됩니다. -> 책임 분리

<br />

### 결론

- 단일 책임 원칙은 SOLID 원칙의 첫 번째 원칙으로 변경에 유연한 시스템을 구축하고, 코드 품질을 향상시킬 수 있습니다.
- 클래스를 설계하고 구현할 때 "이 클래스가 변경되어야 하는 이유가 정말 하나인가?"라는 질문을 스스로에게 던져보는 것도 좋은 방법입니다.

<br />

#### 참고 자료

- ChatGPT 대화 내용