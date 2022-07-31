# Lambda

병렬 처리와 이벤트 지향 프로그래밍에 적합한 함수적 프로그래밍 부각

자바 8부터 익명 함수를 생성하기 위한 식인 람다식을 지원한다.



## 람다식 기본 문법

- ( 매개변수 ) -> { 실행문 } 
- 매개변수 타입은 런타임 시간에 인식되므로 생략 가능

- 매개변수가 하나라면 괄호 생략 가능

- return만 있는 실행문이라면 중괄호와 return을 생략

  ```java
  (x, y) -> x + y
  ```



## 타겟 타입과 함수적 인터페이스

- 자바는 클래스의 멤버로만 메소드를 선언할 수 있기 때문에 인터페이스의 익명 구현 객체를 생성

- 타겟 타입 (target type)
  - 람다식이 대입될 인터페이스
  - 하나의 추상 메소드만 선언되어 있어야함



- `@FunctionalInterface` : 두 개 이상의 추상 메소드가 선언되지 않도록 컴파일러가 체킹 (필수 x)

  ```java
  @FunctionalInterface
  public interface MyFunctionalInterface {
    public int method(int x, int y);
  }
  ```

  ```java
  MyFunctionalInterface fi = (x, y) -> {
    return x + y;
  };
  System.out.println(fi.method(2, 5));
  ```



## 클래스 멤버와 로컬 변수 사용

- 람다식에서 `this`는 내부적으로 생성되는 익명 객체의 참조가 아니라 **람다식을 실행한 객체**
- 