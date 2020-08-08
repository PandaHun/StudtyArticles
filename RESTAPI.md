# RESTful API

- `REST`: REpresentational State Transfer의 약자
- 소프트웨어 아키텍처의 한 형식으로 **자원을 정의하고 자원에 대한 주소를 지정하는 방법 전반에 대한 패턴**
- 쉽게 말해 자원을 이름으로 구분해, 해당 자원의 상태를 주고 받는 모든 것
- `REST`는 **Resource Oriented Architecture** 이다.
- API 설계의 중심에 자원(Resource)이 있고 HTTP Method 를 통해 자원을 처리하도록 설계하는 것이다.

## REST의 특징 혹은 원칙

- Uniform Interface
  - URI로 지정한 자원에 대한 조작을 통일되고 한정적인 인터페이스로 수행하는 아키텍쳐 스타일(제약조건의 집합)
  - Self-Descriptive message
    - 메시지는 스스로 설명해야 한다
  - HATEOAS(hypermedia as the engine of application state)
    - 어플리케이션의 상태는 Hyperlink를 이용해 전이 되어야 한다.
- Stateless
  - 작업의 상태를 저장하거나 관리하지 않는다.
  - 들어오는 요청만을 처리하면 된다.
  - 서비스의 자유도가 높아지고 서버에서 불필요한 정보를 관리하지 않음으로써 구현이 단순해집니다.
- Caching
  - HTTP의 기본 웹 표준을 사용하므로 HTTP가 가진 캐싱 기능을 사용할 수 있다.
- Client-Server
- Hierarchical system
- Code on demand(Optional)
  - 서버에서 클라이언트에 코드를 보내 실행 시킬 수 있어야 한다. (JS)
  - client에 보내는 데이터를 바로 실행 가능한 코드를 보내서 이것을 Client에서 실행하는 것

## REST API 구성요소

- **자원(RESOURCE)** - URI
- **행위(Verb)** - HTTP METHOD
- **표현(Representations)**

## REST API 디자인 가이드

- 첫 번째,

   URI는 정보의 자원을 표현해야 한다.

  - 자원을 표현하는데 중점을 둬야 한다.
  - 행동이 들어가면 안된다.

- **두 번째,** 자원에 대한 행위는 HTTP Method(GET, POST, PUT, DELETE)로 표현한다.

### 주의할 점

- 슬래시(/)는 계층 관계를 나타내는데 사용한다.
- URI 마지막 문자는 슬래시를 포함하지 않는다.
- 하이픈(-)은 가독성을 높이는데 사용하지 않고, 밑줄(_)은 URI에 사용하지 않는다
- URI 경로는 소문자로 작성한다.
- 파일 확장자는 URI에 포함시키지 않는다.
- Accept Header에 확장자 정보를 포함시킨다.

## 장점

1. Open API 를 제공하기 쉽다
2. 멀티플랫폼 지원 및 연동이 용이하다.
3. 원하는 타입으로 데이터를 주고 받을 수 있다.
4. 기존 웹 인프라(HTTP)를 그대로 사용할 수 있다.

## 단점

1. 분산환경에는 부적합하다.
2. HTTP 통신 모델에 대해서만 지원한다.