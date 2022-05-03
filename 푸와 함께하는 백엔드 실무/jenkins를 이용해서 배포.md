# Jenkins
![image](https://user-images.githubusercontent.com/61530368/166514639-4f4e6ce5-4f31-4447-9900-37a87f4b1615.png)

  - jenkins는 CI/CD를 자동으로 해줌 
  - jenkins 인스턴스 생성
  - jenkins 인스턴스에서 jenkins 설정
    * 기본 설치
      * sudo yum install wget
      * sudo yum install maven
      * sudo yum install git
      * sudo yum install docker
    * Jenkins 설치
      * jenkins 설치는 아래 두개의 명령어를 실행하여 패키지를 설치한 후에 install 해야 에러가 안남  
      * sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
      * sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
      * sudo yum install jenkins
      * sudo systemctl start jenkins (background 실행)
      * sudo systemctl status jenkins (상태 확인, loaded, activating이 뜨면 정상적으로 실행됨)
    * Jenkins 인스턴스 8080포트 오픈
      * Jenkins는 8080포트로 서버를 실행했기 때문에 8080포트를 열어준다 
      * GCP에서 방화벽 규칙 설정에서 방화벽 규칙 만들기를 통해 8080 포트 오픈
      * tcp 8080 오픈
      ![image](https://user-images.githubusercontent.com/61530368/166519088-0ff16fa6-0cb6-4525-a051-6633466c0865.png)
    * 웹 브라우저를 통해 확인
      *  sudo cat /var/lib/jenkins/secrets/initialAdminPassword 을 통해 초기 비번 확인 및 입력
      ![image](https://user-images.githubusercontent.com/61530368/166524090-08fa775b-2023-491f-8b54-6020eebc909e.png)
      * 추천 플러그인 선택
      ![image](https://user-images.githubusercontent.com/61530368/166524261-b5f348d5-0b66-45f9-b341-5e0418d0b953.png)
      * 회원가입 후 jenkins 실행
