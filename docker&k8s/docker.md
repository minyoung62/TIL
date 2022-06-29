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

### 볼륨
  - 익명 볼륨(anonymous volum)
    - 커맨드 사용법 
      - docker run -d -p 3000:80 -v /app/feedback --name test test
      - --v /app/feedback 이것이 익명 볼륨 
    - Dockerfile에서 사용법  
      - VOLUM["/app/feedback"]
    - 익명 볼륨은 컨테이너가 삭제되면 없어짐 
    - docker volum list를 통해 Volum확인 가능
    - 익명 볼륨 삭제
      - 익명볼륨은 --rm 옵션이 있을 때만 자동 삭제된다  
      - 때문에 docker rm로 컨테이너를 삭제하더라도 익명볼륨은 여전히 존재
      - 추가로 새로운 컨테이너 생성시 계속적으로 새 익명 볼륨 생성
      - 이렇게 되면 익명 볼륨이 쌓이기 시작
      - docker volume rm VOL_NAME or docker volume prune을 통해 볼륨 삭제 가능 
  - 명명된 볼륨(named volum)
    - 커맨드 사용법
      - docker run -d -p 3000:80 -v feedback:/app/feedback --rm --name test test
      - -v feedback:/app/feedback 
    - 명명된 볼륨은 컨테이너가 삭제되더라도 없어지지 않음
    - 몀명된 볼륨은 Dockerfile에 작성 못함
    - 컨테이너간 데이터 공유에 사용 가능 
### 바인드 마운트 
  - 바인드 마운트 
    - 데이터 저장및 편집시 사용
    - 사용법
      - docker run -d -p 3000:80 -v C:\Users\minyoung\Downloads\data-volumes-01-starting-setup\data-volumes-01-starting-setup:/app --rm --name test test
      - -v my프로젝트_절대경로:/app/feedback   
      - 경로에 특수 문자나 공백이 포함된 경우 ""를 이용해 경로 설정      
### 다른 볼륨 결합 & 병합하기 
  - 바인드 마운트의 문제
    - 바인드 마운트를 사용하면 모든 파일을 다 덮어씌운다 
  - 해결법
    - 때문에 익명 볼륨을 같이 사용하여 덮어씌우면 안되는 파일은 아래와 같이 -v를 한번 더 추가하여 특정 파일은 덮어 씌우는 것을 막을 수 있음 
      - docker run -d -p 3000:80 -v C:\Users\minyoung\Downloads\data-volumes-01-starting-setup\data-volumes-01-starting-setup:/app -v /app/node_modules --rm --name test test
      - docker는 더 깊은 경로를 먼저 우선으로함 그래서 -v /app/node_modules 인 익명 볼륨을 먼저 설정해줌 (즉, 바인드 마운트로인해 덮어씌워지지않음)
      - Dockerfile에서도 설정가능 VOLUME ["/app/node_modules"] 
### 읽기 전용 볼륨 
  - 디폴트 볼륨
    - read - write 제공
  - 읽기 전용 볼륨
    - 읽기 전용 볼륨은 호스트 머신에서만 변경이 가능하고 docker 컨테이너 내에서는 읽기만 가능한 것이다  
    - docker run -d -p 3000:80 -v feedback:/app/feedback -v C:\Users\minyoung\Downloads\data-volumes-01-starting-setup\data-volumes-01-starting-setup:/app:ro -v /app/node_modules --rm --name test test 
    - C:\Users\minyoung\Downloads\data-volumes-01-starting-setup\data-volumes-01-starting-setup:/app:ro  (:ro 읽기 전용)
### "COPY"사용 vs 바인드 마운트 사용
  - Dockerfile 내에 COPY를 없애도 바인드마운트가 있으면 컨테이너가에 어플리케이션이 정상적으로 돌아감 
  - 그러나 바인드마운트는 개발용임 
  - 로컬에서 개발을 마치고 서버에 넣을 때는 Dockfile에서 카피가 있어야 어플리케이션이 실행됨(왜냐하면 서버에는 코드가없기때문!)
  - 추가로 프로덕션 환경에서는 코드가 변경되지않기 때문 (즉, 스냅샷이 컨테이너가 돌아감)
### dockerignore 파일 사용하기
  - Dockerfile에서 COPY명령어 사용시 원하지 COPY를 원하지 않는 것은 dockerignore에 넣어줌 
  - .dockerignore 파일 생성 
    - ex) node_modules 추가
    - ex) .git
### 환경 변수 & ".env"파일 작업
  - Dockerfile에 EVN 명령어
    ```
     ENV PORT 80  # PORT 환경변수에 80넣기 
     EXPOSE $PORT # PORT가 환경변수 
    ```
    -docker run -d -p 3000:8000 --env PORT=8000  -v feedback:/app/feedback -v C:\Users\minyoung\Downloads\data-volumes-01-starting-setup\data-volumes-01-starting-setup:/app:ro -v /app/node_modules --rm --name test test 
    - --env PORT=8000 이렇게 해서 server의 port를 하드코딩 안하고 argument로 넣어줄 수 있음(-e도 사용가능)
    - --env-file ./.env (파일에 적어서 사용도 가능, .env 파일이 있어야함) 
### 빌드 인수(ARG) 사용하기
  - Dockerfile에 ARG명령어
    ```
     ARG DFFAULT_PORT=80  
     ENV PORT $DEFAULT_PORT
     EXPOSE $PORT
     build
    ``` 
    - docker build -t fededback-node:dev --build-arg DEFAULT_PORT=8000 . 
## 네트워킹:(교차)컨테이너 통신
  - 로컬호스트머신과 웹 통신하기 
    - 기본컨테이너(네트워크 설정을하지 않은)는 로컬호스트머신에 작동중인 어플리케이션(DB서버와같은)과 통슨을 못한다
    - 그러나 웹페이지 및 웹 RESTAPI와는 통신할 수 있다  
  - 호스트머신 통신 작업을 위한 컨테이너 만들기
    - mogodb://localhost:27017/test와 같이 일반적인 연결이 아닌,
    - 도커가 알 수 있는 특별한 주소로 해줘야함(아래와 같이) 
    - mogodb://host.docker.internal:27017/test
  - 컨테이너간 통신 
    - 몽고디비 컨테이너로 실행
      - docekr run -d --name mongodb -p  mongo --network favorites-net
      - --network favorites-net 이것을 통해 컨테이너들을 한 네트워크(favorites-net)에 밀어넣어줌
      - 그러나, favorites-net가 네트워크에 존재하지않으면 만들어야함 (볼륨과 달리 네트워크는 도커에서 자동으로 생성해주지 않음!)
  - 네트워크 만드는 법
    - docker network create favorites-net 이것을 이용해 커스텀 네트워크를 만들 수 있음 
  - 어플리케이션에서 연결방법
    - 앞서 네트워크도 만들었고 몽고디비도 favorites-net 네트워크 환경에서 실행시켰다.
    - 그러면 어플리케이션에서 몽고디비로 연결을 해야함
    - 기존에는 mogodb://localhost:27017/test이렇게 연결하였지만 이제는 같은 네트워크를 사용함으로 localhost대신 컨테이너 이름을 넣어준다 
    - mogodb://mogodb:27017/test (몽고디비 컨테이너 이름을 mongodb로 했었음) 
    - 도커에서 자동으로 mongodb이름을 보고 컨테이너의 ip주소로 자동 변환해줌 
## Docker Compose
### Docker Compose 사용 이유
  - Docker Compose란 ?
    - 각각의 컨테이너를 한번에 실행하고 멈추는 것
  - 사용 이유  
    - 다중컨테이너를 사용하게되면 각각의 컨테이너 마다 run명령어를 해줘야함
    - 추가로 run명령어시 옵션에 다양한 옵션이 존재하는데 각 컨테이너마다 다름 
    - 이러한 이유로 옵션을 잘못 설정할 시 그것을 해결하느라 많은 시간을 소비
    - 이것이 Docker Compose를 사용하는 이유
### Docker Compose파일 설정하기
  - docker-compose.yaml파일 생성
    ``` yaml
    version: "3.8" # docker-compose version
    services: # 실행할 container
      mongodb:
        image: 'mongo' # 이미지이름 
        volumes:
          - data:/data/db
        environment:
          MONGO_INIDB_ROOT_USERNAME: max
          MONGO_INIDB_ROOT_PASSWORD: max
        #env_file: #환경변수를 파일로 만들때 사용 방법 
        #  - ./mongo.env 
        container_name: mongodb # container 이름 설정 
      backend:
        build: ./backend #backedn폴더에 Dockerfile을 이용해 빌드해줌  
        #build: (이렇게도 사용 가능)
        #  context: ./backend
        #  dockerfile: Dockerfile-dev
        #  args:
        #   some-arg: 1
        ports: 
          - '80:80' #(호스트포트:도커포트)
        volumes:
          - logs:/app/logs #named 볼륨
          - ./backend:/app (docker-compose에서는 bound mount를 사용할 때 상대경로를 적용해줄 수 있음)
          - /app/node_modules # 익명 볼륨 
        env_file:
          - ./env/backend.env
        depends_on:  # docker run에는 없는 명령어(compose에만 있음)
          - mognodb # backend컨테이너가 mognodb에 의존하기 때문에 디펜던시에 추가해줘야함
      frontend:
        build ./frontend
        ports:
          - '3000:3000'
        volumes:
          - ./frontendsrc:/app/src
        stdin_open: true #(개방형 입력 연결이 필요하다는 옵션)
        tty: true #(터미널에 연결하는 옵션, stdin_open, tty = -it 옵션과 같은 ) 
        depends_on:
          - backend
      
    volumes:
      data: # named 볼륨을 사용하려면 이렇게 해줘야함
      logs: # named 볼륨을 사용하려면 이렇게 해줘야함
 
    ```
    - --rm -d옵션은 compose에서 디폴트로 제공 
  - docker-compose 실행
    - docker-compose.yaml파일에 있는 경로에서 아래의 명령어 실행
    - docker-compose up [-d]
      - -d옵션은 detach모드   
  - docker-compose 종료
    - docker-compose down [-v]
      - -v옵션은 볼륨을 제거해줌   
  - build 옵션
    - docker  
 ## "유틸리티 컨테이너"로 작업하기 & 컨테이너에서 명령 실행하기
 ### 첫 번째 유틸리티 컨테이너 구축
  - Dockerfile을 생성
    ``` yml
    FROM node:14-alpine
    
    WORKDIR /app
    ```
  - 명령어 실행
    - docker run -it -v /home/kimminyoung6709/work:/app node-util npm init 
  - 이 유틸리티를 이용하여 모든 부가 도구를 설치할 필요없이 npm init을 사용가능
### ENTRYPOINT 활용
  - Dockerfile
    ``` yml
    FROM node:14-alpine

    WORKDIR /app

    ENTRYPOINT ["npm"]                                
    ```
  - 명령어 실행
    -  docker run -it -v /home/kimminyoung6709/work:/app mynpm init
  - ENTRYPOINT 뒤에 mynpm init이 붙어서 실행됨
  - docker run -it -v /home/kimminyoung6709/work:/app mynpm install express --save
    - 이것은 express를 설치 및 패키지를 추가하는 것
  - 하지만 npm 명령어를 사용할 때 앞에 많은 명령어가 붙음(docker run -it -v /home/kimminyoung6709/work:/app  이런 것들)
### Docker Compose 사용 
  - docker-compose.yaml
    ``` yaml
    version: "3.8"
    services:
      npm:
        build: ./
        stdin_open: true
        tty: true
        volumes:
         - ./:/app
    ```
  - 명령어
    - docker-compose run npm init 
    - docker-compose run --rm npm init (자동으로 삭제) 

## 더 복잡한 설정: Laravel & PHP 도커화 프로젝트

## Docker 컨테이너 배포하기 
###  
