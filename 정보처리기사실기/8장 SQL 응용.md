## 118 SQL
### DDL
  - DB 구조, 데이터 형식, 접근 방식 등 DB를 구축하거나 수정할 목적으로 사용하는 언어
  - DDL의 3가지 유형
    - CREATE: CHEMA, DOMAIN, TABLE, VIEW, INDEX를 정의
    - ALTER: TABLE에 대한 정의를 변경하는데 사용
    - DROP: SCHEMA, DOMAIN, TABLE, VIEW, INDEX를 삭제함
### CREATE SCHEMA
  - 스키마를 정의하는 명령문
  - 표기 형식
    - CREATE SCHEMA 스키마명 AUTHORIZATIONE 사용자_id;
    - 예제 ) 소유권자의 사용자의 ID가 홍길동인 스키마 대학교를ㄹ 정의하는 SQL문은 다음과 같다
      - CREATE SCHEMAA 대학교 AUTHORIZATION 홍길동;
### CREATE DOMAIN
  - 도메인을 정의하는 명령문
  - 표기형식
    ``` sql
    CREATE DOMAIN 도메인명 [AS] 데이터_타입
      [DEFAULT 기본값]
      [CONSTRAINT 제약조건명 CHECK (범위값)];
    ```
    - 데이터 타입: SQL에서 지원하는 데이터 타입
    - 기본값: 데이터를 입력하지 않았을 때 자동으로 입력되는 값
    - 예제) 성별을 남 또는 여와 같이 정해진 1개의 문자로 표현되는 도메인 SEX를 정의하는 SQL문은 다음과같다 
      ``` sql
      CREATE DOMAIN SEX CHAR(1) // 정의된 도메인은 이름이 SEX이며, 문자형이고 크기는 1자
        DEFAULT '남'             // 도메인 SEX를 지정한 속성의 기본 값은 남자
        CONSTRAINT VALID-SEX CHECK(VALUE('남', '여')); // SEX를 지정한 속성에는 남, 여 중 하나의 값만 저장 가능
      ```
### CREATE TABLE
  - 테이블을 정의하는 명령문 
  - 표기형식

## 119 SQL - DCL
### DCL(Data Control Language, 데이터 제어어)
  - 데이터의 보안, 무결성, 회복, 병행 제어 등을 정의하는 데 사용하는 언어
  - DCL은 데이터베이스 관리자(DBA)가 데이터 관리를 목적으로 사용
  - DCL의 종류
    - COMMIT: 명령에 의해 수행된 결과를 실제 물리적 디스크로저장하고, 데이터베이스 조작 작업이 정상적으로 완료되었음을 관리자에게 알려줌
    - ROLLBACK: 데이터베이스 조작 작업이 비정상적으로 종료되었을 때 원래의 상태로 복구함
    - GRANT: 데이터베이스 사용자에게 사용 권한을 부여함
    - REVOKE: 데이터베이스 사용자의 권한을 취소함
### GRANT / REVOKE
  - GRANT: 권한 부여를 위한 명령어
  - REVOKE: 권한 취소를 위한 명령어
  - 사용자등급 지정 및 해제
    - GRANT 사용자등급 TO 사용자_ID_리스트 [IDENTIFIED BY 암호];
    - REVOKE 사용자등급 FROM 사용자_ID_리스트;
    - 예제) 사용자 ID가 NABI인 사람에게 데이터베이스 및 테이블을 생성할 수 있는 권한을 부여하는 SQL문
      - GRANT RESOURCE TO NABI;
    - 사용자 ID가 "START"인 사람에게 단순히 데이터베이스에 있는 정보를 검색할 수 있는 권한을 부여하는 SQL문
      - GRANT CONNECT TO STAR;
### COMMIT
  - 트랜잭션 처리가 정상적으로 완료된 후 트랜잭션이 수행한 내용을 데이터베이스에 반영하는 명령어
### ROLLBACK
  - 변경되었으나 아직 COMMIT되지 않은 모든 내용들을 취소하고 데이터베이스를 이전 상태로 되돌리는 명령어
### SAVEPOINT
  - 트랜잭션 내에 ROLLBACK 할 위치인 저장점을 지정하는 명령어

## 120 SQL - DML
### DML(Data Manipulation Language, 데이터 조작어)
  - 데이터베이스 사용자가 저장된 데이터를 실질적으로 관리하는데 사용되는 언어
  - DML의 유형
    - SELECT: 테이블에서 튜프을 검색
    - INSERT: 테이블에 새로운 튜프을 삽입
    - DELETE: 테이블에서 튜플을 삭제
    - UPDATE: 테이블에서 튜플의 내용을 갱신
### 삽입문(INSERT INTO ~)
  - 튜픙르 삽입할 때 사용
  - 일반 형식
    ``` sql
    INSERT INTO 테이블명([속성명1, 속성명2, ...)]
    VALUES (데이터1, 데이터2, ...)
    ```
    - 대응하는 속성과 데이터는 개수와 데이터 유형이 일치해야 함
    - 기본 테이블의 모든 속성을 사용할 때는 속성명을 생략 가능
    - SELECT문을 사용하여 다른 테이블의 검색 결과를 삽입 가능
  - 예시
    - INSERT INTO 사원(이름, 부서) VALUES('홍승현','인터넷);
### 삭제문(DELETE FROM ~)
  - 일반 형식
    ``` sql
    DELETE
    FROM 테이블명
    [WHERE 조건];
    ```
    - 모든 레코드를 삭제하더라도 테이블 구조는 남아 있기 때문에 디스크에서 테이블을 완전히 제거하는 DROP는 다름
  - 예시
    ``` sql
    DELETE
    FROM 사원
    WHERER 이름 = '임꺽정';
    ```
### 갱신문(UPDATE ~ SET ~)
  - 일반 형식
    ``` sql
    UPDATE 테이블명
    SET 속성명 = 데이터[, 속성명 = 데이터, ...]
    [WHERE 조건];
    ```
  - 예시
    ``` sql
    UPDATE 사원
    SET 주소 = '수색동'
    WHERE 이름 = "홍길동';
    ```

