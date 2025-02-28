### standaloneSetup()

스프링 컨텍스트를 로드하지 않고 컨트롤러만 독립적으로 테스트할 때 사용되는 메서드입니다.  
빠른 단위 테스트 환경을 제공하며 스프링 시큐리티, @ControllerAdvice, Filter, Interceptor와 같은 전역 설정은 적용되지 않습니다.  
해당 설정이 필요하지 않는 컨트롤러 테스트나 RestDocs 테스트 작성에 사용할 수 있습니다.

```java
@BeforeEach
void setUp(RestDocumentationContextProvider provider) {
    this.mockMvc = MockMvcBuilders.standaloneSetup(new ArticleController(articleService))
            .apply(documentationConfiguration(provider))
            .build();
}
```

<br />

### standaloneSetup()에서 @AuthenticationPrincipal을 주입하는 방법

standaloneSetup()은 테스트 대상 컨트롤러만 독립적으로 실행하기 때문에 webAppContextSetup() 환경에서 @AuthenticationPrincipal을 주입하기 위해 사용하는 SecurityMockMvcRequestPostProcessors.user() 또는 커스텀 어노테이션은 동작하지 않습니다.

standaloneSetup() 환경에서는 HandlerMethodArgumentResolver를 구현하여 MockMvcBuilders에 세팅해야 합니다.

<br />

### HandlerMethodArgumentResolver

컨트롤러 메서드의 파라미터를 처리하는 방식을 커스텀할 수 있습니다.

```java
// 커스텀 객체를 주입할 타겟 -> SecurityUser 파라미터
@PostMapping
public ApiResponse createArticle(@AuthenticationPrincipal SecurityUser securityUser) { .. }

// CustomArgumentResolvers 등록
@BeforeEach
void setUp(RestDocumentationContextProvider provider) {
    this.mockMvc = MockMvcBuilders.standaloneSetup(new ArticleController(articleService))
            .setCustomArgumentResolvers(new SecurityUserArgumentResolver()) // SecurityUserArgumentResolver 추가
            .apply(documentationConfiguration(provider))
            .build();
}

// SecurityUserArgumentResolver 정의
public class SecurityUserArgumentResolver implements HandlerMethodArgumentResolver {

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        return parameter.getParameterType()
                .isAssignableFrom(SecurityUser.class);

        // 파라미터가 @AuthenticationPrincipal을 사용한 경우에만 처리
        // return parameter.hasParameterAnnotation(AuthenticationPrincipal.class);
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) {
        // 실제로 주입할 객체를 반환
        return new SecurityUser(1L, "khghouse@naver.com", List.of(new SimpleGrantedAuthority("ROLE_USER")));
    }

}
```

<br />

### 결론

standaloneSetup()에서는 스프링 컨텍스트를 로드하지 않기 때문에 스프링 시큐리티 환경 또한 로드되지 않습니다.  
따라서 SecurityUser와 같은 UserDetails 구현체를 주입하기 위해서는 CustomArgumentResolvers를 정의, 등록하여야 합니다.

<br />

### 관련 코드
- https://github.com/khghouse/board/blob/master/src/test/java/com/board/support/RestDocsSupport.java
- https://github.com/khghouse/board/blob/master/src/test/java/com/board/support/security/SecurityUserArgumentResolver.java
