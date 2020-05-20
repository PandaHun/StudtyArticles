# Spring Architecture

## 1. Pom.xml

- 필요한 라이브러리를 센터 저장소에서  로컬로 다운 받아 현재 프로젝트에 사용할 수 있게 주입해줌 Package Manager 느낌
- 주로 Maven Central에서 복사해 `Dependency`를 추가함

## 2. WEB-INF/web.xml

- 설정 파일의 위치
  - 객체 관리 컨테이너의 설정 파일 위치: `root-context.xml`
  - 컨트롤러의 위치: `servlet-context.xml`
- `dispatcherServlet` 위치
- 한글 처리 필터

## 3. spring/appServlet/servlet-context.xml

- 리소스 위치 설정
- `ViewResolver` 설정
- 컨트롤러들을 등록

## 4-1. Controller.java

* `@Component`: 아래 세가지 관련

  * `@Respoitory`: DB 관련
  * `@Service`: Service 관련
  * `@Controller`: MVC의 Controller 관련

  ## 4-2. Spring/root-context.xml

  - DB 설정
    - sqlMapper 위치: `mappers/*.xml`
    - 네임스페이스 위치: `mybatis-config.xml`
  - 컨트롤러를 제외한 나머지 모든 객체 관리

  ## 5. resource/mappers/*.xml

  - sql 문장

  ## 6. resoucre/mybatis-config.xml

  - 네임스페이스: sql 문장에서 사용해야하는 패키지명 등록