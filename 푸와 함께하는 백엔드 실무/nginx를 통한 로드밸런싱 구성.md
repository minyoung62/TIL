# nginx를 통한 로드밸런싱 구성

  - GCP 인스턴스 추가 생성
    - 머신 이미지를 통해 기존의 워크 인스턴스 복제
    - 동일한 환경(설치해준 패키지, 키타 파일)의 인스턴스 복제 
    ![image](https://user-images.githubusercontent.com/61530368/166653893-e0f700f4-96f6-4c34-84e5-1bf445dd47d8.png)
    - 아래의 그림과 같이 인스턴스 만들기를 통해 복제 인스턴스 생성
![image](https://user-images.githubusercontent.com/61530368/166654470-25b53fe8-d0be-455d-8d37-6f6884c1d51a.png)
    - 아래와 같이 인스턴스 3개 생성
![image](https://user-images.githubusercontent.com/61530368/166654895-210665cb-54a6-4fb6-aade-b368444da412.png)
  - jenkins 설정
    - 새로운 워크 인스턴스 2, 3을 추가 등록 
    - /dev/null 대신 nohub.out으로 정보 출력 (/dev/null은 내용을 쓰레기통으로 보내는 것) 

  
  - nginx
    - GCP nginx 인스턴스 생성
    - sudo yum install nginx 명령어 실행
    - sudo systemctl start nginx 명령어 실행
    - 웹에서 nginx 인스턴스 외부 아이피 접속
    ![image](https://user-images.githubusercontent.com/61530368/166659874-edf57574-b2cd-4b30-b053-1d1d9eb4793a.png)
  
  - nginx 설정
    - sudo vi /etc/nginx/nginx.conf 명령어 입력
    - 아래의 upstream cpu-bound-app { } 과 location / {} 추가 및 파란색 네모 박스에 워크 인스턴스 내부 아이피 입력
    ![image](https://user-images.githubusercontent.com/61530368/166661563-ed63809f-a864-42d3-9a0c-409aa09c6438.png)
    - sudo systemctl reload nginx 를 통해 nginx 리로드
    - but 아직 404에러 
    - sudo tail -f /var/log/nginx/error.log; 명령어를 통해 에러 확인 
    - sudo setsebool -P httpd_can_network_connect on로 해결 가능
    - 웹에서 nginx 외부 아이피/hash/123 으로 접속
    -  로드밸런싱 구성 완료  
    ![image](https://user-images.githubusercontent.com/61530368/166664727-3583e3f1-1326-40e3-aa7c-172ca4d01b27.png)

     
