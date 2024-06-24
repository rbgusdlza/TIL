# 의존관계 자동 주입

## 다양한 의존관계 주입 방법

의존관계 주입은 크게 4가지 방법이 있다.

* 생성자 주입
* 수정자 주입(setter 주입)
* 필드 주입
* 일반 메서드 주입

<br>

### 1. 생성자 주입

* 생성자를 통해서 의존 관계를 주입 받는 방법이다.
* 특징
  * 생성자 호출시점에 딱 1번만 호출되는 것이 보장된다.
  * **불변, 필수** 의존관계에 사용
  * **생성자가 딱 1개만 있으면 @Autowired를 생략해도 자동 주입 된다.** (스프링 빈에만 해당)

```java
@Component
public class MemberServiceImpl implements MemberService {
 private final MemberRepository memberRepository;
 private final TeamRepository teamRepository;

 @Autowired
 public MemberServiceImpl(MemberRepository memberRepository, TeamRepository teamRepository) {
  this.memberRepository = memberRepository;
  this.teamRepository = teamRepository;
 }
}
```

 <br>

### 2. 수정자 주입(setter 주입)

* `setter`라 불리는 필드의 값을 변경하는 수정자 메서드를 통해서 의존관계를 주입하는 방법이다.
* 특징
  * **선택, 변경** 가능성이 있는 의존관계에 사용
  * 자바빈 프로퍼티 규약의 수정자 메서드 방식을 사용하는 방법이다.

```java
@Component
public class MemberServiceImpl implements MemberService {
 private MemberRepository memberRepository;
 private TeamRepository teamRepository;

 @Autowired
 public void setMemberRepository(MemberRepository memberRepository) {
  this.memberRepository = memberRepository;
 }

 @Autowired
 public void setTeamRepository(TeamRepository teamRepository) {
  this.teamRepository = teamRepository;
 }
}
```

 <br>

 ### 3. 필드 주입

 * 이름 그대로 필드에 바로 주입하는 방법이다.
 * 특징
   * 코드가 간결하다는 장점이 있지만, 외부에서 변경이 불가능해서 테스트 하기 힘들다는 치명적인 단점이 있다.
   * DI 프레임워크가 없으면 아무것도 할 수 없다.
   * 애플리케이션의 실제 코드와 관계 없는 테스트 코드나 스프링 설정을 목적으로 하는 `@Configuration` 같은 곳에서만 특별한 용도로 사용
  
```java
@Component
public class MemberServiceImpl implements MemberService {

 @Autowired
 private final MemberRepository memberRepository;
 @Autowired
 private final TeamRepository teamRepository;
}
```

 <br>

 ### 4. 일반 메서드 주입

 * 일반 메서드를 통해서 주입 받을 수 있다.
 * 특징
   * 한번에 여러 필드를 주입 받을 수 있다.
   * 일반적으로 잘 사용하지 않는다.

 ```java
@Component
public class MemberServiceImpl implements MemberService {

 private final MemberRepository memberRepository;
 private final TeamRepository teamRepository;

 @Autowired
 public void init(MemberRepository memberRepository, TeamRepository teamRepository) {
  this.memberRepository = memberRepository;
  this.teamRepository = teamRepository;
 }
}
```

참고: 의존관계 자동 주입은 스프링 컨테이너가 관리하는 스프링 빈이어야 동작한다.
스프링 빈이 아닌 클래스에서 `@Autowired` 코드를 적용해도 아무 기능도 동작하지 않는다.

<br>

## 옵션 처리

주입할 스프링 빈이 없어도 동작해야 할 때가 있다.
그런데 `@Autowired` 만 사용하면 `required` 옵션의 기본값이 `true` 로 되어 있어서 자동 주입 대상이 없으면 오류가 발생한다.

```java
@Target({ElementType.CONSTRUCTOR, ElementType.METHOD, ElementType.PARAMETER, ElementType.FIELD, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Autowired {
    boolean required() default true;
}
```

자동 주입 대상을 옵션으로 처리하는 방법은 다음과 같다.

* `@Autowired(required = false)` : 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출이 안된다.
* `org.springframework.lang.@Nullable` : 자동 주입할 대상이 없으면 null이 입력된다.
* `Optional<>` : 자동 주입할 대상이 없으면 `Optional.empty` 가 입력된다.

```java
//호출 안됨
@Autowired(required = false)
public void setNoBean1(Member member) {
 System.out.println("setNoBean1 = " + member);
}

//null 호출
@Autowired
public void setNoBean2(@Nullable Member member) {
 System.out.println("setNoBean2 = " + member);
}
//Optional.empty 호출
@Autowired(required = false)
public void setNoBean3(Optional<Member> member) {
 System.out.println("setNoBean3 = " + member);
}
```

출력 결과

```
setNoBean2 = null
setNoBean3 = Optional.empty
```

* 여기서 Member는 스프링 빈이 아니므로 세 가지 케이스에서 자동 주입이 발생하지 않는다.
* `setNoBean1()` 은 `@Autowired(required=false)` 이므로 호출 자체가 안된다.

## 생성자 주입을 선택하자!

생성자 주입을 선택하는 이유는 다음과 같다.

1. **불변**

* 대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료시점까지 의존관계를 변경할 일이 없다. 오히려 대부분의 의존관계는 애플리케이션 종료 전까지 변하면 안된다.(불변해야 한다.)
* 수정자 주입을 사용하면, setXxx 메서드를 `public`으로 열어두어야 한다.
* 누군가 실수로 변경할 수 도 있고, 변경하면 안되는 메서드를 열어두는 것은 좋은 설계 방법이 아니다.
* 생성자 주입은 객체를 생성할 때 딱 1번만 호출되므로 이후에 호출되는 일이 없기 때문에 불변하게 설계할 수 있다.

2. **누락**

프레임워크 없이 순수한 자바 코드를 단위 테스트 하는 경우에 
다음과 같이 수정자 의존관계인 경우,

```java
@Component
public class MemberServiceImpl implements MemberService {
 private MemberRepository memberRepository;
 private TeamRepository teamRepository;

 @Autowired
 public void setMemberRepository(MemberRepository memberRepository) {
  this.memberRepository = memberRepository;
 }

 @Autowired
 public void setTeamRepository(TeamRepository teamRepository) {
  this.teamRepository = teamRepository;
 }
}
```

* `@Autowired` 가 프레임워크 안에서 동작할 때는 의존관계가 없으면 오류가 발생하지만, 지금은 프레임워크 없이 순수한 자바 코드로만 단위 테스트를 수행하고 있다.

```java
@Test
void createMember() {
 MemberServiceImpl memberService = new MemberServiceImpl();
 memberService.createMember("kim", "teamA");
}
```

* 이렇게 테스트를 수행하면 실행은 되지만 실행 결과는 NPE(Null Point Exception)이 발생하는데, memberRepository, teamRepository 모두 의존관계 주입이 누락되었기 때문이다.
* 생성자 주입을 사용하면 다음처럼 주입 데이터를 누락 했을 때 **컴파일 오류**가 발생한다. 그리고 IDE에서 바로 어떤 값을 필수로 주입해야 하는지 알 수 있다.

3. **final 키워드**

생성자 주입을 사용하면 필드에 `final` 키워드를 사용할 수 있다. 그래서 생성자에서 혹시라도 값이 설정되지 않는 오류를 컴파일 시점에 막아준다.

```java
@Component
public class MemberServiceImpl implements MemberService {
 private final MemberRepository memberRepository;
 private final TeamRepository teamRepository;

 @Autowired
 public MemberServiceImpl(MemberRepository memberRepository, TeamRepository teamRepository) {
  this.memberRepository = memberRepository;
 }
}
```

* 필수 필드인 `teamRepository` 에 값을 설정해야 하는데, 이 부분이 누락되었다. 자바는 컴파일 시점에 다음 오류를 발생시킨다.
* `java: variable discountPolicy might not have been initialized`
* **컴파일 오류는 세상에서 가장 빠르고, 좋은 오류다!**

참고: 수정자 주입을 포함한 나머지 주입 방식은 모두 생성자 이후에 호출되므로, 필드에 `final` 키워드를 사용할 수 없다. 오직 생성자 주입 방식만 `final` 키워드를 사용할 수 있다.
