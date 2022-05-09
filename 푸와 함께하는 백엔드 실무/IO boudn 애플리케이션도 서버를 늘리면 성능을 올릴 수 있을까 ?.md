# IO bound 애플리케이션도 서버를 늘리면 성능을 올릴 수 있을까?
- 파일 bound 애플리케이션이라면 가능하지만 DB bound 애플리케이션은 서버를 늘려도 올릴 수 없음 (DB가 병목일 경우)
![image](https://user-images.githubusercontent.com/61530368/167344449-6c7bb299-928e-48c5-af09-81e256b8a0cb.png)
- 복습
  - 프로세스중 스케줄링에 선택을 받아 CPU가 실행시키는 시간을 CPU Burst
    - CPU Burst가 많은 애플리케이션은 CPU Bound 애플리케이션
  - 하드디스크에 읽기 쓰기를 하는 것은 IO Burst
    - IO Burst가 많은 애플리케이션은 IO Bound 애플리케이션 
![image](https://user-images.githubusercontent.com/61530368/167345069-66f63ecb-d8c1-4cd7-b607-a9ea35dfa5f8.png)
- 서버 Scale-out 
  - 서버를 아무리 Scale-out하더라도 DB에 성능이 낮으면 전체 서비스의 성능은 DB의 속도와 같아짐 (병목 현상이라함)

