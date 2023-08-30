### 회원 관리 예제 - 백엔드 개발  

#### 목차  
> [비즈니스 요구사항 정리](#비즈니스-요구사항-정리)  
> [회원 도메인과 리포지토리 만들기](#회원-도메인과-리포지토리-만들기)  
> [회원 리포지토리 테스트 케이스 작성](#회원-리포지토리-테스트-케이스-작성)  
> [회원 서비스 개발](#회원-서비스-개발)  
> [회원 서비스 테스트](#회원-서비스-테스트)  

<br>

## 비즈니스 요구사항 정리  
- 데이터: 회원ID, 이름
- 기능: 회원 등록, 조회
- 아직 데이터 저장소가 선정되지 않음(가상의 시나리오
<br>  

- 일반적인 웹 애플리케이션 계층 구조  
  <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/07795929-3e49-47bc-a7f8-aa7a7c6db28e" width="70%">  
  - 컨트롤러 : 웹 MVC의 컨트롤러 역할
  - 서비스 : 핵심 비즈니스 로직 구현
  - 리포지토리 : 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
  - 도메인 : 비즈니스 도메인 객체, 예) 회원, 주문, 쿠폰 등등 주로 데이터베이스에 저장하고 관리됨
<br>  

- 클래스 의존관계  
  <img src="https://github.com/lpromotion/Inflearn_Spring/assets/88132500/99553c4a-5a64-4e08-b5c2-eba1426be94f" width="60%">  
  - 아직 데이터 저장소가 선정되지 않아서, 우선 인터페이스로 구현 클래스를 변경할 수 있도록 설계
  - 데이터 저장소는 RDB, NoSQL 등 다양한 저장소를 고민중인 상황으로 가정
  - 개발을 진행하기 위해서 초기 개발 단계에서는 구현체로 가벼운 메모리 기반의 데이터 저장소 사용  
  
[목차](#목차)

<br>  

## 회원 도메인과 리포지토리 만들기  
- 회원 객체
  ```java
  package hello.hellospring.domain;

  public class Member {
  
      private Long id; // 식별자 (임의의 값)
      private String name; // 이름
  
      public Long getId() {
          return id;
      }
  
      public void setId(Long id) {
          this.id = id;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  }
  ```
<br>  

- 회원 리포지토리 인터페이스
  ```java
  package hello.hellospring.repository;

  import hello.hellospring.domain.Member;
  
  import java.util.List;
  import java.util.Optional;
  
  public interface MemberRepository {
      Member save(Member member);
      // Optional : null을 처리하는 방법 중 하나
      Optional<Member> findById(Long id);
      Optional<Member> findByName(String name);
      List<Member> findAll(); // 지금까지 저장된 모든 회원 리스트 반환
  }
  ```
<br>  

- 회원 리포지토리 메모리 구현체
  ```java
  package hello.hellospring.repository;
  
  import hello.hellospring.domain.Member;
  
  import java.util.*;
  
  /**
  * 동시성 문제가 고려되어 있지 않음, 실무에서는 ConcurrentHashMap, AtomicLong 사용 고려
  **/
  public class MemoryMemberRepository implements  MemberRepository {
  
      // save 할 때 저장할 곳
      private static Map<Long, Member> store = new HashMap<>();
      
      // 키값을 생성
      private static long sequence = 0L;
  
      @Override
      public Member save(Member member) {
          member.setId(++sequence); // id값 세팅
          store.put(member.getId(), member); // store에 저장
          return member;
      }
  
      @Override
      public Optional<Member> findById(Long id) {
          return Optional.ofNullable(store.get(id)); // 결과가 없을 경우를 대비해 Optional으로 감쌈
      }
  
      @Override
      public Optional<Member> findByName(String name) {
          // store에서 루프를 돌리며 member.getName()이 파라미터로 넘어온 name인지 확인
          // 같을 경우만 필터링됨. 찾으면 반환
          // 결과는 Optional로 반환됨
          return store.values().stream()
                  .filter(member -> member.getName().equals(name))
                  .findAny();
      }
  
      @Override
      public List<Member> findAll() {
          return new ArrayList<>(store.values());
      }
  }
  ```

[목차](#목차)

<br>  

## 회원 리포지토리 테스트 케이스 작성  
개발한 기능을 실행해서 테스트 할 때 자바의 main 메서드를 통해서 실행하거나,  
웹 애플리케이션의 컨트롤러를 통해서 해당 기능을 실행한다.  
이러한 방법은 준비하고 실행하는데 오래 걸리고, 반복 실행하기 어렵고  
여러 테스트를 한번에 실행하기 어렵다는 단점이 있다.  
자바는 JUnit이라는 프레임워크로 테스트를 실행해서 이러한 문제를 해결한다.  
<br>  

- 회원 리포지토리 메모리 구현체 테스트  
  `src/test/java` 하위 폴더에 생성함
  ```java
  package hello.hellospring.repository;
  
  import hello.hellospring.domain.Member;
  import org.assertj.core.api.Assertions;
  import org.junit.jupiter.api.AfterEach;
  import org.junit.jupiter.api.Test;
  
  import java.util.List;
  import java.util.Optional;
  
  import static org.assertj.core.api.Assertions.*;
  
  class MemoryMemberRepositoryTest {
  
      MemoryMemberRepository repository = new MemoryMemberRepository();
  
      // 테스트를 서로 순서와 관계없이 설계가 되어야 함
      // 하나의 테스트가 끝날 때 마다 repository(저장소)를 지워줘야 함
      @AfterEach
      public void afterEach() {
          repository.clearStore();
      }
  
      @Test
      public void save() {
          // given
          Member member = new Member();
          member.setName("spring");
  
          // when
          repository.save(member);
  
          // then
          // 반환 타입이 Optional => get으로 꺼낼 수 있음
          // 위 방법이 좋은 방법은 아님 (테스트용)
          Member result = repository.findById(member.getId()).get(); // 검증
  
          // org.junit.jupiter.api
          // Assertions.assertEquals(member, result); // 다르면 오류 발생
  
          // org.assertj.core.api (편하게 쓸 수 있게 해줌)
          assertThat(member).isEqualTo(result); // static import 함 (Assertions. 생략됨)
      }
  
      @Test
      public void findByName() {
          // given
          Member member1 = new Member();
          member1.setName("spring1");
          repository.save(member1);
  
          Member member2 = new Member();
          member2.setName("spring2");
          repository.save(member2);
  
          // when
          Member result = repository.findByName("spring1").get();
  
          // then
          assertThat(result).isEqualTo(member1);
          // 다르면 오류 발생
      }
  
      @Test
      public void findAll(){
          // given
          Member member1 = new Member();
          member1.setName("spring1");
          repository.save(member1);
  
          Member member2 = new Member();
          member2.setName("spring2");
          repository.save(member2);
  
          //when
          List<Member> result = repository.findAll();
  
          //then
          assertThat(result.size()).isEqualTo(2);
      }
  }
  ```
<br>  

- 회원 레포지토리 메모리 구현체 (추가)
  ```java
  public void clearStore() {
      store.clear();
  }
  ```
<br>  

- `@AfterEach` : 한번에 여러 테스트를 실행하면 메모리 DB에 직전 테스트의 결과가 남을 수 있다.  
  이렇게 되면 다음 이전 테스트 때문에 다음 테스트가 실패할 가능성이 있다.  
  `@AfterEach`를 사용하면 각 테스트가 종료될 때 마다 이 기능을 실행한다.  
  여기서는 메모리 DB에 저장된 데이터를 삭제한다.  
- 테스트는 각각 독립적으로 실행되어야 한다. 테스트 순서에 의존관계가 있는 것은 좋은 테스트가 아니다.  
<br>

- 테스트 주도 개발(TDD) : 테스트 클래스를 먼저 작성한 다음, 구현 클래스를 작성하는 방식.  
  
[목차](#목차)

<br>  

## 회원 서비스 개발  
- 코드를 메서드로 빼기 : 옵션 > 리펙터링 > 메서드 추출
- (단축키) Ctrl + Alt + v
<br>

```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;

import java.util.List;
import java.util.Optional;

public class MemberService {

    // 회원 리포지토리
    private final MemberRepository memberRepository = new MemoryMemberRepository();

    /**
     * 회원가입
     */
    public Long join(Member member) {
        validateDuplicateMember(member); // 중복 회원 검증

        memberRepository.save(member);
        return member.getId();
    }

    private void validateDuplicateMember(Member member) {
        memberRepository.findByName(member.getName())
                        .ifPresent(m -> { // Optional 이기 때문에 사용 가능함
                            throw new IllegalStateException("이미 존재하는 회원입니다.");
                        });
    }

    /**
     * 전체 회원 조회
     */
    public List<Member> findMembers() {
        return memberRepository.findAll();
    }

    public Optional<Member> findOne(Long memberId) {
        return memberRepository.findById(memberId);
    }
}
```  

[목차](#목차)

<br>  

## 회원 서비스 테스트  
- (단축키) Ctrl + Shift + t : 테스트 자동 생성

- 기존에는 회원 서비스가 메모리 회원 리포지토리를 직접 생성하게 함
  ```java
  public class MemberService {

    private final MemberRepository memberRepository = new MemoryMemberRepository();

  }
  ```
<br>  

- 회원 리포지토리의 코드가 회원 서비스 코드를 DI 가능하게 변경한다.
  ```java
  public class MemberService {
  
      // 회원 리포지토리
      private final MemberRepository memberRepository;
  
      // 생성 > Constructor(생성자)
      // MemberRepository를 외부에서 넣어주도록 함
      public MemberService(MemberRepository memberRepository) {
          this.memberRepository = memberRepository;
      }
      ...
  }
  ```
<br>  

- 회원 서비스 테스트
  ```java
  package hello.hellospring.service;
  
  import hello.hellospring.domain.Member;
  import hello.hellospring.repository.MemoryMemberRepository;
  import org.junit.jupiter.api.AfterEach;
  import org.junit.jupiter.api.BeforeEach;
  import org.junit.jupiter.api.Test;
  
  import static org.assertj.core.api.Assertions.*;
  import static org.junit.jupiter.api.Assertions.*;
  
  class MemberServiceTest {
  
      MemberService memberService;
      MemoryMemberRepository memberRepository;
  
      // 테스트 실행할 때마다 각각 생성을 해줌
      // 테스트 독립 실행을 위해 사용
      // 같은 MemoryMemberRepository를 사용할 수 있음
      @BeforeEach
      public void beforeEach() {
          memberRepository = new MemoryMemberRepository();
          memberService = new MemberService(memberRepository);
      }
  
      @AfterEach
      public void afterEach() {
          memberRepository.clearStore();
      }
  
      @Test
      void 회원가입() { // 테스트는 한글로 적을 수 있음
          // given
          Member member = new Member();
          member.setName("spring");
  
          // when
          Long saveId = memberService.join(member);
  
          // then
          Member findMember = memberService.findOne(saveId).get();
          assertThat(member.getName()).isEqualTo(findMember.getName());
      }
  
      @Test
      public void 중복_회원_예외() {
          // given
          Member member1= new Member();
          member1.setName("spring");
  
          Member member2= new Member();
          member2.setName("spring");
  
          // when
          memberService.join(member1);
          IllegalStateException e = assertThrows(IllegalStateException.class, () -> memberService.join(member2));
  
          assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");
  
  /*        try {
              memberService.join(member2);
              fail();
          } catch (IllegalStateException e) {
              assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.123");
          }*/
  
          // then
      }
  
      @Test
      void findMembers() {
      }
  
      @Test
      void findOne() {
      }
  }
  ```
  - `@BeforeEach` : 각 테스트 실행 전에 호출된다. 테스트가 서로 영향이 없도록 항상 새로운 객체를 생성하고, 의존관계도 새로 맺어준다.  

[목차](#목차)  

