# CPU를 극단적으로 사용하는 애플리케이션 만들기
  - 프로세스와 프로그램의 차이
    * 애플리케이션은 하드디스크에 저장
    * 프로그램이 실행되면 프로세스라고 부름 (메모리에 적재)
    * CPU에 의해 프로세스가 실행
    * 순서는 스케쥴링을 통해 부여 
  - I/O Burst, CPU Burst, I/O Bound, CPU Bound
    * I/O를 하는 시간을 I/O 버스트라 함
    * CPU에서 실행되는 시간을 CPU Burst라 함 
    * I/O Burst가 많은 애플리케이션을 I/O Bound 애플리케이션이라 함
    * CPU Burst가 많은 애플리케이션을 CPU Bound 애플리케이션이라 함
  - hash연산을 사용하여 CPU를 극단적으로 사용하는 애플리케이션 만듦

# 새로운 GCP 인스턴스 생성, 환경 세팅, jar 실행, 웹브라우저로 확인 
  - cpu-instance-1 생성
    * 지역 서울
    * cpu micro min version
    * os 변경
    * 방화벽 체크 
  - cpu-instance-1 세팅
    * ssh 실행 
    * yum으로 wget 및 java 설치
    * sudo yum install wget
    * sudo yum install java
  - jar 실행
    * wget https://github.com/lleellee0/class101-files/raw/main/cpu-0.0.1-SNAPSHOT.jar 명령어를 통해 jar 다운
    * ls를 통해 cpu-0.0.1-SNAPSHOT.jar 확인
    * sudo java -jar cpu-0.0.1-SNAPSHOP.jar 명령어 입력
    * spring 서버 실행 완료 
    ![image](https://user-images.githubusercontent.com/61530368/166494046-948d237f-be5c-41d0-91ec-e337a994b387.png)

  - 웹브라우저로 확인
    * 인스턴스외부아이피/hash/123
    * hash는 204ms 
    ![image](https://user-images.githubusercontent.com/61530368/166495567-08a84fa0-6d0b-4201-a254-695b444579f1.png)

    * 인스턴스외부아이피/hash/123
    * hello는 114ms
    ![image](https://user-images.githubusercontent.com/61530368/166495408-6cf6d925-a025-43fb-af9a-a7132055de06.png)
    
