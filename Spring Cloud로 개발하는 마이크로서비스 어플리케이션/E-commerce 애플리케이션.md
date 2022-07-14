## 전체 애플리케이션 구성요소
  - Git Repository: 마이크로서비스 소스 관리 및 프로파일 관리
  - Config Server: Git 저장소에 등록된 프로파일 정보 및 설정 정보
  - Eureka Server: 마이크로서비스 등록 및 검색
  - API Gateway Server: 마이크로서비스 부하 분산 및 서비스 라우팅
  - Microservices: 회원 MS, 주문 MS, 상품(카테고리) MS
  - Queuing System: 마이크로서비스 간 메시지 발행 및 구독 

## 애플리케이션 APIs
  - Catalog Service
    - [GET] /catalog-service/catalogs: 상품 목록 제공 
  - User Service 
    - [POST] /user-service/users: 사용자 정보 등록
    - [GET] /user-service/users: 전체 사용자 조회
    - [GET] /user-service/users/{user_id}: 사용자 정보, 주문 내역 조회
  - Order Service
    - [POST] /order-service/users/{user_id}/orders: 주문 등록
    - [GET] /order-service/users/{user_id{/orders: 주문 확인 

## Users Microservice
  - Features
    - 신규 회원 등록
    - 회원 로그인
    - 상세 정보 확인
    - 회원 정보 수정/삭제
    - 상품 주문
    - 주문 내역 확인
  - APIs
    - ![image](https://user-images.githubusercontent.com/61530368/179003681-2baad6f8-ff43-4ce0-b1c1-e90ee6ffdc50.png)
  
