# 싱글톤 컨테이너

## 웹 애플리케이션과 싱글톤

* 스프링은 태생이 기업용 온라인 서비스 기술을 지원하기 위해 탄생했다.
* 대부분의 스프링 애플리케이션은 웹 애플리케이션이다.
* 웹 애플리케이션은 보통 여러 고객이 동시에 요청을 한다.
* 고객이 요청할 때마다 객체가 생성될 경우, 고객 트래픽이 초당 100이 나오면 초당 100개 객체가 생성되고 소멸되므로 메모리 낭비가 심하다.
* 해결방안은 해당 객체가 딱 1개만 생성되고, 공유하도록 설계하면 된다. → 싱글톤 패턴

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
