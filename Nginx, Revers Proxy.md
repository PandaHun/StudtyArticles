# Nginx?

- Nignx는 Web Server(WS)! WAS가 아니다
  - WS의 대표적인 예는 Apache
- 클라이언트로 부터 요청이 발생했을 때 요청에 맞는 정적 파일(html, css 등)을 보내 주는 역할
- Nginx는 규모가 작은 서비스 이면서 정적 데이터 처리가 많은 서비스에 적합
- HTTP의 WS 역할 외에도 proxy, reverse proxy 서버의 역할 또한 가능
- Apache는 다중 처리 모듈이지만 Nginx는 Event Drvien
- 메모리를 보다 효율적으로 관리
- 오로지 성능에 초점을 둠으로써 필요 없는 기능들은 삭제

# Reverse Proxy

- 클라이언트의 요청을 받아 적절한 웹 서버에 전달하는 역할
  - 단순히 전달만 담당
  - 처리는 그 뒤의 웹 서버가 수행한다
- 웹 서버는 전달 받은 요청을 수행 한 후 클라이언트에 응답하는 것이 아니라, 프록시에게 응답한다.
- 하나의 리버스 프록시가 여러 웹 서버에 요청을 전달하도록 구성할 수 있다.
  - 로드 밸런서의 역할을 수행

## 왜 리버스 프록시를 사용하는가?

- HTTP 요청 내용에 따라 시스템의 동작을 제어한다.
- 실제 서버 접근을 차단
  - 외부의 요청을 받는 ip는 proxy의 ip.
  - 서버의 ip를 알지 못 해 보호할 수 있다.
- 시스템 전체의 메모리 효율을 향상 시킨다.
  - WAS(Tomcat 등)은 하나의 요청에 하나의 프로세스 혹은 쓰레드를 할당해 요청을 처리한다.
  - 정적 콘텐츠를 캐싱해 사용할 뿐 아니라, 동적 콘텐츠 또한 캐싱 할 수 있다.(Web acceleration)