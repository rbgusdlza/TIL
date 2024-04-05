## 스프링 컨테이너란?

* 빈(bean)의 생명 주기(life cycle)와 의존성 주입(Dependency Injection)을 담당하는 컨테이너이다.
* 스프링 컨테이너가 관리하는 자바 객체를 ``빈(bean)``이라고 한다. 

```
//스프링 컨테이너 생성
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

* 스프링 프로젝트에서 주로 ``ApplicationContext``가 스프링 컨테이너 역할을 한다.
* ``ApplicationContext``는 인터페이스이다.
* 스프링 컨테이너는 XML을 기반으로 만들 수 있고, 애노테이션 기반의 자바 설정 클래스로 만들 수 있다. 지금 예시에서는 ``AppConfig``라는 애노테이션 기반의 자바 설정 클래스를 이용해 스프링 컨테이너를 만들었다.

<br>

## 스프링 컨테이너의 생성 과정

### 1. 스프링 컨테이너 생성

* 스프링 컨테이너를 생성할 때는 **구성 정보**를 지정해 주어야 한다.
* 여기서는 ``AppConfig.class`` 즉, 설정 클래스 정보를 구성 정보로 지정했다.

### 2. 스프링 빈 등록

* 스프링 컨테이너는 파라미터로 넘어온 설정 클래스 정보를 사용해서 스프링 빈을 등록한다.

### 3. 스프링 의존관계 설정

* 스프링 컨테이너는 구성 정보를 참고해서 의존관계를 주입(DI)한다.

<br>

## 빈 이름 설정