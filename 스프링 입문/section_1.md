> [스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard)
<br>
  
목차  
> [프로젝트 생성](#프로젝트-생성)  
> [라이브러리 살펴보기](#라이브러리-살펴보기)

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

    
