# Spring Security와 OAuth 2.0으로 로그인 기능 구현
  - 스프링 시큐리티는 막강한 인증과 권한 부여 기능을 가진 프레임워크

# 스프링 시큐리티와 스프링 시큐리티 OAuth2 클라이언트

# 구글 서비스 등록
  - 구글 클라우드 플랫폼 주소로 이동
  - 프로젝트 생성
  - API 및 서비스 대시보드 이동
  - 사용자 인증 정보 > 사용자 인증 정보 만들기
  - OAuth 클라이언트 ID 만들기 > 동의 화면ㅁ 구성 버튼 클릭
  - OAuth 동의 화면 입력
  - OAuth 클라이언트 ID 만들기
  - 승인된 리다이렉트 URL
#### 클라이언트 ID와 클라이언트 보안 비밀코드를 프로젝트에 설정

application-oauth.properties
```
spring.security.oauth2.client.registration.google.client-id=클라이언트 ID
spring.security.oauth2.client.registration.google.client-secret=클라이언트 보안 비밀번호
spring.security.oauth2.client.registration.google.scope=profile,email
```

applications.properties 추가
```
spring.profiles.include=oauth
```
gitignore 등록
```
application-oauth.properties
```
  - 보안을 위해 깃허브에 application-oauth.properties 파일이 올라가는 것 방지 
 
# 구글 로그인 연동
#### User 클래스 생성
``` java
package com.example.awsProject.domain;

import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

import javax.persistence.*;

@Getter
@NoArgsConstructor
@Entity
public class User extends BaseTimeEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false)
    private String email;

    @Column
    private String picture;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private Role role;

    @Builder
    public User(String name, String email, String picture, Role role) {
        this.name = name;
        this.email = email;
        this.picture = picture;
        this.role = role;
    }

    public User update(String name, String picture) {
        this.name = name;
        this.picture = picture;

        return this;
    }

    public String getRoleKey() {
        return this.role.getKey();
    }
}

```
  - @Enumerated(EnumType.STRING)
    * JPA로 데이터베이스에 저장할 때 Enum 값을 어떤 형태로 저장할지 결정
    * 기본적으로 int로 숫자가 저장
    * 숫자로 저장되면 데이터베이스로 확인할 때 그 값이 무슨 코드를 의미하는지 알 수 없음
    * 그래서 문자열(EnumType.STRING)로 지정될 수 있도록 선언
 
 #### Role 클래스 생성
``` java
package com.example.awsProject.domain;

import lombok.Getter;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor
public enum Role {

    GUSET("ROLE_GUEST", "손님"),
    USER("ROLE_USER", "일반 사용자");

    private final String key;
    private final String title;
}
```

#### UserRepository 생성
``` java
package com.example.awsProject.user;

import com.example.awsProject.domain.User;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface UserRepository extends JpaRepository<User, Long> {

    Optional<User> findByEmail(String email);
}
```

#### 스프링 시큐리티 설정

> build.gradle에 추가 
> > implementation 'org.springframework.boot:spring-boot-starter-oauth2-client' 

  - spring-boot-starter-oauth2-client
    * 소셜 로그인 등 클라이언트 입장에서 소셜 기능 구현 시 필요한 의존성
    * spring-boot-starter-oauth2-client와 spring-security-oauth2-jose를 기본으로 관리해줌

#### SecurityConfig 클래스 생성
``` java
package com.example.awsProject.config.auth;

import com.example.awsProject.domain.Role;
import com.example.awsProject.user.CustomOAuth2UserService;
import lombok.RequiredArgsConstructor;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@RequiredArgsConstructor
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    private final CustomOAuth2UserService customOAuth2UserService;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .csrf().disable()
                .headers().frameOptions().disable()
                .and()
                .authorizeRequests()
                .antMatchers("/", "/css/**", "/images/**",
                        "/js/**", "/h2-console/**").permitAll()
                .antMatchers("/api/v1/**").hasRole(Role.
                        USER.name())
                .anyRequest().authenticated()
                .and()
                .logout()
                .logoutSuccessUrl("/")
                .and()
                .oauth2Login()
                .userInfoEndpoint()
                .userService(customOAuth2UserService);
    }
}
```
코드 설명
  - @EnableWebSecurity
    * Spring Security 설정들을 활성화
  - .csrf().disable().headers().frameOptions().disable()
    * h2-console 화면을 사용하기 위해 해당 옵션들을 disable 
  - authorizeRequests
    * URL별 권한 관리를 설정하는 옵션의 시작점
    * authorizeRequests가 선언되어야만 antMatchers 옵션이 사용 가능
  - antMatches
    * 권한 관리 대상을 지정하는 옵션
    * URL, HTTP 메소드별로 관리 가능
    * "/"등 지정된 URL들은 permitAll() 옵션을 통해 전체 열람 권한을 부여
    * "/api/v1/**"주소를 가진 API는 USER 권한을 가진 사람만 가능하도록 설정
  - anyRequest
    * 설정된 값들 이외 나머지 URL들을 나타냄
    * 여기서는 authenticated()을 추가하여 나머지 URL들은 모두 인정된 사용자들에게만 허용
  - logout().logoutSuccessUrl("/")
    * 로그아웃 기능에 대한 여러 설정의 진입점
    * 로그아웃 성공 시 /주소로 이동
  - oauth2Login
    * OAuth 2 로그인 기능에 대한 열 ㅓ설정의 진입점
  - userInfoEndpoint
    * OAuth 2 로그인 성공 이후 사용자 정보를 가져올 때의 설정들을 담당
  - userService
    * 소셜 로그인 성공 시 후속 조치를 진행할 UserService 인터페이스의 구현체를 등록
    * 리소스 서버(즉, 소셜 서비스들)에서 사용자 정보를 가져온 상태에서 추가로 진행하고자 하는 기능을 명시 가능
    
#### CustomOAuth2UserService 클래스 생성
이 클래스에서는 구글 로그인 이후 가져온 사용자의 정보(email, name, picture 등)들을 기반으로 가입 및 정보수정, 세션 저장 등의 기능 지원

``` java
package com.example.awsProject.user;

import com.example.awsProject.domain.User;
import lombok.RequiredArgsConstructor;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.oauth2.client.userinfo.DefaultOAuth2UserService;
import org.springframework.security.oauth2.client.userinfo.OAuth2UserRequest;
import org.springframework.security.oauth2.client.userinfo.OAuth2UserService;
import org.springframework.security.oauth2.core.OAuth2AuthenticationException;
import org.springframework.security.oauth2.core.user.DefaultOAuth2User;
import org.springframework.security.oauth2.core.user.OAuth2User;
import org.springframework.stereotype.Service;

import javax.servlet.http.HttpSession;
import java.util.Collections;

@RequiredArgsConstructor
@Service
public class CustomOAuth2UserService implements OAuth2UserService<OAuth2UserRequest, OAuth2User> {
    private final UserRepository userRepository;
    private final HttpSession httpSession;

    @Override
    public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException{
        OAuth2UserService delegate = new DefaultOAuth2UserService();
        OAuth2User oAuth2User = delegate.loadUser(userRequest);

        String registrationId = userRequest.getClientRegistration().getRegistrationId();
        String userNameAttributeName = userRequest.getClientRegistration()
                .getProviderDetails().getUserInfoEndpoint().getUserNameAttributeName();

        OAuthAttributes attributes = OAuthAttributes
                .of(registrationId, userNameAttributeName, oAuth2User.getAttributes());

        User user = saveOrUpdate(attributes);
        httpSession.setAttribute("user", new SessionUser(user));

        return new DefaultOAuth2User(
                Collections.singleton(new SimpleGrantedAuthority(user.getRoleKey())),
                attributes.getAttributes(),
                attributes.getNameAttributeKey());
    }

    private User saveOrUpdate(OAuthAttributes attributes) {
        User user = userRepository.findByEmail(attributes.getEmail())
                .map(entity -> entity.update(attributes.getName(), attributes.getPicture()))
                .orElse(attributes.toEntity());
        return userRepository.save(user);
    }
}

```
#### 코드설명
  - registrationId
    * 현재 로그인 진행 중인 서비스를 구분하는 코드
    * 지금은 구글만 사용하는 불필요한 값, 이후 네이버 로그인 연동시 네이버 로그인인지, 구글 로그인인지 구분하기 위한 용도
  - userNameAttributeName
    * OAuth2 로그인 진행 시 키가 되는 필드값 (Primary Key와 같은 의미)
  - OAuthAttributes
    * OAuth2UserSErvice를 통해 가져온 OAuth2User의 attribut를 담을 클래스
    * 이후 네이버 다른 소셜 로그인도 이 클래스를 사용
  - SessionUser
    * 세선에 사용자 정보를 저장하기 위한 Dto 클래스
 
 #### OAuthAttributes.java
 ``` java
 package com.example.awsProject.user;

import com.example.awsProject.domain.Role;
import com.example.awsProject.domain.User;
import lombok.Builder;
import lombok.Getter;

import java.util.Map;

@Getter
public class OAuthAttributes {
    private Map<String, Object> attributes;
    private String nameAttributeKey;
    private String name;
    private String email;
    private String picture;

    @Builder
    public OAuthAttributes(Map<String, Object> attributes,
                           String nameAttributeKey, String name,
                           String email, String picture) {
        this.attributes = attributes;
        this.nameAttributeKey= nameAttributeKey;
        this.name = name;
        this.email = email;
        this.picture = picture;
    }

    public static OAuthAttributes of(String registrationId,
                                     String userNameAttributeName,
                                     Map<String, Object> attributes) {
        return ofGoogle(userNameAttributeName, attributes);
    }

    private static OAuthAttributes ofGoogle(String userNameAttributeName,
                                            Map<String, Object> attributes) {
        return OAuthAttributes.builder()
                .name((String) attributes.get("name"))
                .email((String) attributes.get("email"))
                .picture((String) attributes.get("picture"))
                .attributes(attributes)
                .nameAttributeKey(userNameAttributeName)
                .build();
    }


    public User toEntity() {
        return User.builder()
                .name(name)
                .email(email)
                .picture(picture)
                .role(Role.GUSET)
                .build();
    }
}
 ```
 
 #### SessionUser
 ``` java
 package com.example.awsProject.user;

import com.example.awsProject.domain.User;
import lombok.Getter;

import java.io.Serializable;

@Getter
public class SessionUser implements Serializable {
    private String name;
    private String email;
    private String picture;

    public SessionUser(User user) {
        this.name = user.getName();
        this.email = user.getEmail();
        this.picture = user.getPicture();
    }
}
   ```
   - @Entity User 클래스를 SessionUser로 사용하지 말아야함
   - Entity 클래스는 직렬화 코드를 넣지 않는게 좋음
   - @OneToMany, @ManyToMany등 자식 엔티티를 가지고 있다면 직렬화 대상에 자식들까지 포함됨 성능 이슈, 부수 효과가 발생 가능
   - 때문에 직렬화 기능을 가진 세션 Dto를 하나 추가로 만들어줌
  
# 어노테이션 기반으로 개선
  다른 컨트롤러와 메소드에서 세션값이 필요하다면 그때마다 직접 세션에서값을 가져와야함. 이 부분을 메소드 인자로 세션값을 받을 수 있또록 변경 가능
  
 @LoginUser 어노테이션 생성
 ``` java
 
 package com.example.awsProject.config.auth;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface LoginUser {
}
 
 ```
  - @Target(ElementType.PARAMETER)
    * 이 어노테이션이 생성도리 수 있는 위치를 지정
    * PARAMETER로 지정했으니 메소드의 파라미터로 선언된 객체에서만 사용 가능
  - @interface
    * 이 파일을 어노테이션 클래스로 지정
    * LoginUser라는 이름을 가진 어노테이션이 생성된 것
  - @Retention(RetentionPolicy.RUNTIME)
    * 컴파일 이후에도 JVM에 의해서 참조 가능
   
#### HandlerMethod ArgumentResolver 인터페이스를 구현한 LoginUserArgumentResolver 클래스를 생성
  - 조건에 맞는 경우 메소드가 있다면 HandlerMethodArgumentResolver의 구현체가 지정한 값으로 해당 메소드의 파라미터를 넘김
 ``` java
 package com.example.awsProject.config.auth;

import com.example.awsProject.user.SessionUser;
import lombok.RequiredArgsConstructor;
import org.springframework.core.MethodParameter;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.support.WebDataBinderFactory;
import org.springframework.web.context.request.NativeWebRequest;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.method.support.ModelAndViewContainer;

import javax.servlet.http.HttpSession;

@RequiredArgsConstructor
@Component
public class LoginUserArgumentResolver implements HandlerMethodArgumentResolver {

    private final HttpSession httpSession;

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        boolean isLoginUserAnnotation = parameter.getParameterAnnotation(LoginUser.class) != null;
        boolean isUserClass = SessionUser.class.equals(parameter.getParameterType());

        return isLoginUserAnnotation && isUserClass;
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
        return httpSession.getAttribute("user");
    }
}
 ```
 코드 설명
  - supportParameter 
    * 컨트롤러 메서드의 특정 파라미터를 지원하는지 판단
    * 여기서 파리미터에@LoginUser 어노테이션이 붙어 있고, 파라미터 클래스 타입이 SessionUser.class인 경우 true를 반환
  - resolveArgument
    * 파라미터에전달할 객체를 생성

#### LoginUserArgumentResolver를 스프링에서 인식될 수 있도록 WebMvcConfigurer 추가해야함
WebConfig 클래스 생성 및 설정
``` java
package com.example.awsProject.config.auth;

import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import java.util.List;

@RequiredArgsConstructor
@Configuration
public class WebConfig implements WebMvcConfigurer {

    private final LoginUserArgumentResolver loginUserArgumentResolver;

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers){
        argumentResolvers.add(loginUserArgumentResolver);
    }
}

```
  - HandlerMethodArgumentResolver는 항상 WebMvcConfigurer의 addArgumentResolvers를 사용해 추가해야함
 
 ### 기존에 IndexController 코드 주성
 ``` java
     @GetMapping("/")
    public String index(Model model, @LoginUser SessionUser user) {
        model.addAttribute("posts", postService.findAllDesc());


        if(user != null){
            model.addAttribute("userName", user.getName());
        }

        return "index";
    }
 ```
 코드 설명
  - @LoginUser SessionUser user
    * 기존에 (User)httpSession.getAttribut("user")로 가져오던 세션 정보 개선
    * 어느 컨트롤러든 @LoginUser만 사용하면 세션 정보를 가져올 수 있음

# 세션 저장소로 데이터베이스 사용하기
  세션 저장소에 대해 다음의 3가지 중 한 가지를 선택
    - 톰캣 세션
      * 일반적으로 별다른 설정을 하지 않을 때 기본적으로 선택
      * 톰캣(WAS)에 세션이 저장되기 때문에 2대 이상의 WAS가 구동되는 환경에서는 톰캣들 간의 세션 공유를 위한 추가 설정 필요
    - MySQL과 같은 데이터베이스를 세션 저장소로 사용
      * 여러 WAS 간의 공용 세션을 사용할 수 있는 가장 쉬운 방법
      * 많은 설정이 필요없지만, 결국 로그인 요청마다 DB IO가 발생하여 성능상 이슈가 발생할 수 있음
      * 보통 로그인 요청이 많이 없는 백오피스, 사내 시스템 용도에서 사용
    - Redis, Memcached와 같은 메모리 DB를 세션 저장소로 사용
      * B2C 서비스에서 가장 많이 사용하는 방식
      * 실제 서비스로 사용하기 위해서는 Embedded Redis와 같은 방식이 아닌 외부 메모리 서버가 필요
 
### spring-session-jdbc 등록

#### Buidler.gradle
```
implementation 'org.springframework.boot:spring-session-jdbc'
```
#### application.properties
```
spring.session.store-type=jdbc
```

# 네이버 로그인

  - https://developers.naver.com/apps/#/register?api=nvlogin
  - 네이버 서비스 등록 
  - 로그인 오픈 API 서비스 환경 설정
  - 등록 완료 시 ClientID와 ClientSecret이 생성
  - 해당 키값들을 application-oauth.properties에 등록
  - 네이버에서는 스프링 시큐리티를 공식 지원하지 않음 
  - 그동안 Common-OAuth2Provider에서 해주던 값들도 전부 수동 입력 해야함

#### application-oauth.properties
```
# registration
spring.security.oauth2.client.registration.naver.client-id=클라이언트ID
spring.security.oauth2.client.registration.naver.client-secret=클라이언트비밀번호
spring.security.oauth2.client.registration.naver.redirect-uri={baseUrl}/{action}/oauth2/code/{registrationId}
spring.security.oauth2.client.registration.naver.authorization-grant-type=authorization_code
spring.security.oauth2.client.registration.naver.scope=name,email.profile_image
spring.security.oauth2.client.registration.naver.client-name=Naver

# provider
spring.security.oauth2.client.provider.naver.authorization-uri=https://nid.naver.com/oauth2.0/authorize
spring.security.oauth2.client.provider.naver.token-uri=https://nid.naver.com/oauth2.0/token
spring.security.oauth2.client.provider.naver.user-info-uri=https://openapi.naver.com/v1/nid/me
spring.security.oauth2.client.provider.naver.user-name-attribute=response
```
코드설명
  - user-name-attribute=response
    * 기준이 되는 user_name의 이름을 네이버에서는 response로 해야함
    * 이유는 네이버의 회원 조회 시 반환되는 JSON 형태이기 때문

### 스프링 시큐리티 설정 등록
 #### OAuthAttributes.java 추가
 ``` java
  '''
    public static OAuthAttributes of(String registrationId,
                                    String userNameAttributeName,
                                    Map<String, Object> attributes) {
       if("naver".equals(registrationId)) {
           return ofNaver("id", attributes);
       }
       
       return ofGoogle(userNameAttributeName, attributes);
   }
   '''
     private static OAuthAttributes ofNaver(String userNameAttributeName,
                                            Map<String, Object> attributes) {
        Map<String, Object> response = (Map<String, Object>) attributes.get("response");

        return OAuthAttributes.builder()
                .name((String) attributes.get("name"))
                .email((String) attributes.get("email"))
                .picture((String) attributes.get("profile_image"))
                .attributes(response)
                .nameAttributeKey(userNameAttributeName)
                .build();
    }
 ```

# 기존 테스트에 시큐리티 적용
#### application.properties(test)

#### build.gradle

#### PostsApiControllerTest
``` java
package com.example.awsProject.dto;

import com.example.awsProject.post.PostRepository;
import com.example.awsProject.post.Posts;
import org.junit.After;
import org.junit.Before;
import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.core.AutoConfigureCache;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.context.runner.WebApplicationContextRunner;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpMethod;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.security.test.context.support.WithMockUser;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MockMvcBuilder;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.*;
import static org.springframework.security.test.web.servlet.setup.SecurityMockMvcConfigurers.springSecurity;

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class PostsApiControllerTest {

    @LocalServerPort
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private PostRepository postRepository;

    @Autowired
    private WebApplicationContext context;

    private MockMvc mvc;

    @Before
    public void setup() {
        mvc = MockMvcBuilders
                .webAppContextSetup(context)
                .apply(springSecurity())
                .build();
    }

    @After
    public  void tearDown() throws Exception {
        postRepository.deleteAll();
    }



    @Test
    @WithMockUser(roles = "USER")
    public void Posts_등록된다() throws Exception {
        //given
        String title = "title";
        String contents = "content";
        PostsSaveRequestDto requestDto = PostsSaveRequestDto.builder()
                .title(title)
                .content(contents)
                .author("author")
                .build();
        String url = "http://localhost:" + port + "/api/v1/posts";

        //when
        ResponseEntity<Long> responseEnitty = restTemplate.postForEntity(url, requestDto, Long.class);

        //then
        assertThat(responseEnitty.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(responseEnitty.getBody()).isGreaterThan(0L);

        List<Posts> all = postRepository.findAll();
        assertThat(all.get(0).getTitle()).isEqualTo(title);
        assertThat(all.get(0).getContent()).isEqualTo(contents);
    }

    @Test
    @WithMockUser(roles = "USER")
    public void Posts_수정된다() throws Exception {
        //given
        Posts savePosts = postRepository.save(Posts.builder()
                .title("title")
                .content("content")
                .author("author")
                .build());

        Long updateId = savePosts.getId();
        String expectedTitle = "title2";
        String expectedContent = "content2";
        PostsUpdateRequestDto requestDto = PostsUpdateRequestDto.builder()
                .title(expectedTitle)
                .content(expectedContent)
                .build();

        String url = "http://localhost:" + port + "/api/v1/posts/" + updateId;


        HttpEntity<PostsUpdateRequestDto> requestEntity = new HttpEntity<>(requestDto);

        //when
        ResponseEntity<Long> responseEntity = restTemplate.exchange(url, HttpMethod.PUT, requestEntity, Long.class);

        //then
        assertThat(responseEntity.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(responseEntity.getBody()).isGreaterThan(0L);

        List<Posts> all = postRepository.findAll();
        System.out.println(all.get(0).getTitle());
        assertThat(all.get(0).getTitle()).isEqualTo(expectedTitle);
        assertThat(all.get(0).getContent()).isEqualTo(expectedContent);
    }

}
```
코드설명
  - @WithMockUser(roles = "USER")
    * 인증된 모의 사용자를 만들어서 사용
    * roles에 권한 추가 가능
    * ROLE_USER 권한을 가진 사용자가 API를 요청하는 것과 동일한 효과

### 문제 @WebMvcTest에서 CustomOAuth2UserService을 찾을 수 없음
  - @WebMvcTest는 WebSecurityConfigurerAdapter, WebMvcConfigurer를 비롯한 
@ControllerAdvice, @Controller를 읽음. 즉, @Repository, @Service, @Component는 스캔 대상 아님 
    * 스킨 대상에서 SecurityConfig를 제거
    * @WithMockUser를 사용해서 가짜로 인증된 사용자를 생성
    

