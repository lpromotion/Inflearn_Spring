### 스프링 웹 개발 기초  

#### 목차  
> [정적 컨텐츠](#정적-컨텐츠)  
> [MVC와 템플릿 엔진](#MVC와-템플릿-엔진)  
> [API](#API)  

<br>

## 정적 컨텐츠  
- 파일을 그대로 웹 브라우저에 내려주는 방식
- 스프링부트 정적 컨텐츠 기능
  - https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content
  <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/d76c884b-ddf7-48c9-9fd3-ae49b6f85f4d" width="80%">  
- 정적 컨텐츠 이미지  
  <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/6b533abb-ccf1-4805-bf78-9043a03a52eb" width="70%">   
  
[목차](#목차)

<br>

## MVC와 템플릿 엔진  
- 가장 많이 하는 방식. 서버에서 프로그래밍해서 html을 동적으로 바꿔서 내리는 방식
- MVC : Model, View, Controller
- Controller  
  ```java
  @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam(value="name", required = false) String name, Model model){
        model.addAttribute("name", name);
        return "hello-template";
    }
  ```
- View
  ```html
  <html xmlns:th="http://www.thymeleaf.org">
  <body>
  <p th:text="'hello ' + ${name}">hello! empty</p>
  </body>
  </html>
  ```
- MVC, 템플릿 엔진 이미지  
  <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/b498889c-4e2b-49eb-aecb-bbd366aa5fd1" width="70%">   

  
[목차](#목차)

<br>

## API  
- json 데이터 포맷으로 클라이언트에 데이터를 전달하는 방식
- @ResponseBody 문자 반환 
  - Controller
    ```java
    @GetMapping("hello-string")
      @ResponseBody
      public String helloString(@RequestParam("name") String name){
          return "hello " + name;
      }
    ```  
  -  HTTP의 BODY에 문자 내용을 직접 반환(HTML BODY TAG를 말하는 것이 아님) (HTTP에는 HEADER와 BODY로 나)
  - `@ResponseBody`를 사용하면 뷰 리졸버(`viewResolver`)를 사용하지 않음
- @ResponseBody 객체 반환
  - Controller
    ```java
    @GetMapping("hello-api")
    @ResponseBody
    public Hello helloApi(@RequestParam("name") String name){
        Hello hello = new Hello();
        hello.setName(name);
        return hello;
    }

    static class Hello {
        private String name;

        // Getter, Setter - Java Bean 규약
        // 메서드를 통해 접근함
        // 프로퍼티 접근 방식
        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }
    ```
  - `@ResponseBody` 를 사용하고, 객체를 반환하면 객체가 JSON으로 변환됨  
    <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/c4ad9867-01b0-4a46-919e-dc0308c805a4" width="70%">
- @ResponseBody 사용 원리
  <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/e32ea195-efda-4a2e-8b02-5d654ad7819c" width="70%">
- `@ResponseBody` 를 사용
  - HTTP의 BODY에 문자 내용을 직접 반환
  - `viewResolver` 대신에 `HttpMessageConverter`가 동작
  - 기본 문자처리 : `StringHttpMessageConverter`
  - 기본 객체처리 : `MappingJackson2HttpMessageConverter`
  - byte 처리 등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음  
- 참고) 클라이언트의 HTTP Accept 해더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서
`HttpMessageConverter` 가 선택된다.
  
[목차](#목차)
