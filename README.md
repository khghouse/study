### 📦 패키지 구조

도메인별 패키지 구조로 설계했습니다.

```
board
├── global/                                   # 전역 설정 및 공통 모듈
│   ├── config/                               # Spring 설정 클래스들
│   ├── common/                               # 공통 컴포넌트
│   │   ├── dto/                              # 공통 응답 객체
│   │   │   ├── page/
│   │   │   └── ApiResponse.java
│   │   ├── exception/                        # 글로벌 예외 처리
│   │   │   ├── BadRequestException.java
│   │   │   ├── UnauthorizedException.java
│   │   ├── util/                             # 유틸리티 클래스
│   │   │   ├── CommonUtil.java
│   │   ├── entity/                           # 공통 엔티티 기반
│   │   │   ├── BaseEntity.java
│   │   └── component/                        # 범용 컴포넌트
│   ├── security/                             # 보안 관련 (JWT, 인증/인가)
│   ├── aop/                                  # AOP 관련
│   ├── annotation/                           # 커스텀 어노테이션
│   └── infrastructure/                       # 외부 서비스 연동
│       ├── slack/
│       │   ├── SlackService.java
│       │   ├── SlackChannel.java
│       │   └── dto/
│       │       └── SlackMessageRequest.java
│       └── email/
│           ├── EmailService.java
│           ├── EmailTemplate.java
│           └── dto/
│               └── EmailRequest.java
├── domain/                                  # 비즈니스 도메인
│   ├── article/                             # 사용자 도메인
│   │   ├── controller/
│   │   │   └── ArticleController.java
│   │   │   └── ArticleLikeController.java
│   │   ├── service/
│   │   │   └── ArticleService.java
│   │   │   └── ArticleLikeService.java
│   │   ├── entity/
│   │   │   └── Article.java
│   │   │   └── ArticleLike.java
│   │   ├── repository/
│   │   │   └── ArticleRepository.java
│   │   │   └── ArticleLikeRepository.java
│   │   │   └── ArticleQueryRepository.java
│   │   │   └── ArticleLikeQueryRepository.java
│   │   └── dto/
│   │       ├── request/
│   │       │   ├── ArticleRequest.java
│   │       │   └── ArticleServiceRequest.java
│   │       │   └── ArticleLikeServiceRequest.java
│   │       └── response/
│   │           └── ArticleResponse.java
│   │           └── ArticleDetailResponse.java
│   ├── comment/
│   │   ├── controller/
│   │   ├── service/
│   │   ├── entity/
│   │   ├── repository/
│   │   └── dto/
│   │       ├── request/
│   │       └── response/
└── Application.java
```

<br />
