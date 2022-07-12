## API Gateway란?
  - 요청에 대한 인터페이스 역할
### api gateway에서의 작업
  -  인증 및 권한 통합
  -  서비스 검색 통합
  -  응답 캐싱
  -  정책, 회로 차단기
  -  속도 제한
  -  부하 분산
  -  로깅, 추적, 상관 관계
  -  헤더, 쿼리 문자열 및 청구 변환
  -  IP 허용 목록에 추가 
### Netflix Ribbon
  - Spring Cloud에서의 MSA간 통신
    - RestTemplate
      - ![image](https://user-images.githubusercontent.com/61530368/178483740-5f81996d-9e0f-415c-a458-c5f556f42aa7.png) 
    - Feign Client  
      - ![image](https://user-images.githubusercontent.com/61530368/178483804-ae07fc4a-2719-4c7a-a60e-d0ea1ca8f9ca.png)
  - Ribbon: Client side Load Balancer
    - 서비스 이름으로 호출
    - Health Check(마이크로 서비스가 정상적으로 작동하는지 체크)
    - Spring cloud Ribbon은 Spring Boot 2.4에서는 Maintenance상태  
    - ![image](https://user-images.githubusercontent.com/61530368/178484326-addb6199-cc15-4796-89ea-86462303cde9.png)
## Netflix Zuul 구현
  - 구성
    - First Service
    - Second Service
    - Netflix Zuul -> Gateway
    - ![image](https://user-images.githubusercontent.com/61530368/178484567-ec7f0733-8d18-4c12-89e3-a6249fd43072.png)
    - Spring cloud Zuul은 Spring Boot 2.4에서는 Maintenance상태  
## Spring Cloud Gateway 
## Spring Cloud Gateway - Filter
  - ![image](https://user-images.githubusercontent.com/61530368/178491308-b8413279-5196-400e-ae46-9fcb1bec5886.png)

## Spring Cloud Gateway - Custom Filter
  - ![image](https://user-images.githubusercontent.com/61530368/178502868-ba3ac69e-3763-4a45-b7ec-3d9555814df4.png)
  - 비동기 방식이라 서블릿아니라 ServerHttpReuqest를 사용
## Spring Cloud Gateway - Global Filter
  - ![image](https://user-images.githubusercontent.com/61530368/178505967-5d799bbb-c7e2-42e8-ba04-3c1cd6601e08.png)
  - ![image](https://user-images.githubusercontent.com/61530368/178506477-192963d3-cc53-4859-80fc-446d0a5f123d.png)
  - 글로벌 필터는 모든 필터보다 제일 우선 출력되고 제일 후에 출력
## Spring Cloud Gateway - Custom Filter (Logging)
  - ![image](https://user-images.githubusercontent.com/61530368/178510031-829fb197-90e6-4457-aeec-9fec82b63fe8.png)
## Spring Cloud Gateway - Eureka 연동
  
