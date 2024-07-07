# 빈 생명주기 콜백

## 빈 생명주기 콜백 시작

데이터베이스 커넥션 풀이나, 네트워크 소켓처럼 애플리케이션 시작 시점에 필요한 연결을 미리 해두고, 애플리케이션
종료 시점에 연결을 모두 종료하는 작업을 진행하려면, 객체의 초기화와 종료 작업이 필요하다.


스프링을 통해 이러한 초기화 작업과 종료 작업을 어떻게 진행하는지 예제로 알아보겠다.
간단하게 외부 네트워크에 미리 연결하는 객체를 하나 생성한다고 가정해보자. 실제로 네트워크에 연결하는 것은 아니고, 단순히 문자만 출력하도록 했다. 
이 `NetworkClient` 는 애플리케이션 시작 시점에 `connect()` 를 호출해서 연결을 맺어두어야 하고, 애플리케이션이 종료되면 `disConnect()` 를 호출해서 연결을 끊어야 한다.

**예제 코드, 테스트 하위에 생성**

```java
package hello.core.lifecycle;

public class NetworkClient {

  private String url;

  public NetworkClient() {
    System.out.println("생성자 호출, url = " + url);
    connect();
    call("초기화 연결 메시지");
  }

  public void setUrl(String url) {
    this.url = url;
  }

  //서비스 시작시 호출
  public void connect() {
    System.out.println("connect: " + url);
  }

  public void call(String message) {
    System.out.println("call: " + url + " message = " + message);
  }

  //서비스 종료시 호출
  public void disconnect() {
    System.out.println("close: " + url);
  }
}
```

**스프링 환경설정과 실행**

```java
package hello.core.lifecycle;

import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

public class BeanLifeCycleTest {

  @Test
  public void lifeCycleTest() {
    ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
    NetworkClient client = ac.getBean(NetworkClient.class);
    ac.close(); //스프링 컨테이너를 종료, ConfigurableApplicationContext 필요
  }

  @Configuration
  static class LifeCycleConfig {

    @Bean
    public NetworkClient networkClient() {
      NetworkClient networkClient = new NetworkClient();
      networkClient.setUrl("http://hello-spring.dev");
      return networkClient;
    }
  }
}
```

실행해보면 다음과 같은 예상치 못한 결과가 나온다.

```
생성자 호출, url = null
connect: null
call: null message = 초기화 연결 메시지
```

생성자 부분을 보면 url 정보 없이 connect가 호출되는 것을 확인할 수 있다.
객체를 생성하는 단계에는 url이 없고, **객체를 생성한 다음**에 외부에서 수정자 주입을 통해서 `setUrl()` 이 호출되어야 url이 존재하게 된다.

스프링 빈은 간단하게 다음과 같은 라이프사이클을 가진다.

**객체 생성** -> **의존관계 주입**

스프링 빈은 객체를 생성하고, 의존관계 주입이 다 끝난 다음에야 필요한 데이터를 사용할 수 있는 준비가 완료된다.
따라서 초기화 작업은 의존관계 주입이 모두 완료되고 난 다음에 호출해야 한다. 
그런데 개발자가 의존관계 주입이 모두 완료된 시점을 어떻게 알 수 있을까?
**스프링은 의존관계 주입이 완료되면 스프링 빈에게 콜백 메서드를 통해서 초기화 시점을 알려주는 다양한 기능을 제공**한다. 
또한 **스프링은 스프링 컨테이너가 종료되기 직전에 소멸 콜백**을 준다. 따라서 안전하게 종료 작업을 진행할 수 있다.

<br>

### **스프링 빈의 이벤트 라이프사이클**

**스프링 컨테이너 생성** -> **스프링 빈 생성** -> **의존관계 주입** -> **초기화 콜백** -> **사용** -> **소멸전 콜백** -> **스프링 종료**

