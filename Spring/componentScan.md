# 컴포넌트 스캔

## 컴포넌트 스캔과 의존관계 자동 주입 시작하기

* 지금까지 스프링 빈을 등록할 때는 Java 코드의 `@Bean`이나 XML의 `<bean>` 등을 통해서 설정 정보에 직접 등록할 스프링 빈을 나열했다.
* 등록해야 할 스프링 빈이 수십, 수백개가 되면 일일이 등록하기도 번거롭고, 설정 정보도 커지고, 누락하는 문제도 발생한다.
* 그래서 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 **컴포넌트 스캔**이라는 기능을 제공한다.
* 또한 의존관계도 자동으로 주입하는 `@Autowired` 라는 기능도 제공한다.
  * `@Autowired` 를 사용하면 생성자에서 여러 의존관계도 한번에 주입받을 수 있다. 
* 컴포넌트 스캔을 사용하려면 먼저 `@ComponentScan` 을 설정 정보에 붙여주면 된다.
* 기존의 AppConfig와는 다르게 `@Bean`으로 등록한 클래스가 하나도 없다!
* 컴포넌트 스캔은 이름 그대로 `@Component` 애노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록하는 것이다.
* 이전에 AppConfig에서는 `@Bean` 으로 직접 설정 정보를 작성했고, 의존관계도 직접 명시했다. 이제는 이런 설정 정보 자체가 없기 때문에, 의존관계 주입도 이 클래스 안에서 해결해야 한다.

<br>

## 컴포넌트 스캔 과정

1. @ComponentScan

* `@ComponentScan` 은 `@Component`가 붙은 모든 클래스를 스프링 빈으로 등록한다.
* 이때 스프링 빈의 기본 이름은 클래스명을 사용하되 맨 앞글자만 소문자를 사용한다.
  * 빈 이름 기본 전략: MemberServiceImpl 클래스일 경우, memberServiceImp이 빈 이름으로 사용된다.
  * 빈 이름 직접 지정: 만약 스프링 빈의 이름을 직접 지정하고 싶으면 `@Component("memberService2")` 이런식으로 이름을 부여하면 된다.

2. @Autowired 의존관계 자동 주입

* 생성자에 `@Autowired`를 지정하면, 스프링 컨테이너가 자동으로 해당 스프링 빈을 찾아서 주입한다.
* 이때 기본 조회 전략은 타입이 같은 빈을 찾아서 주입하는 것이다.
  *  `getBean(클래스명)` 와 동일하다고 이해하면 된다.
 
<br>

## 탐색 위치와 기본 스캔 대상

### 탐색할 패키지의 시작 위치 지정

* 모든 클래스를 컴포넌트 스캔하면 시간이 오래 걸리기 때문에 꼭 필요한 위치부터 탐색하도록 시작 위치를 지정할 수 있다.

```java
@ComponentScan(
 basePackages = "hello.core",
}
```

* `basePackages` : 탐색할 패키지의 시작 위치를 지정한다. 이 패키지를 포함해서 하위 패키지를 모두 탐색한다.
  * `basePackages = {"hello.core", "hello.service"} ` 이렇게 여러 시작 위치를 지정할 수도 있다.
* `basePackageClasses` : 지정한 클래스의 패키지를 탐색 시작 위치로 지정한다.
* 만약 지정하지 않으면 `@ComponentScan` 이 붙은 설정 정보 클래스의 패키지가 시작 위치가 된다.

**권장하는 방법**

패키지 위치를 지정하지 않고, 설정 정보 클래스의 위치를 프로젝트 최상단에 두는 것이 권장된다.
최근 스프링 부트도 이 방법을 기본으로 제공한다.

예를 들어, 프로젝트가 다음과 같이 구조가 되어 있으면
```
com.hello
com.hello.serivce
com.hello.repository
````
프로젝트 시작 루트인 `com.hello`에 `AppConfig` 같은 메인 설정 정보를 두고, `@ComponentScan` 애노테이션을 붙이고, `basePackages` 지정은 생략한다.
이렇게 하면 `com.hello` 를 포함한 하위는 모두 자동으로 컴포넌트 스캔의 대상이 된다. 
그리고 프로젝트 메인 설정 정보는 프로젝트를 대표하는 정보이기 때문에 프로젝트 시작 루트 위치에 두는 것이 좋다 생각한다.
참고로 스프링 부트를 사용하면 스프링 부트의 대표 시작 정보인 `@SpringBootApplication` 를 이 프로젝트 시작 루트 위치에 두는 것이 관례이다. 
(그리고 이 설정안에 바로 `@ComponentScan` 이 들어있다!)

<br>

## 컴포넌트 스캔 기본 대상

컴포넌트 스캔은 `@Component` 뿐만 아니라 다음과 내용도 추가로 대상에 포함한다.

* `@Component` : 컴포넌트 스캔에서 사용
* `@Controller` : 스프링 MVC 컨트롤러에서 사용
* `@Service` : 스프링 비즈니스 로직에서 사용
* `@Repository` : 스프링 데이터 접근 계층에서 사용
* `@Configuration` : 스프링 설정 정보에서 사용

해당 클래스의 소스 코드를 보면 `@Component` 를 포함하고 있는 것을 알 수 있다.

```java
@Component
public @interface Controller {
}

@Component
public @interface Service {
}

@Component
public @interface Configuration {
}
```

* 참고: 사실 애노테이션에는 상속관계라는 것이 없다. 그래서 이렇게 애노테이션이 특정 애노테이션을 들고 있는 것을 인식할 수 있는 것은 자바 언어가 지원하는 기능은 아니고, **스프링이 지원**하는 기능이다.

컴포넌트 스캔의 용도 뿐만 아니라 다음 애노테이션이 있으면 스프링은 부가 기능을 수행한다.

* `@Controller` : 스프링 MVC 컨트롤러로 인식
* `@Repository` : 스프링 데이터 접근 계층으로 인식하고, 데이터 계층의 예외를 스프링 예외로 변환해준다.
* `@Configuration` : 앞서 보았듯이 스프링 설정 정보로 인식하고, 스프링 빈이 싱글톤을 유지하도록 추가 처리를 한다.
* `@Service` : `@Service` 는 특별한 처리를 하지 않는다. 대신 개발자들이 핵심 비즈니스 로직이 여기에 있겠구나 라고 비즈니스 계층을 인식하는데 도움이 된다.

<br>

## 필터

* `includeFilters` : 컴포넌트 스캔 대상을 추가로 지정한다.
* `excludeFilters` : 컴포넌트 스캔에서 제외할 대상을 지정한다.

<br>

**컴포넌트 스캔 대상에 추가할 애노테이션**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyIncludeComponent {
}
```

**컴포넌트 스캔 대상에서 제외할 애노테이션**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyExcludeComponent {
}
```

**컴포넌트 스캔 대상에 추가할 클래스**

```java
@MyIncludeComponent
public class BeanA {
}
```

**컴포넌트 스캔 대상에서 제외할 클래스**

```java
@MyExcludeComponent
public class BeanB {
}
```

<br>

```java
@ComponentScan(
 includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
 excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
)
```

* `includeFilters` 에 `MyIncludeComponent` 애노테이션을 추가해서 BeanA가 스프링 빈에 등록된다.
* `excludeFilters` 에 `MyExcludeComponent` 애노테이션을 추가해서 BeanB는 스프링 빈에 등록되지 않는다.

<br>

## FilterType 옵션

FilterType은 2가지 주요 옵션이 있다.

* `ANNOTATION`: 기본값, 애노테이션을 인식해서 동작한다.
  * ex) `org.example.SomeAnnotation`
* `ASSIGNABLE_TYPE`: 지정한 타입과 자식 타입을 인식해서 동작한다.
  * ex) `org.example.SomeClass`

참고: `@Component` 면 충분하기 때문에, `includeFilters` 를 사용할 일은 거의 없다. `excludeFilters` 는
여러가지 이유로 간혹 사용할 때가 있지만 많지는 않다.
특히 최근 스프링 부트는 컴포넌트 스캔을 기본으로 제공하는데, 개인적으로는 옵션을 변경하면서 사용하기 보다는 스프링의 기본 설정에 최대한 맞추어 사용하는 것이 권장된다.

<br>

## 중복 등록과 충돌

컴포넌트 스캔에서 같은 빈 이름을 등록하면 어떻게 될까?
다음 두가지 상황이 있다.

1. 자동 빈 등록 vs 자동 빈 등록
2. 수동 빈 등록 vs 자동 빈 등록

* 빈을 수동, 자동 등록할 때, 그 이름이 같은 경우 스프링은 오류를 발생시킨다.
  * `ConflictingBeanDefinitionException` 예외 발생