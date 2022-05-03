# CPU bound 애플리케이션 dockerized
![image](https://user-images.githubusercontent.com/61530368/166504142-85671e4b-14bc-4d88-aafb-4bb9745d8fdb.png)
  - local
    * 로컬 환경에서 dockerfile 작성
    * dockerfile을 build하여 docker image 생성
    * docker image를 dockerhub에 push
  - GCP instance
    * docker image를 dockerhub에서 pull 
    * docker image를 run을 통해 실행하여 contrainer 생성(애플리케이션 실행)
 
 - Spring Boot Docker(https://spring.io/guides/gs/spring-boot-docker/ 참고)
    * Dockerfile 생성
    * 아래의 코드 Dockerfile에 복사 붙여넣기
   ``` 
   FROM openjdk:8-jdk-alpine
    ARG JAR_FILE=target/*.jar
    COPY ${JAR_FILE} app.jar
    ENTRYPOINT ["java","-jar","/app.jar"]
    ```
    * docker hub 가입 및 repository 생성
      * repository 생성
      ![image](https://user-images.githubusercontent.com/61530368/166506934-3813f3a5-dd2b-4243-aa06-f5a6171d31e7.png)
    
    * local에서 build 및 run
      * docker build -t minyoung62/spring-boot-cpu-bound .   
      * docker run -p 80:80 minyoung62/spring-boot-cpu-bound 
    * docker hub로 image push
      * docker push minyoung62/spring-boot-cpu-bound
      * error 가 뜸 (이유는 login을 안해서임)
      * docker login 명령어를 통해 로그인 후 다시 명령어 실행
      * docker push minyoung62/spring-boot-cpu-bound
    * docker hub 확인
      * minyoung62/spring-boot-cpu-bound가 push된 것을 확인 할 수 있음
    * GCP instance에서 docker pull 및 run
      * sudo docker pull minyoung62/spring-boot-cpu-bound
      * sudo docker run -p 80:80 minyoung62/spring-boot-cpu-bound
     
