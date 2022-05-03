# Artillery를 사용한 부하 테스트(Stress Test) 
  - npm install -g artiller 실행
  - cpu-test.yaml 파일 생성

``` yaml
config:
  target: "http://{인스턴스 외부 ip}"
  phases:
    - duration: 60  // 60초동안 성능 측정
      arrivalRate: 1 // 가상유저 1명 
      name: Warm up
scenarios:
  - name: "just get hash" // test 이름 정의
    flow:
        - get:  //get 요청 
            url: "/hash/123"   // url 설정 
      
```
  - artillery.cmd run --output report.json .\cpu-test.yaml 실행 
  - 현재 경로에 report.json 만들어짐 (이것은 결과임)
  - artillery.cmd report .\report.json을 통해 html 형태로 변경 
  ![image](https://user-images.githubusercontent.com/61530368/166502051-f17cb102-895f-430e-937f-00e51eada69a.png)
  - 가로축은 시간 세로축은 지연 시간
  - Artillery 결론
    * 예상 TPS보다 여유럽게 (ex. 예상 TPS가 1000정도이면 트래픽이 튀는 상황을 대비해 3000 ~ 4000 정도로 잡기)
    * 기대 Latency를 만족할 때까지 (단일 요청에 대한 Latency가 기대하는 Latency 보다 높다면 Scale-out으로 해결 불가능 이러한 경우 코드가 비효율적이거나 해당 API의 I/O가 병목인 경우임)
    * Scale-out을 해도 성능이 늘지 않으면 병목을 의심 
