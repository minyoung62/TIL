# Spring Boot에서 JPA 사용하기
### JPA(Java Persistence API)란
  - JPA는 여러 ORM 전문가가 참여한 EJB 3.0 스펙 작업에서 기존 EJB ORM이던 Entity Bean을 JPA라고 바꾸고 JavaSe, JavaEE를
영속성(persistence) 관리와 ORM을 위한 표준 기술이다. JPA는 ORM 표준 기술로 Hibernate, OpenJPA, EclipseLink, TopLink Essentials
과 같은 구현체가 있고 이에 표준 인턴페이스가 바로 JPA이다

  - ORM(Object Relational Mapping)이란 RDB 테이블을 객체지향적으로 사용하기 위한 기술이다. RDB 테이블은 객체지향적 특징(
상속, 다형성, 레퍼런스, 오브젝트 등)이 없고 자바와 같은 언어로 접근하기 쉽지 않다. 때문에 ORM을 사용해 오브젝트와 RDB 사이에 존재하는
개념과 접근을 객체지향적으로 다루기 위한 기술

  - 장점
    * 객체지향적으로 데이터를 관리할 수 있기 때문에 비지니스 로직에 집중 가능 및 객체지향 개발이 가능
    * 테이블 생성, 변경, 관리가 용이
    * 로직을 쿼리에 집중하기 보다는 개체자체에 집중 가능
    * 빠른 개발 가능
  - 단점
    * 어렵움 장점을 더 극대화 하기위해서는 많은 것을 숙지
    * 잘 이해하고 사용하지 않으면 데이터 손실 발생 가능 (persistence context)
    * 성능상 문제가 있을 수 있음 (잘 이해해야 해결 가능)
    
  - ORM(Object-relationa-Mapping) 이란
    * 객체는 객체대로 설계하고, 관계형 데이터베이스는 관계형 데이터베이스대로 설계
    * ORM 프레임워크가 중간에서 매핑
    * ORM은 객체와 RDB 두 기둥 위에 있는 기술
   
### 요구사항 분석 
 #### jpa 기능을 사용하여 게시판과 회원 기능을 구현
 > *Post 기능*
>	- post 조회
>	- post 등록
>	- post 수정
>	- post 삭제

 > *member 기능*
>	- 구굴/네이버 로그인
>	- 로그인한 사용자 글 작성 권한
>	- 본인 작성 글에 대한 권한 관리

### Spring Data JPA 적용하기

#### Entity 클래스 생성
Posts.class
``` java
package com.example.awsProject.post;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

import javax.persistence.*;

@Getter
@Builder
@AllArgsConstructor
@NoArgsConstructor
@Entity
public class Posts {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(length = 500, nullable = false)
    private String title;

    @Column(columnDefinition = "TEXT", nullable = false)
    private String content;

    private String author;
}


```
#### 코드설명

  - @Enitty
    * 테이블과 링크될 클래스임을 나타냄
    * 기본값으로 크래스의 카멜케이스 이름을 언더 스커어 네이밍(_)으로 테이블 이름을 매칭
    * ex) PostManager.java -> post_manager table
  
  - @GeneratedValue
    * PK 생성 규칙
    * 스프링 부트 2.0에서는 Generation.IDENTITY 옵션을 추가해야만 auto_increment가 됨
  
  - @Id
    * 해당 테이블의 PK 필드를 나타냄
  
  - @Column
    * 테이블의 컬럼을 나타내며 굳이 선언하지 않더라도 해당 클래스의 필드는 모두 컬럼 처리
    * 기본값 외에 추가 옵션을 사용할 때 사용
  
  - @Builder
    * 해당 클래스의 빌더 패턴 클래스를 생성

#### Post 클래스로 Database를 접근하게 해 줄 JpaRepository를 생성

``` java
package com.example.awsProject.post;

import org.springframework.data.jpa.repository.JpaRepository;


public interface PostRepository extends JpaRepository<Posts, Long> {
}
```
  - 인터페이스 생성 후 JpaRepository<Entity 클래스, PK 타입>을 상속하면 기본적인 CRUD 메소드가 자동으로 생성 
  - @Repository를 추가할 필요 없음
  - Enitty 클래스와 기본 Enitty Repository는 함께 위치해야 함

# Spring Data JPA 테스트 코드 작성
  - PostRepositoryTest 클래스 생성
``` java
package com.example.awsProject.post;

import org.junit.After;
import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.List;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;
import static org.junit.jupiter.api.Assertions.*;

@RunWith(SpringRunner.class)
@SpringBootTest
class PostRepositoryTest {

    @Autowired
    PostRepository postRepository;

    @After
    public void cleanup() {
        postRepository.deleteAll();
    }

    @Test
    public void 게시글저장_불러오기() throws Exception {
        //given
        String title = "테스트 게시글";
        String content = "테스트 본문";

        postRepository.save(Posts.builder()
                .title(title)
                .content(content)
                .author("asdf@gmail.com")
                .build());
        //when
        List<Posts> postsList = postRepository.findAll();

        //then
        Posts posts =postsList.get(0);
        assertThat(posts.getTitle()).isEqualTo(title);
        assertThat(posts.getContent()).isEqualTo(content);
    }

}
```
#### 코드설명
  - @After
    * Junit에서 단위 테스트가 끝날 때마다 수행되는 메소드
    * 보통은 배포 전 전체 테스트를 수행할 때 테스트간 데이터 침범을 막기 위해 사용
    * 여러 테스트가 동시에 수행되면 테스트용 데이터베이스인 H2에 데이터가 그대로 남아 있어 다음 테스트 실행시 테스트가 실패할 수 있음
  - postRepository.save
    * 테이블 posts에 insert/update 쿼리를 실행
    * id 값이 있다면 update, 없다면 insert 쿼리가 실행
  - postRepository.findAll
    * 테이블 post에 있는 모든 데이터를 조회하는 메소드
  - @SpringBootTest
    * 별다른 설정이 없으면 H2 데이터베이스를 자동으로 실행

# 등록/수정/조회 api 만들기

API를 만들기 위해 총 3개의 클래스가 필요
  - Request 데이터를 받을 Dto
  - API 요청을 받을 Controller
  - 트랜잭션, 도메인 기능 간의 순서를 보장하는 Service


> - Spring 웹 계층
> > ![images_swchoi0329_post_9af0b977-4428-4651-b598-87bc65ba096f_jpa6](https://user-images.githubusercontent.com/61530368/165307246-2231b244-7b84-47ca-92d2-613942ff532c.png)

  - Web Layer
    * 흔히 사용하는 컨트롤러(@Controller)와 JSP/freemarker 등 뷰 템플릿 영역
    * 이외에도 필터(@Filter), 인터셉터, 컨트롤러 어드바이스(@ConrollerAdvice)등 외부 요청과 응답에 대한 전반적인 영역
  - Service Layer
    * @Service에 사용되는 서비스 영역
    * 일반적으로 Controller와 Dao의 중간 영역에서 사용
    * @Tranactional이 사용되어야 하는 영역이기도 함
  - Repository Layer
    * Database와 같이 데이터 저장소에 접근하는 영역
    * Dao(Data Access Object) 영역
  - Dtos
    * Dto(data Transfer Object)는 계층 간에 데이터 교환을 위한 객체
  - Domain Model
    * 도메인이라 불리는 개발 대상을 모든 사람이 동일한 관점에서 이해할 수 있고 공유할 수 있도록 단순화시킨 것
    * 이를테면 택시 앱이라고 하면 배차, 탑승, 요금 등이 모두 도메인이 될 수 있음
    * @Entity가 사용된 영역이 도메인 모델
  
 #### 등록
 
 PostsApiController
 ``` java
 package com.example.awsProject.dto;

import com.example.awsProject.post.PostService;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@RequiredArgsConstructor
@RestController
public class PostsApiController {

    private final PostService postService;

    @PostMapping("/api/v1/posts")
    public Long save(@RequestBody PostsSaveRequestDto requestDto) {
        return postService.save(requestDto);
    }
}

 ```

PostService
``` java
package com.example.awsProject.post;

import com.example.awsProject.dto.PostsSaveRequestDto;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@RequiredArgsConstructor
public class PostService {

    private final PostRepository postRepository;

    @Transactional
    public Long save(PostsSaveRequestDto requestDto) {
        return postRepository.save(requestDto.toEntity()).getId();
    }
}

```
PostsSaveRequestDto
``` java
package com.example.awsProject.dto;


import com.example.awsProject.post.Posts;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor
public class PostsSaveRequestDto {
    private String title;
    private String content;
    private String author;
    @Builder
    public PostsSaveRequestDto(String title, String content,
                               String author) {
        this.title = title;
        this.content = content;
        this.author = author;
    }

    public Posts toEntity() {
        return Posts.builder()
                .title(title)
                .content(content)
                .author(author)
                .build();
    }
}
```
  - Entity 클래스는 데이터베이스와 맞닿은 핵심 클래스
  - 때문에 Request/Response 클래스로 사용하면 안됨
  - Dto 클래스를 이용하여 사용 (View Layer와 DB Layer 분리)
  
 PostApiControllerTest
 ``` java
 package com.example.awsProject.dto;

import com.example.awsProject.post.PostRepository;
import com.example.awsProject.post.Posts;
import org.junit.After;
import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.core.AutoConfigureCache;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.*;

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class PostsApiControllerTest {

    @LocalServerPort
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private PostRepository postRepository;

    @After
    public  void tearDown() throws Exception {
        postRepository.deleteAll();
    }

    @Test
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

}
```
  - @WebMvcTest의 경우 JPA 기능이 작동하지 않기 때문에 사용하지 않음
  - @SpringBootTest와 TestRestTemplate 사용
  - SpringBootTest.WebEnvironment.RANDOM_PORT 랜덤 포트 사용
 
#### 수정/조회

PostApiController 추가
``` java
    @PutMapping("/api/v1/posts/{id}")
    public Long update(@PathVariable Long id, @RequestBody PostsUpdateRequestDto requestDto) {
        return postService.update(id, requestDto);
    }
    
    @GetMapping("/api/v1/posts/{id}")
    public PostsResponseDto findById (@PathVariable Long id) {
        return postService.findById(id);
    }
 ```
 
PostResponseDto
``` java
package com.example.awsProject.dto;

import com.example.awsProject.post.Posts;
import lombok.Getter;

@Getter
public class PostsResponseDto {
    private Long id;
    private String title;
    private String content;
    private String author;

    public PostsResponseDto(Posts entity) {
        this.id = entity.getId();
        this.title = entity.getTitle();
        this.content = entity.getContent();
        this.author = entity.getAuthor();
    }
}
```
PostUpdateResponseDto
``` java
package com.example.awsProject.dto;

import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor
public class PostsUpdateRequestDto {
    private String title;
    private String content;

    @Builder
    public PostsUpdateRequestDto(String title, String content) {
        this.title = title;
        this.content = content;
    }
}

```

PostService 메소드 추가
``` java
    public Long update(Long id, PostsUpdateRequestDto requestDto) {
        Posts posts = postRepository.findById(id)
                .orElseThrow(() -> new IllegalArgumentException("해당 게시글이 없음"));

        posts.update(requestDto.getTitle(), requestDto.getContent());
        return id;
    }

    public PostsResponseDto findById(Long id) {
        Posts entity = postRepository.findById(id)
                .orElseThrow(() -> new IllegalArgumentException("해당 게시글이 없습니다. id="+ id));

        return new PostsResponseDto(entity);
    }
```

  - update 기능에서 데이터베이스에 쿼리를 날리는 부분이 없음
  - 이게 가능한 이유는 JPA의 영속성 컨텍스트 때문
  - 영속성 컨텍스트란, 엔티티를 영구 저장하는 환경
  - JPA의 핵심 내용은 엔티티가 영속성 컨텍스트에 포함되어 있냐 아니냐로 갈림
  - JPA의 엔티티 매니저가 활성화된 상태로 트랜잭션 안에서 데이터베이스에 데이트럴 가져오면 이 데이터는 영속성 컨텍스트가 유지된 사애
  - 이 상태에서 해당 데이터의 값을 변경하면 트랜잭션이 끝나는 시점에 해당 테이블에 변경부분을 반영
  - 이러한 개념을 더티 체킹(Dirty Checking)이라함

#### PostsApiConrollerTest
``` java
package com.example.awsProject.dto;

import com.example.awsProject.post.PostRepository;
import com.example.awsProject.post.Posts;
import org.junit.After;
import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.core.AutoConfigureCache;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpMethod;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.*;

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class PostsApiControllerTest {

    @LocalServerPort
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private PostRepository postRepository;

    @After
    public  void tearDown() throws Exception {
        postRepository.deleteAll();
    }

    @Test
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

        List<Posts> all =postRepository.findAll();
        assertThat(all.get(0).getTitle()).isEqualTo(expectedTitle);
        assertThat(all.get(0).getContent()).isEqualTo(expectedContent);
    }

}
```
# JPA Auditing으로 생성시간/수정시간 자동화

#### BaseTimeEnitty
``` java
package com.example.awsProject.domain;

import lombok.Getter;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.EntityListeners;
import javax.persistence.MappedSuperclass;
import java.time.LocalDateTime;

@Getter
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public class BaseTimeEntity {

    @CreatedDate
    private LocalDateTime createDate;

    @LastModifiedDate
    private LocalDateTime modifiedDate;
}
```
  - @MappedSuperclass
    * JPA Entity 클래스들이 BaseTimeEntity를 상속할 경우 
      (createDate, modifiedDate)도 컬럼으로 인식하도록 함
  - @EntityListener(AuditingEntityListener.class)
    * BaseTimeEntity 클래스에 Auditing 기능을 포함
  - @CreatedDate
    * Entity가 생성되어 저장될 때 시간이 자동 저장
  - @LastModifiedDate
    * 조회한 Entity의 값이 변경할 때 시간이 자동 저장
    
``` java
public class Posts extends BaseTimeEntity
```
  - 위와 같이 BaseTimeEntity를 Posts가 상속 받음 

``` java
@EnableJpaAuditing
@SpringBootApplication
public class AwsProjectApplication {

	public static void main(String[] args) {
		SpringApplication.run(AwsProjectApplication.class, args);
	}

}

```
  - JPA Auditing 어노테이션들을 모두 활성화 할수 있도록 Applicaiton 클래스에 어노테이션 추가
 


