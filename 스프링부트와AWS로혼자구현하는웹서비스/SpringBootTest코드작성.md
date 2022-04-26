# TDD란?
  - 테스트 주도 개발 : 테스트가 개발을 이끌어 나간다
  - 메소드나 함수 같은 프로그램 모듈을 작성할 때 
  - 작성 종료조건을 먼저 정해놓고 코딩을 시작 한다는 의미로 받아들이면 편함
    * RED: 항상 실패하는 테스트를 먼저 작성
    * GREEN: 테스트에 통과하는 프로덕션 코드 작성
    * REFACTOR: 테스트가 통과하면 프로덕션 코드를 리팩토링
  - 위의 레드 그린 사이클 처럼 우선 테스트를 작성하고 그걸 통과하는 코드를 만들고 해당 과정을 반복하면서 제대로 동작하는지에 대한 피드백을 적극적으로 받아 들이는 것
  
 # Tdd 사용 이유
  - 개발 단계초기에 문제를 발견 가능
  - 추후에 코드를 리팩토링하거나 라이브러리 업그레이드 등에서 기존기능이 올바르게 작동하는지 확인 가능 
  - 기능에 대한 불확실성 감소
  - 시스템에 대한 실제 문서를 제공 즉, 단위 테스트 자체가 문서로 사용
  
 # Controller 테스트 코드 작성하기
  
  ```java

package com.example.awsProject;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class AwsProjectApplication {

	public static void main(String[] args) {
		SpringApplication.run(AwsProjectApplication.class, args);
	}

}

  ```
###  Application 클래스는 앞으로 만들 프로젝트의 메인 클래스
   - @SpringBootApplication으로 인해 스프링 부트의 자동 설정, 스프링 Bean 읽기와 생성을 모두 자동 설정
   - @SpringBootApplication이 있는 위치부터 설정을 읽기 때문에 이 클래스는 항상 프로젝트의 최상단에 위치해야 함
   - main 메소드에서 실행하는 SpringApplication.run으로 인해 내장 WAS(Web Application Server, 웹 어플리케이션 서버)를 실행
   - 내장 WAS란 별도로 외부에 WAS를 두지 않고 애플리케이션을 실행할 때 내부에서 WAS를 실행하는 것. 이렇게 되면 항상 서버에 톰캣(Tomcat)을 설치할 필요가 없게되고, 스프링 부트를 만들어진 Jar 파일로 실행하면 됨

# HelloController Class 작성
  ``` java
  package com.example.awsProject;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
    
    @GetMapping("/hello")
    public String hello() {
        return "hello";
    }
    
}
  
  ```
  
###   코드 설명
   - @RestController
    * 컨트롤러를 JSON을 반환하는 컨트롤로 만듦
   - @GetMapping
    * HTTP Method인 Get인 요청을 받을 수 있는 API를 만듦
   
 # HelloControllerTest Class 작성
 ``` java
 
 package com.example.awsProject;

import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@WebMvcTest(controllers = HelloController.class)
class HelloControllerTest {

    @Autowired
    private MockMvc mvc;

    @Test
    public void hello가_리턴된다() throws Exception {
        String hello = "hello";

        mvc.perform(get("/hello"))
                .andExpect(status().isOk())
                .andExpect(content().string(hello));
        
    }

}
 
 ```
###  코드설명
  - @RunWith(SpringRunner.class)
     * 테스트를 진행할 때 JUint에 내장된 실행자 외에 다른실행자를 실행
     * 여기서는 SpringRunner라는 스프링 실행자를 사용
     * 즉, 스프링 부트 테스트와 JUnit 사이에 연결자 역할
  - @WebMvcTest
     * 여러 스프링 테스트 어노테이션 중, Web(Spring MVC)에 집중할 수 있는 어노테이션
     * 선언할 경우 @Controller, @ControllerAdvice 등을 사용 가능
     * 단, @Service, @Component, @Repository 등은 사용 불가
     * 여기서는 컨트롤러만 사용
  - @Autowired
     * 스프링이 관리하는 빈(Bean)을 주입 받음
  - private MockMvc mvc
     * 웹 API를 테스트할 때 사용
     * 스프링 MVC 테스트의 시작점
     * 이 클래스를 통해 HTTP GET, POST 등에 대한 API 테스트 가능
  - mvc.perform(get("/hello"))
     * MockMvc를 통해 /hello 주소로 HTTP GET 요청
     * 체이닝이 지원되어 아래와 같이 여러 검증 기능을 이어서 선언 가능
  - .andExpect(status().isOk())
     * mvc.perform의 결과를 검증
     * HTTP Header의 Status를 검증
     * 우리가 흔히 알고 있는 200, 404, 500 등의 상태를 검증
  - .andExpect(content().string(hello))
     * mvc.perform의 결과를 검증
     * 응답 본문의 내용 검증
     
# DTO 룸복 테스트 코드 작성 
###  HelloREsponseDto 클래스 생성
``` java
package com.example.awsProject;

import lombok.Getter;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor
public class HelloResponseDto {

    private final String name;
    private final int amount;
}

```
#### 코드설명
  - @Getter 
    * 선언된 모든 필드의 get 메소드를 생성
  - @RequiredArgsConstructor
    * 선언된 모든 final 필드가 포함된 생성자를 생성
    * final이 없는 필드는 포함하지 않음

### HelloResponseDtoTest 클래스 생성
``` java
package com.example.awsProject;

import org.junit.jupiter.api.Test;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.*;

class HelloResponseDtoTest {
    
    @Test
    public void 룸복_기능_테스트() throws Exception {
        //given
        String name = "test";
        int amount = 1000;
        
        //when
        HelloResponseDto dto = new HelloResponseDto(name, amount);
        
        //then
        assertThat(dto.getName()).isEqualTo(name);
        assertThat(dto.getAmount()).isEqualTo(amount);
    }
}
```
#### 코드설명
  - assertThat
    * assertj라는 테스트 검증 라이브러리의 검증 메소드
  - isEqualTo
    * 검증하고 싶은 대상을 메소드 인자로 받음
    * 메소드 체이닝이 지원되어 isEqualTo와 같이 메소드를 이어서 사용할 수 있음

### HelloController 메소드 추가
``` java
 @GetMapping("/hello/dto")
    public HelloResponseDto helloDto(@RequestParam("name") String name,
                                     @RequestParam("amount") int amount) {
        return new HelloResponseDto(name, amount);
    }
```
#### 코드설명
    - @RequestParam
      * 외부에서 API로 넘긴 파라미터를 가져오는 어노테이션

### HelloControllerTest 메소드 추가
``` java

    @Test
    public void helloDto가_리턴된다() throws  Exception {
        String name = "hello";
        int amount = 1000;

        mvc.perform(
                        get("/hello/dto")
                                .param("name", name)
                                .param("amount", String.valueOf(amount)))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.name", is(name)))
                .andExpect(jsonPath("$.amount", is(amount)));
    }
 ```
 #### 코드설명
  - param
    * API 테스트할 때 사용될 요청 파라미터를 설정
    * 단, 값은 String만 허용
    * 숫자/날짜 등의 데이터도 작성할 때는 문자열로 변경해야 가능
  - jsonPath
    * JSON 응답값을 필드별로 검증할 수 있는 메소드
    * $를 기준으로 필드명을 

    
    
    
    
    
    
    
    
    
