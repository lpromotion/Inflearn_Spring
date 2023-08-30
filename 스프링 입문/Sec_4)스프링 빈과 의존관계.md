### 스프링 빈과 의존관계  

#### 목차  
> [컴포넌트 스캔과 자동 의존관계 설정](#컴포넌트-스캔과-자동-의존관계-설정)  
> [자바 코드로 직접 스프링 빈 등록하기](#자바-코드로-직접-스프링-빈-등록하기)

<br>

## 컴포넌트 스캔과 자동 의존관계 설정  
회원 컨트롤러가 회원 서비스와 회원 리포지토리를 사용할 수 있게 의존관계를 준비  
<br>  

- 회원 컨트롤러에 의존관계 추가
  ```java
  package hello.hellospring.controller;
  
  import hello.hellospring.service.MemberService;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.stereotype.Controller;
  
  @Controller
  public class MemberController {
  
      private final MemberService memberService;
  
      @Autowired // 스프링 컨테이너에서 memberService를 가져옴 (DI : Dependency Injection)
      public MemberController(MemberService memberService) {
          this.memberService = memberService;
      }
  }
  ```
  - 생성자에 `@Autowired`가 있으면 스프링이 연관된 객체를 스프링 컨테이너에서 찾아서 넣어줌.
    이렇게 객체 의존관계를 외부에서 넣어주는 것을 DI (Dependency Injection), 의존성 주입이라 함.
  - 이전 테스트에서는 개발자가 직접 주입했고, 여기서는 `@Autowired`에 의해 스프링이 주입해줌
 
<br>  

- 오류 발생  
  `Consider defining a bean of type 'hello.hellospring.service.MemberService' in
your configuration.`  
  memberService가 스프링 빈으로 등록되어 있지 않음  
  <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/5634ddeb-9cc1-49aa-97ee-bc8e742bba2d.png" width="60%">  
 
<br>  

> 참고 : helloController는 스프링이 제공하는 컨트롤러여서 스프링 빈으로 자동 등록됨  
> `@Controller`가 있으면 자동 등록됨  

<br>  

스프링 빈을 등록하는 2가지 방법
- 컴포넌트 스캔과 자동 의존관계 설정
- 자바 코드로 직접 스프링 빈 등록하기  

<br>  

- 컴포넌트 스캔 원리
  - `@Component` 애노테이션이 있으면 스프링 빈으로 자동 등록됨
  - `@Controller` 컴트롤러가 스프링 빈으로 자동 등록된 이유도 컴포넌트 스캔 때문
 
<br>  

- `@Component`를 포함하는 다음 애노테이션도 스프링 빈으로 자동 등록됨
  - `@Controller`
  - `@Service`
  - `@Repository`
 
<br>  

- 회읜 서비스 스프링 빈 등록
  ```java
  @Service // 스프링이 올라올 때, 스프링 컨테이너에 MemberService를 등록해줌
  public class MemberService {
  
      // 회원 리포지토리
      private final MemberRepository memberRepository;
  
      // 생성 > Constructor(생성자)
      // MemberRepository를 외부에서 넣어주도록 함
      @Autowired
      public MemberService(MemberRepository memberRepository) {
          this.memberRepository = memberRepository;
      }
  
  }
  ```
 
<br>  

> 참고 : 생성자에 `@Autowired`를 사용하면 객체 생성 시점에 스프링 컨테이너에서 해당 스프링 빈을 찾아서 주입함  
> 생성자가 1개만 있으면 `@Autowired`는 생략할 수 있음  
 
<br>  

- 회원 리포지토리 스프링 빈 등록
  ```java
  @Repository
  public class MemoryMemberRepository implements  MemberRepository {}
  ```
 
<br>  

- 스프링 빈 등록 이미지  
  <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/560c691c-4a95-4b42-a02b-63c1f522aa95.png" width="60%">  
  - `memberService`와 `memberRepository`가 스프링 컨테이너에 스프링 빈으로 등록됨

<br>  

> 참고 : 스프링은 스프링 컨테이너에 스프링 빈을 등록할 때, 기본으로 싱글톤으로 등록함.(유일하게 하나만 등록해서 공유함)  
> 따라서 같은 스프링 빈이면 모두 같은 인스턴스임  
> 설정으로 싱글톤이 아니게 설정할 수 있지만, 특별한 경우를 제외하면 대부분 싱글톤을 사용함  
  
[목차](#목차)

<br>  

## 자바 코드로 직접 스프링 빈 등록하기  
회원 서비스와 회원 리포지토리의 `@Service`, `@Repository`, `@Autowired` 애노테이션을 제거하고 진행  
<br>  

```java
package hello.hellospring;

import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringConfig {

    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
}
```  
<br>  

여기서는 향후 메모리 리포지토리를 다른 리포지토리로 변경할 예정이므로,  
컴포넌트 스캔 방식 대신에 자바 코드로 스프링 빈을 설정함  
<br>  

> 참고 : XML로 설정하는 방식도 있지만 최근에는 잘 사용하지 않으므로 생략함  
<br>  

> 참고 : DI에는 필드 주입, setter 주입, 생성자 주입 이렇게 3가지 방법이 있음 (위 방법은 생성자 주입)  
> 의존 관계가 실행 중에 동적으로 변하는 경우는 거의 없으므로 생성자 주입을 권장함  
<br>  

> 참고 : 실무에서는 주로 정형화된 컨트롤러, 서비스, 리포지토리 같은 코드는 컴포넌트 스캔을 사용함.  
> 그리고 정형화 되지 않거나, 상황에 따라 구현 클래스를 변경해야 하면 설정을 통해 스프링 빈으로 등록함.  
<br>  

> 주의 : `@Autowired`를 통한 DI는 `helloController`, `memberService`등과 같이 스프링이 관리하는 객체에서만 동작함.  
> 스프링 빈으로 등록하지 않고 내가 직접 생성한 객체에서는 동작하지 않음  
  
[목차](#목차)
