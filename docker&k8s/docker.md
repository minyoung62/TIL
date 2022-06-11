## Docker 이미지 & 컨테이너 
### 이미지 & 컨테이너: 무엇이며, 왜 사용하는가?
### 외부 이미지의 사용 & 실행
### 우리의 목표:NodeJS 앱
### Dockerfile을 사용하여 
### 자체 이미지를 기반으로 컨테이너 실행
### EXPOSE & 약간의 유틸리티 기능
### 이미지는 읽기 전용!
### 이미지 레이어 이해 
### 이미지 & 컨테이너 관리 
### 컨테이너 중지 & 재시작
### Attached & Detached컨테이너 이해하기
### 이미 실행중인 컨테이너에 연결하기
### 인터렉티브 모드로 들어가기 
### 이미지 & 컨테이너 삭제하기
  - 컨테이너 remove
    - docker rm [name...]
      - 실행중인것은 못지움  
  - 이미지 remove
    - docker images
      - 현재 로컬에 image list read
    - docker rmi [IMAGE Id...]
      - 삭제하려는 이미지가 더 이상 컨테이너에서 사용되지 않고
      - 중지나 사용중인 것이 하나도 없을때
      - 즉, 컨테이너에 없을 때 삭제 가능
    - docker image prune
      - 사용되지 않는 모든 이미지 제거  
### 중지된 컨테이너 자동 제거
  - docker run에 --rm 옵션
    - docker run -p 3000:80 -d --rm [IMAGE ID]
      - --rm은 컨테이너가 stop이 되면 자동으로 컨테이너를 지워줌
### 작동 배경 살펴보기: 이미지 검사
  - 이미지는 크지만 
  - 실행중인 컨테이너는 실제로는 크지 않음 
  - 왜냐하면 명령 레이어가 이미지 위에 추가된 얇은 부가 레이어이기 때문
  - 여러 컨테이너는 이미지 내부의 코드를 공유
  - 그래서 이미지 내부는 이 코드로 잠겨있다(이미지는 읽기전용모드이다)
  - docker image inspect [IMAGE ID]
    - 이미지에 대한 정보가 포한된 결과 출력 
### 컨테이너에/컨테이너로 부터 파일 복사하기
  - 컨테이너에 복사하기
    - docker cp dummy/. [container name]:/test 
      - dummy는 내 로컬에 있는 폴더
      - [container name]:/test는 복사하고싶은 컨테이너 [container name]에 경로 test (컨테이너 안에 test라는 경로가 없으면 자동으로 만들어줌)
  - 컨테이너로부터 파일 복사하기
    - docker cp [container name]:/test  dummy
      - container안에 있는 test폴더에서 내 컴퓨터(local)에 dummy폴더에 복사를 해줌 (로컬(내 컴퓨터에) dummy폴더가 없으면 자동으로 만들어줌) 
### 컨테이너와 이미지에 이름 지정 & 태그 지정하기
  - 컨테이너에 이름 지정
    - docker run -p 3000:80 -d --rm --name MY_Container_Name [IMAGE ID]
      - --name 옵션 뒤에 MY_Container_Name이름을 지정 
  - 이미지에 이름 태그 지정 
    - 이미지는 name과 tag가 존재(name : tag)
    - name는 node와 같은 그룹네임으로 정의
    - tag는 특정화된 버전 
    - docker build -t [name]:[tag] .
### 이미지 공유하기 - 개요
  - 1. Dockerfile을 공유하여 이미지 공유
  - 2. Share a Built Image
### DockerHub에 이미지 Push
  - docker hub에 login and create repository 
  - docker tag oldname minyoung62/node-hello-word 
    - dockerhub에 push를 위해 로컬에있던 이미지이름을 이 커맨드로 바꾼다
  - docker push minyoung62/node-hello-word
    - docker push시 이름이 일치해야함 
    - 하지만 acesss 가 안될꺼다 왜냐하면 dockerhub의 주인이 아니기 때문
    - 그래서 docker login명령어로 push하려는 컴퓨터에서 dockerhub로그인을 해줘야함 
  - 
