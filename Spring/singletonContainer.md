# 싱글톤 컨테이너

## 웹 애플리케이션과 싱글톤

<img src="./img/spring_basic_4.png">

* 스프링은 태생이 기업용 온라인 서비스 기술을 지원하기 위해 탄생했다.
* 대부분의 스프링 애플리케이션은 웹 애플리케이션이다.
* 웹 애플리케이션은 보통 여러 고객이 동시에 요청을 한다.
* 고객이 요청할 때마다 객체가 생성될 경우, 고객 트래픽이 초당 100이 나오면 초당 100개 객체가 생성되고 소멸되므로 메모리 낭비가 심하다.
* 해결방안은 해당 객체가 딱 1개만 생성되고, 공유하도록 설계하면 된다. → 싱글톤 패턴

<br>

## 싱글톤 패턴

* 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴이다.
* 싱글톤 패턴을 구현하기 위해서는 객체 인스턴스를 2개 이상 생성하지 못하도록 막아야 한다.
  * `private` 생성자를 사용해서 외부에서 임의로 `new` 키워드를 사용하지 못하도록 막아야 한다.

```java
public class SingletonService {

  //1. static 영역에 객체를 딱 1개만 생성해둔다.
  private static final SingletonService instance = new SingletonService();

  //2. public으로 열어서 객체 인스턴스가 필요하면 이 static 메서드를 통해서만 조회하도록 허용한다.
  public static SingletonService getInstance() {
    return instance;
  }
  //3. 생성자를 private으로 선언해서 외부에서 new 키워드를 사용한 객체 생성을 못하게 막는다.
  private SingletonService() {
  }

  public void logic() {
    System.out.println("싱글톤 객체 로직 호출");
  }
}
```

1. static 영역에 객체 instance를 미리 하나 생성해서 올려둔다.
2. 이 객체 인스턴스가 필요하면 오직 `getInstance()` 메서드를 통해서만 조회할 수 있다. 이 메서드를 호출하면 항상 같은 인스턴스를 반환한다.
3. 오직 1개의 객체 인스턴스만 존재해야 하므로, 생성자를 `private`으로 제한해서 혹시라도 외부에서 `new` 키워드로 객체 인스턴스가 생성되는 것을 막는다.

싱글톤 패턴을 적용하면 고객의 요청이 올 때 마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 사용할 수 있다.
하지만, 싱글톤 패턴은 다음과 같은 수 많은 문제점들을 가지고 있다.

**싱글톤 패턴 문제점**
  * 싱글톤 패턴을 구현하는 코드 자체가 많이 들어간다.
  * 객체가 하나만 생성되기 때문에 테스트하기 어렵다.
  * 내부 속성을 변경하거나 초기화 하기 어렵다.
  * 결론적으로 유연성이 떨어진다.

<br>

## 싱글톤 컨테이너

* 스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서, 객체 인스턴스를 싱글톤(1개만 생성)으로 관리한다.
* 지금까지 우리가 학습한 스프링 빈이 바로 싱글톤으로 관리되는 빈이다.
* 스프링 컨테이너는 싱글턴 패턴을 적용하지 않아도, 객체 인스턴스를 싱글톤으로 관리한다.
  * 이전에 설명한 컨테이너 생성 과정을 자세히 보자. 컨테이너는 객체를 하나만 생성해서 관리한다.
* 스프링 컨테이너는 싱글톤 컨테이너 역할을 한다. 이렇게 싱글톤 객체를 생성하고 관리하는 기능을 **싱글톤 레지스트리**라 한다.
* 스프링 컨테이너의 이런 기능 덕분에 싱글톤 패턴의 모든 단점을 해결하면서 객체를 싱글톤으로 유지할 수 있다.
  * 싱글톤 패턴을 위한 지저분한 코드가 들어가지 않아도 된다.
  * DIP, OCP, 테스트, private 생성자로 부터 자유롭게 싱글톤을 사용할 수 있다.

 <br>

**싱글톤 컨테이너 적용 후**

<img src="./img/spring_basic_5.png">

* 스프링 컨테이너 덕분에 고객의 요청이 올 때 마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 재사용할 수 있다.

<br>

## 싱글톤 방식의 주의점

* 객체 인스턴스를 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지(stateful)하게 설계하면 안된다.
* 무상태(stateless)로 설계해야 한다!
  * 특정 클라이언트에 의존적인 필드가 있으면 안된다.
  * 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
  * 가급적 읽기만 가능해야 한다.
  * 필드 대신에 자바에서 공유되지 않는 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.

<br>

## @Configuration과 싱글톤

그런데 이상한점이 있다. 다음 AppConfig 코드를 보자.

```java
@Configuration
public class AppConfig {

 @Bean
 public MemberService memberService() {
  return new MemberServiceImpl(memberRepository());
 }
 
 @Bean
 public OrderService orderService() {
  return new OrderServiceImpl(memberRepository(), discountPolicy());
 }

 @Bean
 public MemberRepository memberRepository() {
  return new MemoryMemberRepository();
 }
 ...
}
```

* memberService 빈을 만드는 코드를 보면 `memberRepository()` 를 호출한다.
  * 이 메서드를 호출하면 `new MemoryMemberRepository()` 를 호출한다.
* orderService 빈을 만드는 코드도 동일하게 `memberRepository()` 를 호출한다.
  * 이 메서드를 호출하면 `new MemoryMemberRepository()` 를 호출한다.

결과적으로 각각 다른 2개의 `MemoryMemberRepository` 가 생성되면서 싱글톤이 깨지는 것 처럼 보인다.
스프링 컨테이너는 이 문제를 어떻게 해결할까?

<br>

## @Configuration과 바이트코드 조작의 마법

스프링 컨테이너는 싱글톤 레지스트리다. 따라서 스프링 빈이 싱글톤이 되도록 보장해주어야 한다. 그런데 스프링이 자바 코드까지 어떻게 하기는 어렵다.
그래서 스프링은 클래스의 바이트코드를 조작하는 라이브러리를 사용한다. 모든 비밀은 `@Configuration`을 적용한 `AppConfig`에 있다.

<br>

`@Configuration`을 적용한 `AppConfig`를 통해 만들어진 빈들은 내가 만든 클래스가 아닌,
스프링이 `CGLIB`라는 바이트코드 조작 라이브러리를 사용해서 `AppConfig` 클래스를 상속받은 임의의 다른 클래스를 만들고,
그 다른 클래스를 통해 등록한 빈이다. 그 임의의 다른 클래스가 바로 싱글톤이 보장되도록 해준다.

<br>

### `@Configuration` 을 적용하지 않고, `@Bean` 만 적용하면 어떻게 될까?

`@Configuration`을 붙이면 바이트코드를 조작하는 `CGLIB` 기술을 사용해서 싱글톤을 보장하지만, 
만약 `@Bean`만 적용하면 어떻게 될까?

<br>

`@Configuration`를 적용하지 않으면 `AppConfig`가 `CGLIB` 기술 없이 순수한 `AppConfig`로 스프링 빈에 등록된다.
즉, 의존관계 주입이 필요해서 메서드를 직접 호출할 때 싱글톤을 보장하지 않는다.
