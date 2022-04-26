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




