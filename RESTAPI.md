# REST_API

## REpresentational State Transfer

자원을 이름으로 구분해, 해당 자원의 상태를 주고 받는 모든 것

REST API를 이용하면 하나의 백엔드로 다양한 디바이스 지원이 가능하다

HTTP 프로토콜 인프라를 그대로 활용하기 때문에 별도의 인프라 구축이 불필요 하고, 서버와 클라이언트 역할을 명확하게 분리한다.

## 구성요소

HTTP GET /api/movie/{movie-id}

/api/movie/{movie-id} : HTTP URI로 Resource를 나타낸다

GET : HTTP Method로 자원에 대한 행동을 나타낸다

전체를 HTTP Message payload로 내용이라 한다.

## 설계 규칙

* **URI**: 정보의 자원을 표현해야 한다.
  리소스 명은 동사보다 명사를 사용한다(복수형)
* 자원에 대한 행위는 HTTP Method로 표현한다
* 슬래시(/)는 계층 관계를 나타내는데 사용한다.
* URI 마지막 문자는 슬래시를 포함하지 않는다.
* 하이픈(-)은 가독성을 높이는데 사용하지 않고, 밑줄(_)은 URI에 사용하지 않는다
* URI 경로는 소문자로 작성한다.
* 파일 확장자는 URI에 포함시키지 않는다.
* Accept Header에 확장자 정보를 포함시킨다.

## 이후 알아볼 것

REST API HTTP 응답코드

GraphQL