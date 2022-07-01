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

### 수동배포에서 관리형 서비스로
  - ECS 
    - 컨테이너 관리를 도와주는 서비스
    - 컨테이너 구동 및 실행, 모니터링 등등...
### ECS
  - ECS 4가지 범주
    - 클러스터, 컨테이너 정의, 태스크, 서비스  
  - 컨테이너 정의
    - ![image](https://user-images.githubusercontent.com/61530368/176799346-677963a1-579b-4dac-96b4-68cda3e341d5.png)
    - ![image](https://user-images.githubusercontent.com/61530368/176800166-4f11f358-fc1b-40df-a7d5-31a8256e712b.png)
      - custom 클릭
      - 이미지
        - 이미지에 도커허브 리파지토리를 넣음 
        - 다른 컨테이너 리지스트리를 사용하는경우에는 https://registry.com/minyoung62/node-example-1 이렇게 해주어ㅑ함 
      - 포트매핑
        - 컨테이너 내부 포트를 적어주면 자동으로 외부 포트를 매핑해줌  
      - 고급 컨테이너 구성
        - ENVIRONMENT
          - 작업 디렉터리
          - 환경파일  
          - 환경변수
        - 컨테이너 시간 초과
          - 시작 시간 초과
          - 중지 시간 초과  
        - 네트워크 구성
          - 네트워크 설정 등등 
        - 스토리지 및 로깅  
          - 마운트 포인트
          - 볼륨 
  - 테스트 정의
    - ![image](https://user-images.githubusercontent.com/61530368/176800428-9a27e1c3-71f4-4ce8-8949-59624d588113.png)
  - 클러스터
    - ![image](https://user-images.githubusercontent.com/61530368/176800498-e209f2cd-3901-41bd-abe9-39cfaeee13b5.png)
  - 서비스 시작
    - ![image](https://user-images.githubusercontent.com/61530368/176801630-f5facb2f-87b2-4bf6-8da5-e1dad5844f23.png)
      - 작업 클릭
      - ![image](https://user-images.githubusercontent.com/61530368/176801685-ec6e2fd7-ad73-49a2-9985-5be9a8890dfa.png)
      - 주소로 접속
      - ![image](https://user-images.githubusercontent.com/61530368/176801719-5b7f0b33-c52c-4d81-83e4-b8a3ef02455f.png)
      - 자동으로 도커 풀 및 실행 된 것을 확인할 수 있음 
### 관리되는 컨테이너를 업데이트하기
  - 로컬
    - 로컬에서 코드를 수정한 후 다시 build
      - docker build -t node-dep-example-1 . node-dep-example-1 .
    - 이미지 태그 이름을 도커허브 리파지토리에 맞게 바꾸기
      - docker tag node-dep-example-1 minyoung62/node-example-1
    - 도커허브로 푸쉬
      - docker push minyoung62/node-example-1 
  - ECS
    - ![image](https://user-images.githubusercontent.com/61530368/176802208-a77d0a6c-de87-46c3-bc88-00cb1ae722b0.png)
      - 작업 정의 클릭
    - ![image](https://user-images.githubusercontent.com/61530368/176802250-c4418c42-c057-4b00-8a47-9b3582ab489d.png)
      - 새개정 클릭 
      
    - ![image](https://user-images.githubusercontent.com/61530368/176802391-bbb14195-7ff4-475a-8081-a2d004d53a02.png)
    - ![image](https://user-images.githubusercontent.com/61530368/176802399-365ad561-7ff0-4412-b48f-aea1867dcaae.png)
      - 새개정 생성 (제일 하단 오른쪽에 생성있음) 
    - ![image](https://user-images.githubusercontent.com/61530368/176802468-6341631d-4354-4870-8ee2-b9695b2876b0.png)
      - 서비스 업데이트 클릭 
    - ![image](https://user-images.githubusercontent.com/61530368/176802530-73332b62-9853-4ad3-8cc6-36c98df03809.png)
      - 컴토로 건너뛰기 클릭 -> 서비스 업데이트
    - ![image](https://user-images.githubusercontent.com/61530368/176802920-d2173cbc-90dc-417c-a7db-e7e513c5f292.png)
      - 서비스업데이트 완료
    - ![image](https://user-images.githubusercontent.com/61530368/176803033-9e6ef634-cfca-42ef-bd71-c7bf730ce276.png)
      - 새로운 작업이 생겼고
    - ![image](https://user-images.githubusercontent.com/61530368/176803071-b98decf7-a689-466a-a073-0c2219c1b0ee.png)
      - 해당 아이피로 접속해서 업데이트 확인 가능  
  - 로드밸런실 관련 참고
    - https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-load-balancing.html 

### 다중 컨테이너 앱 준비하기 
  - AWS ECS 사용시 docker compose 사용을 못하는 이유(동일한 테스크에 컨테이너를 추가하면 동일한 머신에서의 실행이 보장)
    - docker compose는 한 컴퓨터(로컬 머신)에서 사용할 수 있음
    - 그러나 ECS는 여러개의 분산 서버이기 때문에 docker compose를 이용하게 되면 ip매핑이 안됨 
    - 그런데 AWS ECS는 동일한 테스트에 컨테이너를 추가하면 동일한 머신에서의 실행이 보장됨 
  - docker hub
    - ![image](https://user-images.githubusercontent.com/61530368/176805611-20bb5b17-dbf5-4ed8-a15f-6d9592c6fdb6.png)
    - goals-node repository 생성  
  - local 
    - docker build -t goals-node ./backend/.
    - docker tag goals-node minyoung62/goals-node
    - docker push minyoung62/goals-node
    
### NodeJS 백엔드 컨테이너 구성하기
  - AWS ECS 클러스터 생성
    - ![image](https://user-images.githubusercontent.com/61530368/176806204-bd99f84a-3d28-426a-95b5-546d6cc3c4ee.png)
      - 클러스터 생성
    - ![image](https://user-images.githubusercontent.com/61530368/176806281-9e40b29e-19cb-407e-bd9f-ddb91ac65ff3.png)
      - 네트워크 전용 > 다음 단계
    - ![image](https://user-images.githubusercontent.com/61530368/176806386-b1f92dae-0849-4715-aa4b-9dae59b693f5.png)
      - 클러스터이름 작성 > 생성
    - ![image](https://user-images.githubusercontent.com/61530368/176806672-4c751869-0f66-465e-b7e0-65f2f9feef44.png)
      - 클러스터 생성 완료 
  - 새 작정 생성
    - ![image](https://user-images.githubusercontent.com/61530368/176806836-4b2f30cf-cacd-4461-af68-62a91e06fb09.png)
    - ![image](https://user-images.githubusercontent.com/61530368/176806861-5a068eb1-2232-4231-a16a-d3ddcac7c486.png)
      -FARGATE 무한대로 서버 증가 가능 
    - ![image](https://user-images.githubusercontent.com/61530368/176807051-6355ff54-2f6f-4a05-a2cb-70572d3ab30e.png)
      - cpu, 메모리 설정  
    - ![image](https://user-images.githubusercontent.com/61530368/176807101-dc6974a6-0c8a-46b3-b621-0369222929a7.png)
      - 컨테이너 추가 클릭

    - ![image](https://user-images.githubusercontent.com/61530368/176810853-4bae2741-9aa3-4405-a9ad-a61218ab409c.png)
      - 배포환경에서는 nodemon이 필요없기 때문에 명령어에 node, app.js를 입력  

### 두 번째 컨테이너 & 로드 밸런서 배포하기
  - mongodb 컨테이너 추가
    - ![image](https://user-images.githubusercontent.com/61530368/176811303-1090ce5a-1e28-4a92-88ab-0ab02f7dfb51.png)
    - ![image](https://user-images.githubusercontent.com/61530368/176811322-f2810623-b46f-47a6-b036-dc2720e958fa.png)
    - ![image](https://user-images.githubusercontent.com/61530368/176811351-cc401935-d804-4ae4-9e56-e7266c1c6c2b.png)
      - 스토리지 및 로깅은 일단 아무것도 안한 상태로 놔둠 (나중에 진행)
  - ![image](https://user-images.githubusercontent.com/61530368/176811422-23251360-c488-4bb1-be5b-12d7b56f24ee.png)
    - 두개의 컨테이너 만들었고
  - ![image](https://user-images.githubusercontent.com/61530368/176811451-b18cf56e-9a73-4f8e-8102-ee7a4e09ee3b.png)
    - 작업 정의 성공
  - 클러스터에서 서비스 생성
    - ![image](https://user-images.githubusercontent.com/61530368/176811623-f9b1da20-fb3d-4147-bb01-b5c0c86838f3.png)
      - 서비스 > 생성 클릭
    - ![image](https://user-images.githubusercontent.com/61530368/176811742-8e423a31-c3c9-41a5-88d5-624a43b38b47.png)
      - 시작유형 FARAGATE 선택
      - 작정정의 goals선택(앞서 만든 작업 정의)
      - 서비스 이름 goals-service
      - 작업 개수 1개
      - 다음 단계 클릭
    - ![image](https://user-images.githubusercontent.com/61530368/176812057-6744fdaa-f9c3-4ac3-b2eb-a35d09203707.png)
      - 클러스터 vpc(vpc는 클러스터를 생성할 때만 만들어짐)
      - 서브넷은 모두 추가 
      - 자동 할당 퍼블릭 IP enbaled로 설정 
    - ![image](https://user-images.githubusercontent.com/61530368/176812112-0607abc9-440c-4047-86c3-a237448da261.png)
      - Application Load Balancer 클릭
      - EC2 콘솔에서 로드 밸런서를 생성하십시오 클릭
    - ![image](https://user-images.githubusercontent.com/61530368/176812248-8853ef90-b61f-4124-9621-a28574e2f407.png)
      - application load balancer create 클릭 
    - ![image](https://user-images.githubusercontent.com/61530368/176812535-27be21e8-67e7-467a-a9c4-eb12fb4502fb.png)
      - Basic configuration에 로드밸런서 이름 추가
      - Network mapping에 VPC에 앞서 설정한 VPC랑 똑같은걸로해야 로드밸런서가 동일한 네트워크에서 작동하도록 보장함
      - us-east-1a, us-east-1b 둘 다 체크 클릭
    - ![image](https://user-images.githubusercontent.com/61530368/176813417-ed780e08-0bc9-4e66-8906-329fe257b78b.png)
      - 리스너 추가 > 로드발랜서 생성
    - ![image](https://user-images.githubusercontent.com/61530368/176813509-723932f1-c674-4013-b9f8-7d27304d0be9.png)
      - 앞서 만든 로드밸런스 추가
    - ![image](https://user-images.githubusercontent.com/61530368/176813866-0101d5db-faad-4ea7-a063-98efb2cb6e12.png)
      - 로드밸런스할 컨테이너 등록 > 다음 단계
    - ![image](https://user-images.githubusercontent.com/61530368/176813899-cdd76638-2772-4544-9f49-14e56f7fc883.png)
      - 다음단계 > 서비스 생ㅇ성
    - ![image](https://user-images.githubusercontent.com/61530368/176814026-c3239c67-7477-4ff7-ae66-9efa83a0383a.png)














