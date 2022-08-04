## Future
  - Future란?
    - 함수가 끝나고 다음 실행을 진행할 수 있게 해줌 
  - 코드
  ``` dart
  void main() {
    var myFuture = Future(() {
      return 'hello';
    });

    print('This runs first!');
    myFuture.then((result) => print(result));
    print('This also runs before the future is done!');
  }
  ```
  - 결과
  ``` 
  This runs first!
  This also runs before the future is done!
  hello
  ```
  - 하지만 비동기로 진행됨
## Future 체인 연결 및 에러 처리 방법
  - 코드
  ``` dart
  void main() {
    var myFuture = Future(() {

    });

    print('This runs first!');
    myFuture.then((_) => print('...'))
      .catchError((error) {
        //..
      })
      .then((_) {print('After first then');});
    print('This also runs before the future is done!');
  }
  ```
  - 위와 같이 then과 catchError를 사용할 수 있음 
  - 결과
  ``` 
  This runs first!
  This also runs before the future is done!
  ...
  After first then
  ```
