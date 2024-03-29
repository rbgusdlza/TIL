## 기본형 vs 참조형

* 기본형(primitive type) : `int` , `long` , `double` , `boolean` 처럼 변수에 사용할 값을 **직접** 넣을 수 있는 데이터 타입을 기본형이라 한다.
* 참조형(reference type): Student student1 , int[] students 와 같이 데이터에 접근하기 위한 **참조(주소)** 를 저장하는 데이터 타입을 참조형이라 한다. 참조형은 객체 또는 배열에 사용된다.

<br>

## 변수 대입
* **자바에서 변수에 값을 대입하는 것은 변수에 들어 있는 값을 복사해서 대입하는 것이다.**
* 따라서 기본형, 참조형 모두 항상 변수에 있는 값을 복사해서 대입한다.
* 메서드 호출에서도 마찬가지로, 매개변수(파라미터) 또한 결국 변수이므로 매개변수에 값을 전달하는 것도 앞서 설명한 내용과 같이 값을 복사해서 전달한다.