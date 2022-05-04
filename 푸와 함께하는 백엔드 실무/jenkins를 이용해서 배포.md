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
      ![image](https://user-images.githubusercontent.com/61530368/166624623-57bae136-1ec7-4769-bfc5-d6aa8036c711.png)
    * jenkins에 플러그인 추가 
      * jenkins 웹에서 jenkins 관리 > 플러그인 관리 > 설치 가능 탭 클릭 > seach에 SSH 플러그인 체크 후 재시작 없이 설치 > 메인 페이지로 돌아가기 
  - 배포 
    * ssh를 통해 배포 
    * 젠킨스가 워커로 접속하여 도커 이미지를 풀 받고 런 
    * 중요한 문제 하나가 있음
    * 젠킨스만 ssh를 통해 접속할 수 있게 해야함
    * 오직 젠킨스 인스턴스만 워커 인스턴스로 접속하게 설정
  - 오직 젠킨스 인스턴스만 워커 인스턴스로 접속하게 설정
    * 워커로 하여금 젠킨스 인스턴스라고 증명할 수 있는 ssh만 허용 설정
      ![image](https://user-images.githubusercontent.com/61530368/166625196-a5691185-acc4-4fc1-81c9-6a67ad3658b1.png)
    * 위와 같이 젠킨스에서 젠킨스 개인키와 공개키를 만들어 공개키를 워커에게 등록해줌 ( 서명 방식이랑 유사)
    * 이렇게 하면 오직 젠킨스만 워커에 ssh로 접속 가능 
    * 젠킨스 인스턴스에서 ssh로 워커 인스턴스에 접속하여 docker run을 실행시켜주면됨 
    * jenkins instance에서 공개키 암호키 쌍 만들기
      * ssh-keygen -t rsa -f ~/.ssh/id_rsa 명령어 입력 > 엔터 > 엔터 
      * 공개키 암호키 쌍 생성
      * /home/kimminyoung6709/.ssh/id_rsa 이 경로에서 암호키 확인 가능 
      * /home/kimminyoung6709/.ssh/id_rsa.pub 이 경로에서 공개키 확인 가능
      * 워커 인스턴스에 접속
      * 젠킨스 인스턴스의 공개키를 워커 인스턴스의 ~/.ssh/authorized_keys에 제일 하단에 복붙 ( 이 방법 말고 아래의 그림방법 이용해야 워커 인스턴스에서 젠킨스 인스턴스의 공개키가 유지됨 )
      ![image](https://user-images.githubusercontent.com/61530368/166634213-20437891-fca0-4b9d-8b15-1ddbfe647c1f.png)

      * 워커 인스턴스에서 폴더 및 파일 권한 변경
      * chmod 700 ~/.ssh
      * chmod 600 ~/.ssh/authorized_keys
      * Local에서 https://archives.jenkins-ci.org/plugins/publish-over-ssh/latest/ 로 접속 > publish-over-ssh.hpi 파일 다운
      * 젠킨스 웹에 접속 >Jenkins 관리 > 플러그인 관리 > 고급 > 플러그인 올리기에 다운받은 .hpi 파일 업로드 > deploy
      * Dashboard > Jenkins 관리 > 시스템 설정 > 제일 하단으로 스크롤 Publish over SSH 작성 
      * Publish over SSH 작성 > Key에 젠킨스 인스턴스의 암호키를 복붙 > SSH severs 추가 버튼
      * worker intstnace 정보 작성 > name 작성 > hostname 작성(워커 인스턴스 내부아이피) > username 작성 > Test Configuration 클릭 > Success (auth error가 나면 워크 인스턴스의 공개키가 제대로 등록되어있는지 확인) > 저장
      ![image](https://user-images.githubusercontent.com/61530368/166629183-6bd65801-199c-4c61-ac28-4d0bab2b9ab3.png)
    * 워크 인스턴스의 배보 스크립트 작성 
      * 배포 스크립트는 Item이라는 단위로 관리 
      * 새로운 Item 클릭 > item name 입력 > Freestype project 클릭 > Ok 클릭
      ![image](https://user-images.githubusercontent.com/61530368/166629451-4b24dc9f-3309-4929-b7ff-ac7be6b5fef6.png)
      * 빌드 후 조치 > Send build artifacts over SSH 클릭
      ![image](https://user-images.githubusercontent.com/61530368/166629521-f4443641-621c-4c3a-bc21-07b0603c35f2.png)
      * SSH Server > 워커 인스턴스 선택 > 고급 > Verbose output in console 체크(로그 자세히 출력) > Transfers > Transfer Set > Excec command에 docker run -p 8080:80 minyoung62/spring-boot-cpu-bound 명령어 입력 (기존에 외부:내부 80:80을 8080:80으로 바꿔주고 외부의 8080으로 실행하기 때문에 sudo를 제거)
      * 추가로 워크 인스턴스에 sudo chmod 666 /var/run/docker.sock 명령어를 통해 해당 폴더에 권한 부여 
      * cpu-instance-1 deploy > 구성 > Exec command에 nohup docker run -p 8080:80 minyoung62/spring-boot-cpu-bounds > /dev/null 2>&1 & 작성(nohupr과  &는 background에서 실행 시킨다는 의미, /dev/null 2>&1는 표준 에러를 표준 출력으로 리다이렉션한다는 의미)
      * build now를 하면 정상적으로 젠킨스 인스턴스가 워커 인스턴스의 도커 런 명령어 실행
      * 워커인스턴스 외부 아이피:8080/hash/123 접속시 정상 작동 확인 가능 
      
