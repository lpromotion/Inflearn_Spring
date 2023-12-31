### 스프링 빈과 의존관계  

#### 목차  
> [회원 웹 기능 - 홈 화면 추가](#회원-웹-기능---홈-화면-추가)  
> [회원 웹 기능 - 등록](#회원-웹-기능---등록)  
> [회원 웹 기능 - 조회](#회원-웹-기능---등록)  

<br>  

## 회원 웹 기능 - 홈 화면 추가  

- 홈 컨트롤러 추가
  ```java
  package hello.hellospring.controller;
  
  import org.springframework.stereotype.Controller;
  import org.springframework.web.bind.annotation.GetMapping;
  
  @Controller
  public class HomeController {
  
      @GetMapping("/")
      public String home() {
          return "home";
      }
  }
  ```
<br>  

- 회원 관리용 홈
  ```html
  <!DOCTYPE HTML>
  <html xmlns:th="http://www.thymeleaf.org">
  <body>
  <div class="container">
      <div>
          <h1>Hello Spring</h1>
          <p>회원 기능</p>
          <p>
              <a href="/members/new">회원 가입</a>
              <a href="/members">회원 목록</a>
          </p>
      </div>
  </div> <!-- /container -->
  </body>
  </html>
  ```

> 참고 : 컨트롤러가 정적 파일보다 우선순위가 높음  
<br>

[목차](#목차)

<br>  

## 회원 웹 기능 - 등록  
#### 회원 등록 폼 개발   
- 회원 등록 폼 컨트롤러
  ```java
  @Controller
  public class MemberController {
  
      private final MemberService memberService;
  
      @Autowired // 스프링 컨테이너에서 memberService를 가져옴 (DI : Dependency Injection)
      public MemberController(MemberService memberService) {
          this.memberService = memberService;
      }
  
      @GetMapping("members/new")
      public String createForm() {
          return "members/createMemberForm";
      }
  }
  ```
<br>  

- 회원 등록 폼 HTML (`resources/templates/members/createMemberForm`)
  ```html
  <!DOCTYPE HTML>
  <html xmlns:th="http://www.thymeleaf.org">
  <body>
  <div class="container">
      <form action="/members/new" method="post">
          <div class="form-group">
              <label for="name">이름</label>
              <input type="text" id="name" name="name" placeholder="이름을 입력하세요">
          </div>
          <button type="submit">등록</button>
      </form>
  </div> <!-- /container -->
  </body>
  </html>
  ```
<br>  

#### 회원 등록 컨트롤러   
- 등록 화면에서 데이터를 전달 받을 폼 객체
  ```java
  package hello.hellospring.controller;
  
  public class MemberForm {
      private String name;
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  }
  ```
<br>  

- 회원 컨트롤러에서 회원을 실제 등록하는 기능
  ```java
  @PostMapping("members/new")
  public String create(MemberForm form) {
  
      Member member = new Member();
      member.setName(form.getName());
      
      memberService.join(member);
      
      return "redirect:/"; // 홈 화면으로
  }
  ```
<br>  

[목차](#목차)

<br>  

## 회원 웹 기능 - 조회  
- 회원 컨트롤러에서 조회 기능
  ```java
  @GetMapping("/members")
  public String list(Model model) {
      List<Member> members = memberService.findMembers();
      model.addAttribute("members", members);
      
      return "members/memberList";
  }
  ```
<br>  

- 회원 리스트 HTML
  ```html
  <!DOCTYPE HTML>
  <html xmlns:th="http://www.thymeleaf.org">
  <body>
  <div class="container">
      <div>
          <table>
              <thead>
              <tr>
                  <th>#</th>
                  <th>이름</th>
              </tr>
              </thead>
              <tbody>
              <tr th:each="member : ${members}"> <!-- th:each -> 루프가 돌아감 (타임리프 문법) -->
                  <td th:text="${member.id}"></td>
                  <td th:text="${member.name}"></td>
              </tr>
              </tbody>
          </table>
      </div>
  </div> <!-- /container -->
  </body>
  </html>
  ```
<br>  

- (단축키) Ctrl + e : 최근에 조회한 파일 목록

[목차](#목차)

<br>  
