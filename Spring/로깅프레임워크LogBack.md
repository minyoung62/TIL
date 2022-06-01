# LogBack (출처 : 어라운드허브 스튜디오)
### Logback 이란?
  - Log4J를 기반으로 개발된 로깅(Logging) 라이브러리 
  - log4j에 비해 약 10배 정도 빠른 퍼포먼스, 메모리 효율성 증대
### Logback 특징
  - 로그에 특정 레벨을 설정할 수 있음(Trace -> Debug -> Info -> Warn -> Error)
  - 실운영과 테스트 상황에서 각각 다른 출력 레벨을 설정하여 로그를 확인할 수 있음
  - 출력 방식에 대해 설정할 수 있음
  - 설정 파일을 일정 시간마다 스캔하여 어플리케이션 중단 없이 설정 변경 가능
  - 별도의 프로그램 없이 자체적으로 로그 압축을 지원
  - 로그보관 기간 설정 가능
### Logback 구조
  ![image](https://user-images.githubusercontent.com/61530368/171388651-b37f2f80-f85e-4c25-84a6-51c821eb6b18.png)
### Logback 설정
  - 일반적으로 Classpath에 있는 logback 설정 파일을 참조하게 됨
### Logback 설정파일 형식
![image](https://user-images.githubusercontent.com/61530368/171389072-4694e883-9451-4c60-8052-18fc370f8433.png)
  - Appender 영역
    - Log의 형태 및 어디에 출력할지 설정하기 위한 영역
    - 대표적인 Appender 형식은 아래와 같은
      - ConsoleAppender : 콘솔에 로그를 출력
      - FileAppender : 파일에 로그를 저장
      - RollingFileAppender : 여러개의 파일을 순회하며 로그를 저장
      - SMTPAppender : 로그를 메일로 보냄
      - DBAppender : 데이터베이스에 로그를 저장 
  - encoder 
    - Appender 내에 포함되는 항목이며, patter을 사용하여 원하는 형식으로 로그를 표현할 수 있음
  - root
    - 설정한 Appender를 참조하여 로그의 레벨을 설정할 수 있음
    - root는 전역 설정이며, 지역 설정을 하기 위해서는 logger를 사용
### 로그 레벨
  - 레벨 순서
    - TRACE -> DEBUG -> INFO -> WARN -> ERROR
  - ERROR
    - 로직 수행 중에 오류가 발생한 경우, 시스템적으로 심각한 문제가 발생하여 작동이 불가한 경우
  - WARN
    - 시스템 에러의 원인이 될 수 있는 경고 레벨, 처리 가능한 사항 
  - INFO
    - 상태변경과 같은 정보성 메시지  
  - DEBUG
    - 어플리케이션의 디버깅을 위한 메시지 레벨   
  - TRACE  
    - DEBUG 레벨보다 더 디테일한 메시지를 표현하기 위한 레벨 
  - ex) 로그레벨을 'INFO'라고 설정했을 경우 'TRACE', 'DEBUG'레벨은 출력하지 않음
### pattern
  ![image](https://user-images.githubusercontent.com/61530368/171390278-629c4d1a-fa67-413a-af0e-41fdc83f7337.png)


