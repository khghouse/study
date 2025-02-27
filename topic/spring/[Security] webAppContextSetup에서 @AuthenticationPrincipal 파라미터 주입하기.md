### @AuthenticationPrincipal

스프링 시큐리티에서 인증된 사용자 정보를 주입하기 위해 사용하는 어노테이션입니다.  
이를 활용하여 컨트롤러 메서드에서 현재 로그인한 사용자 정보를 가져올 수 있습니다.

```java
@PostMapping
public ApiResponse createArticle(@AuthenticationPrincipal SecurityUser securityUser) {
    return ApiResponse.ok(articleService.createArticle(securityUser.getMemberId()));
}
```

<br />

### @WebMvcTest

컨트롤러 테스트를 위해 사용하는 어노테이션입니다.  
전체 애플리케이션 컨텍스트를 로드하지 않고, 웹 관련 빈들만 로드합니다.

<br />

### webAppContextSetup()

스프링 애플리케이션 컨텍스트를 로드하여 테스트하는 방식으로 @WebMvcTest는 기본적으로 webAppContextSetup()을 사용합니다.  
스프링 시큐리티, @ControllerAdvice, Filter, Interceptor 등이 적용됩니다.

```java
MockMvc mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext)
        .build();
```

<br />

### @WebMvcTest에서 @AuthenticationPrincipal을 주입하는 방법

<br />

#### 1. @WithMockUser

스프링 시큐리티에서 제공하는 @WithMockUser를 사용하여 주입할 수 있습니다.  
간단하게 테스트 가능하나 UserDetails 기본 구현체인 User 객체만 주입 가능합니다.

```java
import org.springframework.security.test.context.support.WithMockUser;

@Test
@DisplayName("게시글을 등록하고 정상 응답한다.")
@WithMockUser(username = "user", roles = "USER")
void createArticle() throws Exception { .. }
```

<br />

#### 2. SecurityMockMvcRequestPostProcessors.user() 사용

스프링 시큐리티 테스트에서 제공하는 션 활용하면 @AuthenticationPrincipal을 주입받을 수 있습니다.  
커스텀 필드가 포함된 SecurityUser와 같은 UserDetails 구현체도 테스트 가능하지만 반복 사용 시 중복 코드가 발생할 수 있습니다.

```java
import static org.springframework.security.test.web.servlet.request.SecurityMockMvcRequestPostProcessors.user;

@Test
@DisplayName("게시글을 등록하고 정상 응답한다.")
void createArticle() throws Exception {
    // given
    SecurityUser securityUser = new SecurityUser(1L, "khghouse@naver.com", List.of(new SimpleGrantedAuthority("ROLE_USER")));
    
    // when, then
    mockMvc.perform(post("/api/v1/articles")
        .with(user(securityUser))
        .with(csrf())
        .contentType(MediaType.APPLICATION_JSON)
        .content(objectMapper.writeValueAsString(request)))
    .andDo(print())
    .andExpect(status().isOk());
}
```

<br />

#### 3. 커스텀 어노테이션 사용

커스텀 어노테이션을 생성하여 @AuthenticationPrincipal을 주입받을 수 있습니다.  
어노테이션을 통해 여러 테스트에서 재사용 가능하며 코드가 간결해집니다. 다만 어노테이션, 팩토리 클래스 생성 등 추가 설정이 필요합니다.

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@WithSecurityContext(factory = CustomSecurityUserFactory.class)
public @interface WithCustomSecurityUser {

    long memberId() default 1L;

    String username() default "khghouse@naver.com";

    String role() default "USER";

}

public class CustomSecurityUserFactory implements WithSecurityContextFactory<WithCustomSecurityUser> {

    @Override
    public SecurityContext createSecurityContext(WithCustomSecurityUser annotation) {
        SecurityContext context = SecurityContextHolder.createEmptyContext();

        SecurityUser securityUser = new SecurityUser(annotation.memberId(), annotation.username(), List.of(new SimpleGrantedAuthority("ROLE" + annotation.role())));

        Authentication auth = new UsernamePasswordAuthenticationToken(securityUser, null, securityUser.getAuthorities());
        context.setAuthentication(auth);

        return context;
    }

}

@Test
@DisplayName("게시글을 등록하고 정상 응답한다.")
@WithCustomSecurityUser
void createArticle() throws Exception { .. }
```

<br />

### 결론

@WebMvcTest 환경에서 @AuthenticationPrincipal을 주입하는 방법은 여러 가지가 있으며, 간결하고 유지보수성이 높은 커스텀 어노테이션 사용이 권장됩니다.

<br />

### 관련 코드
- https://github.com/khghouse/board/blob/master/src/test/java/com/board/support/security/WithCustomSecurityUser.java
- https://github.com/khghouse/board/blob/master/src/test/java/com/board/support/security/CustomSecurityUserFactory.java
- https://github.com/khghouse/board/blob/master/src/test/java/com/board/support/ControllerTestSupport.java