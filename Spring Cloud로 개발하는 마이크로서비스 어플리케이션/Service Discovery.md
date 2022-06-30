## Spring Cloud Netflix Eureka
  - ![image](https://user-images.githubusercontent.com/61530368/176656397-ad2f6bb8-2603-42c5-a897-f9b6f2c3752a.png)
    - 서비스가 어디있는지를 등록하고 검색을 하는게 discovery service라하고 netfilx eureka를 이용해 이것을 사용
    - 요청이 오면 필요한 서비스의 위치를 알려줌 
  - EcommerceDiscoveryService
    - dependency에 spring-cloud-starter-netflix-eureka-server
    - ![image](https://user-images.githubusercontent.com/61530368/176659134-727162eb-9301-4c63-94fc-c124e672e1aa.png)
      - @EnableEurekaServer어노테이션으로 실행 애플리케이션 웹을 Eureka 서버로 등록
    - application.yml
      - ![image](https://user-images.githubusercontent.com/61530368/176659357-4486054a-fee8-43a7-ad6e-53e646eba0b9.png)
      - 2번줄 port 설정
      - 6번줄 마이크로 서비스는 각각 고유한 아이디 필요 discoveryservice로 이름 설정
      - 10, 11번줄 register-with-uereka: false, fetch-registry: false로 하지않으면 eureka서버에 자기자신을 등록함(이것은 자기자신을 굳이 등록할 필요없기 때문에 설정해주는 것)
  - 실행화면
    - ![image](https://user-images.githubusercontent.com/61530368/176661288-3b7ed667-cea2-49a4-83af-fa58e8761542.png)
 
