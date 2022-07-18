## WAS 서버 
  - open jdk 11설치
    - yum install java-11-openjdk-devel.x86_64
  - 환경변수 설정
    - export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-11.0.11.0.9-1.el7_9.x86_64  
  - 결과 확인
    - echo $JAVA_HOME
     
## DB 서버
  - postgresql을 도커로 실행시키는 명령어
    - docker run --name pgsql -d -p 5432:5432 -e POSTGRES_USER=postgresql -e POSTGRES_PASSWORD=postgrespassword postgres

  - postgresql-instance-1에서 실행해야할 명령어
    - sudo yum install -y docker
    - sudo systemctl start docker
    - sudo chmod 666 /var/run/docker.sock
    - docker run --name pgsql -d -p 5432:5432 -e POSTGRES_USER=postgresql -e POSTGRES_PASSWORD=postgrespassword postgres
  - db 접속
    - docker exec -it postgres /bin/bash 
