
# 개요
 - 트래픽 대용량 처리
 - 배포 및 자동화
 - 무중단 배포
 

# Google Cloud Platform(GCP) 계정 생성, 인스턴스 생성
  - 
  
# docker 
  - 도커와 vm의 차이점
    * vm은 각각의 인스턴스의 독립적인 운영체제처럼 동작
    * docker는 하나의 프로세스로 동작 
    * 때문에 docker가 리소스를 덜 잡아먹음 
  - IaC (Infrastructure as Code)
    * 도커파일에 실행 명령어를 넣고 실행 하면 동일한 환경 구성
    * 인프라 설정 삽질 해소
  - Docker Desktop 
    * Docker + GUI
  - Docker 설치
    * GCP 인스턴스에 들어가서 아래의 명령어 실행 
    * sudo yum install docker
  - Docker 실행
    * sudo systemctl start docker
    * docker run -d -p 80:80 docker/getting-started 명령어 실행
    * 그러나 권한이 없다고 뜸
    * 1024번 포트 이하에 있는 포트를 well-known port라고 하는데 이 포트를 열때는 관리자 권한 필요
    * sudo를 붙여서 해결
    * sudo docker run -d -p 80:80 docker/getting-started
  - 확인
    * GCP 외부아이피를 통해 웹페이지 접속 
 ![image](https://user-images.githubusercontent.com/61530368/166486140-1ffa40b7-eb79-43c0-b886-a0d68bf1ec47.png){: width="100px" height="100px"}

