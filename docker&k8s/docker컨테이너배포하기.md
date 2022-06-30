## docker 컨테이너 배포하기

### 예제로 시작하기
  - build
    - docker build -t node-dep-example .
  - run
    - docker run -d --rm --name node-dep -p 80:80 node-dep-example
### 프로덕션에서 바인드 마운트
  - 개발
    - 코드를 캡슐화할 필요가 없음(이렇게하는 이유는 이미지를 리빌드 안해도되기 때문)
  - 프로덕트
    - 컨테이너안에 모든 것을 포함시키고 호스팅머신 주변에는 아무것도 없게 해야함
    - 바인드 마운드 대신 카피를 사용  

### AWS & EC2 소개
  - EC2 실행
    - aws 로그인
    - aws 관리 콘솔
    - ![image](https://user-images.githubusercontent.com/61530368/176562985-4af591d8-f1ba-47a9-b6f6-cdeea01e166f.png)
      - ec2 클릭
    - ![image](https://user-images.githubusercontent.com/61530368/176563033-4cd7d935-8b7c-4c83-91ae-789811bfda43.png)
      - 인스턴스 시작 클릭
    - 애플리케이션 및 OS 이미지
      - ![image](https://user-images.githubusercontent.com/61530368/176563144-9cafb902-c85d-4fc0-943f-2999e024c6eb.png)
    - 인스턴스 유형 
      - ![image](https://user-images.githubusercontent.com/61530368/176563175-6697f0ee-e448-462f-8c89-7136752ab475.png)
    - 키 페어(로그인)
      - ![image](https://user-images.githubusercontent.com/61530368/176563323-7fc5af7e-2cc3-43b2-acc5-612bcf25d05e.png)
    - 네트워크 설정
      - ![image](https://user-images.githubusercontent.com/61530368/176563430-5e7f2746-6858-4516-816a-1c7d1c615f64.png)
  - ssh로 연결 (putty사용)
    - https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/putty.html
  - docker 다운로드
    - sudo yum update -y
    - sudo amazon-linux-extras install docker
  - docker 실행
    - sudo service docker start 
    
### Linux에 Docker 일반적인 설치하기
  - https://docs.docker.com/engine/install/ 
  
### 로컬 이미지를 클라우드로 푸시하기
  - dockerhub
    - dockerhub에서 create repository  
    - ![image](https://user-images.githubusercontent.com/61530368/176566592-7987da29-bf4b-4817-929e-b9c32b86e1ff.png)
  - local 
    - .dockerignore 생성 및 아래 추가 
      ``` yaml
      node_modules
      Dockerfile
      *.pem
      *.ppk
      ```
    - 이미지 build
      - docker build -t node-dep-example-1 .
      - docker images를 해보면 node-dep-example-1 이름이 있을 것이다
      - 그런데 도커허브에 push하기위해서는 기존의 이름 앞에 minyoung62/를 붙여야함으로 
      - image tag name을 docker tag를 통해 rename 해줄 것이다 
      - docker tag node-dep-example-1 minyoung62/node-example-1
    - 이미지 push 
      - docker push minyoung62/node-example-1
 ### EC2에서 앱 실행 & 게시하기
  - EC2
    - sudo docker run -d --rm -p 80:80 minyoung62/node-example-1
    - 80포트가 열려있지 않다면 해당 인스턴스 클릭 > 보안 > 인바운드 규칙 추가
  - 3.82.173.77로 접속
    - ![image](https://user-images.githubusercontent.com/61530368/176568993-be598a1b-bdeb-4440-8abc-faf175eb79f1.png)
### 컨테이너/이미지 관리 & 업데이트 
