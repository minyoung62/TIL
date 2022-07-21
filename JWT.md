# JWT 소개
  - Json 객체를 사용해 토큰 자체에 정보들을 저장하고 있는 Web Token 
  - Header, Payload, Signature로 구성
    - Header: Signature를 해싱하기 위한 알고리즘 정보 담음
    - Payload : 서버와 클라이언트가 주고 받는, 시스템에서 실제로 사용될 정보에 대한 내용 담음
    - Signature: 토큰의 유효성을 검증하는 용도 (Signature를 이용해 서버에서 토큰이 유효함을 검증)
# JWT 장단점
  - 장점
    - 중앙의 인정서버, 데이터 스토어에 대한 의존성 없음 수평확장에 유리 
    - Base64 URL Safe Encoding 방식을 이용해 URL, Cookie, Header 모두 사용 가능
  - 단점
    - Payload의 정보가 많아지면 네트워크 사용량 증가, 데이터 설계 고려 필요
    - 토큰이 클라이언트에 저장, 서버에서클라이언트의 토큰을 조작할 수 없음
