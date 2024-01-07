### AOP

#### 목차  
> [AOP가 필요한 상황](#AOP-필요한-상황)  
> [AOP 적용](#AOP-적용)  

<br>  

## AOP가 필요한 상황  
- 모든 메소드의 호출 시간을 측정하고 싶다면?
- 공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern)
- 회원 가입 시간, 회원 조회 시간을 측정하고 싶다면?  
<img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/f800b4dd-43e0-4e02-aa2a-d8f0b9ac458e.png" width="50%"> 
 <br>  

- 문제
  - 회원가입, 회원 조회에 시간을 측정하는 기능은 핵심 관심 사항이 아니다.
  - 시간을 측정하는 로직은 공통 관심 사항이다.
  - 시간을 측정하는 로직과 핵심 비즈니스의 로직이 섞여서 유지보수가 어렵다.
  - 시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.
  - 시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다

<br>  

## AOP 적용  
- AOP: Aspect Oriented Programming  
- 공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern) 분리  
<img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/2e538583-725f-4d92-918c-3aebc4f31ef6.png" width="50%">

<br>  


- 시간 측정 AOP 등록
  ```java
  package hello.hellospring.aop;
  import org.aspectj.lang.ProceedingJoinPoint;
  import org.aspectj.lang.annotation.Around;
  import org.aspectj.lang.annotation.Aspect;
  import org.springframework.stereotype.Component;
  
  @Component // 스프링빈 등록
  @Aspect
  public class TimeTraceAop {
  
      @Around("execution(* hello.hellospring..*(..))")
      public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
    
          long start = System.currentTimeMillis();
          System.out.println("START: " + joinPoint.toString());
    
          try {
              return joinPoint.proceed();
          } finally {
              long finish = System.currentTimeMillis();
              long timeMs = finish - start;
              System.out.println("END: " + joinPoint.toString()+ " " + timeMs + "ms");
          }
      }
  }
  ```

<br>  

- 스프링빈 등록  
  ```java
  package hello.hellospring;
  
  @Configuration
  public class SpringConfig {
  
      @Bean
      public TimeTraceAop timeTraceAop(){
          return new TimeTraceAop();
      }
  
  }
  ```

<br>  

<img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/177ba624-9551-4fc5-b723-895913b9cca2.png" width="50%">

이 기술을 통해 어디서 병목이 발생하는 지 알 수 있음.  

- 해결
  - 회원가입, 회원 조회 등 핵심 관심 사항과 시간을 측정하는 공통 관심 사항을 분리한다.
  - 시간을 측정하는 로직을 별도의 공통 로직으로 만들었다.
  - 핵심 관심 사항을 깔끔하게 유지할 수 있다.
  - 변경이 필요하면 이 로직만 변경하면 된다.
  - 원하는 적용 대상을 선택할 수 있다.

<br>  

### 스프링의 AOP 동장 방식 설명  
- AOP 적용 전 의존관계  
  <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/d4680d1b-142c-4129-82b9-4b2f23f4dbef.png" width="50%">  <br>
- AOP 적용 후 의존관계  
  <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/2ecd48de-cfbb-4d4c-ad4f-73b16d933a30.png" width="50%">  <br>
- AOP 적용 전 전체 그림  
  <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/36a0faec-91e7-4247-9c98-0f3753de252f.png" width="50%">  <br>
- AOP 적용 후 전체 그림  
  <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/6a12bfbd-e0fd-4492-94fd-542fb87f253b.png" width="50%">  <br>

- 실제 Proxy가 주입되는지 콘솔에 출력해서 확인하기  
  <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/9d300bcc-a29e-442d-9ff1-ae4766153073.png" width="50%">  <br>  
