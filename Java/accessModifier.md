## 접근 제어자란?

* 접근 제어자는 접근 권한을 제어하는 키워드로, 이것을 사용하면 해당 클래스 외부에서 특정 필드나 메서드에 접근하는 것을 허용하거나 제한할 수 있다.

<br>

## 접근 제어자가 필요한 이유

```
어떤 클래스를 설계할 때, 외부에 필요한 기능만 제공하고, 객체 내부적으로 이용하는 기능이나 객체의 속성은 감추도록 해야 한다.
이렇게 설계하지 않으면, 외부 이용자나 이 객체를 사용하는 다른 설계자가 의도치 않은 기능, 속성을 조작해 오류를 발생시킬 가능성이 있다.
이러한 접근 권한에 대한 제약은 접근 제어자를 이용해 구현할 수 있다.
```

<br>

## 캡슐화

* 캡슐화(Encapsulation)는 객체 지향 프로그래밍의 중요한 개념 중 하나다.
* 캡슐화는 데이터와 해당 데이터를 처리하는 메서드를 하나로 묶어서 **외부에서의 접근을 제한**하는 것을 말한다.
* 캡슐화를 통해 데이터의 직접적인 변경을 방지하거나 제한할 수 있다.
* 즉, 캡슐화는 쉽게 이야기해서 속성과 기능을 하나로 묶고, 외부에 꼭 필요한 기능만 노출하고 나머지는 모두 내부로 숨기는 것이다.
* 접근 제어자를 이용해 캡슐화를 안전하게 완성할 수 있다.

<br>

> 어떤 것을 숨기고 어떤 것을 노출해야 할까?

1. 데이터를 숨겨라

객체에는 속성(데이터)과 기능(메서드)이 있다. 캡슐화에서 가장 필수로 숨겨야 하는 것은 속성(데이터)이다.
객체 내부의 데이터를 외부에서 함부로 접근하게 두면, 클래스 안에서 데이터를 다루는 모든 로직을 무시하고 데이터를 변경할 수 있고
이로 인해 캡슐화가 깨진다. **객체의 데이터는 객체가 제공하는 기능인 메서드를 통해서 접근해야 한다.**

2. 기능을 숨겨라

객체의 기능 중에서 외부에서 사용하지 않고 내부에서만 사용하는 기능들이 있다. 이런 기능도 모두 감추는 것이 좋다.
우리가 자동차를 운전하기 위해 자동차가 제공하는 복잡한 엔진 조절 기능, 배기 기능까지 우리가 알 필요는 없다.
우리는 단지 엑셀과 핸들 정도의 기능만 알면 된다. 만약 사용자에게 이런 기능까지 모두 알려준다면, 사용자가 자동차에 대해 너무 많은 것을 알아야 한다.

정리하면, **데이터는 모두 숨기고, 기능은 꼭 필요한 기능만 노출하는 것이 좋은 캡슐화이다.**

<br>

## 접근 제어자의 종류

* `private` : 모든 외부 호출을 막는다.
* `default` (package-private): 같은 패키지안에서 호출은 허용한다.
* `protected` : 같은 패키지안에서 호출은 허용한다. 패키지가 달라도 상속 관계의 호출은 허용한다.
* `public`: 모든 외부 호출을 허용한다.

<br>

## 접근 제어자의 사용 - 클래스 레벨

* 클래스 레벨의 접근 제어자는 `public` , `default` 만 사용할 수 있다.
* `public` 클래스는 반드시 파일명과 이름이 같아야 한다.

<br>

## 접근 제어자의 사용 - 필드, 메서드 레벨

* `private`은 나의 클래스 안으로 속성과 기능을 숨길 때 사용하고, 외부 클래스에서 해당 기능을 호출할 수 없다.
* `default`는 나의 패키지 안으로 속성과 기능을 숨길 때 사용, 외부 패키지에서 해당 기능을 호출할 수 없다.
* `protected`는 상속 관계로 속성과 기능을 숨길 때 사용, 상속 관계가 아닌 곳에서 해당 기능을 호출할 수 없다.
* `public`은 기능을 숨기지 않고 어디서든 호출할 수 있게 공개한다.