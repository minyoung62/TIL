# Swagger

## 협업을 위해 필요한 라이브러리
  - 기존에 사용 방식
    - ![image](https://user-images.githubusercontent.com/61530368/176367111-31bee5a0-7f19-47b1-a60b-36efa0bf546a.png)
    - ![image](https://user-images.githubusercontent.com/61530368/176367136-bdc16605-14bc-45c7-9bf9-321534ba1c20.png)
    - api가 추가되면 엑셀에 기입후 다시 배포해야함
  - Swagger란?
    - 서버로 요청되는 api리스트를 html 화면으로 문서화하여 테스트 할 수 있는 라이브러리
    - 이 라이브러리는 서버가 가동되면서 @RestController를 읽어 API를 분석하여 HTML 문서를 작성함
  - Swagger가 필요한 이유
    - Rest api의 스펙을 문서화 하는 것은 매우 중요
    - api를 변경할 때마다 Reference 문서를 계속 바꿔야하는 불편함이 있음 
  - Swagger 설정 방법
    - @Configuration: 어노테이션 기반의 환경 구성을 돕는 어노테이션 IoC Container에게 해당 클래스를 Bean 구성 Class 임을 알려줌
    - @Bean: 개발자가 직접 제어가 불가능한 외부 라이브러리 등을 Bean으로 만들 경우에 사용 
