### 프로젝트 환경설정

#### 목차  
> [프로젝트 생성](#프로젝트-생성)  
> [라이브러리 살펴보기](#라이브러리-살펴보기)  
> [View 환경설정](#View-환경설정)  
> [빌드하고 실행하기](#빌드하고-실행하기)  

<br>

## 프로젝트 생성  
- Spring Intializr
  - SNAPSHOT : 만들고 있는 것
  - M1 : 정식 릴리즈 된 버전 아님
  - Project Metadata : group에 보통 기업명, Arifact는 빌드된 결과물
  ![Untitled](https://github.com/lpromotion/Inflearn_Spring/assets/88132500/21133274-378d-4904-baa5-6aaf12ca0d1e)

- build.gradle
  - repositories > mavenCentral() : 라이브러리를 다운받기 위한 사이트
  - dependencies : 추가된 라이브러리
- 실행 파일 -> main 메소드를 실행
![image](https://github.com/lpromotion/Inflearn_Spring/assets/88132500/ccdac3b8-9165-48da-82a4-cbe377c0c2e0)
  - localhost:8080 으로 접속하면 "Whitelabel Error Page"가 뜸 => 실행 성겅
  - SpringApplication.run(클래스명.class, args);
    - 스프링부트 애플리케이션이 실행됨
    - 톰켓 웹서버를 내장하고 있기 때문에, 톰켓 자체적으로 띄우면서 스프링부트가 올라옴
      ![image](https://github.com/lpromotion/Inflearn_Spring/assets/88132500/bc5f9f87-275f-471f-ab4c-198f3cacf4d0)

[목차](#목차)  
<br>  

## 라이브러리 살펴보기
![image](https://github.com/lpromotion/Inflearn_Spring/assets/88132500/f8b014b8-1f93-4ade-b00a-f3f65113e240)
- Gradle은 의존관계가 있는 라이브러리를 함께 다운로드함

- 스프링부트 라이브러리
  - spring boot starter-web  
    - spring-boot-starter-tomcat: 톰캣 (웹서버)
    - spring-webmvc: 스프링 웹 MVC
  - spring-boot-starter-thymeleaf: 타임리프 템플릿 엔진(View)  
  - spring-boot-starter(공통): 스프링 부트 + 스프링 코어 + 로깅  
    - spring-boot  
      - spring-core  
    - spring-boot-starter-logging  
      - logback, slf4j
- Log  
  ![image](https://github.com/lpromotion/Inflearn_Spring/assets/88132500/dcfc1112-c2e7-4e1f-99b1-9a8990b86ae0)  
  - spring-boot-starter-logging  
    - logback, slf4j이 많이 쓰기 때문에 기본적으로 포함되어 있음

- 테스트 라이브러리  
  - spring-boot-starter-test  
    - junit: 테스트 프레임워크  
    - mockito: 목 라이브러리  
    - assertj: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리  
    - spring-test: 스프링 통합 테스트 지원

[목차](#목차)  

<br>  
  
## View 환경설정  
- Welcome Page 만들기
  ![image](https://github.com/lpromotion/Inflearn_Spring/assets/88132500/90ae9957-1e84-49c6-8b10-29fb9c4f5973)  
  - resources/static/index.html (정적 페이지)
  - 스프링부트가 제공하는 Welcome Page 기능
    - static/index.html 을 올려두면 Welcome Page 기능을 제공함
  - spring.io > spring boot > spring boot reference documentation
- thymeleaf 템플릿 엔진
  - thymeleaf 공식 사이트: https://www.thymeleaf.org/
  - 스프링 공식 튜토리얼: https://spring.io/guides/gs/serving-web-content/
  - 스프링부트 메뉴얼: https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/
html/spring-boot-features.html#boot-features-spring-mvc-template-engines

- thymeleaf 템플릿엔진 동작 환경  
  ![image](https://github.com/lpromotion/Inflearn_Spring/assets/88132500/17b01e30-ea88-4d8d-8581-fb3c75e50097)
  - 컨트롤러에서 리턴값으로 문자를 반환하면 `viewResolver`가 화면을 찾아서 처리함
    - 스프링부트 템플릿엔진 기본 viewName 매핑
    - `resources:templates/` + {ViewName} + `.html`
  - `spring-boot-devtools`라이브러리를 추가하면, `html`파일을 컴파일만 해주면 서버 재시작없이 View 파일 변경이 가능함
    - 인텔리제이 컴파일 방법 : 메뉴 build -> Recompile

[목차](#목차) 
<br>  

## 빌드하고 실행하기   

콘솔로 이동
1. ./gradlew build  
2. cd build/libs (빌드할 경우 빌드 폴더가 생성됨)
3. java -jar hello-spring-0.0.1-SNAPSHOT.jar (build/libs 에 있는 파일)
4. 실행 확인

2에서 ./gradlew clean build 하면 빌드 폴더를 삭제하고 다시 생성  

[목차](#목차) 
