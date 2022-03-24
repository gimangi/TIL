

# Dependency Injection (의존성 주입)



## IoC를 구현하는 방법

1. ##### DL (Dependency Lookup) : 의존성 검색

   - 컨테이너 제공하는 API를 이용해 저장소에서 빈을 Lookup한다.
   - ex) applicationContext.getBean

2. ##### DI (Dependency Injection) : 의존성 주입

   - 각 객체간의 의존성을 컨테이너가 자동으로 연결해준다

   1. ##### Setter Injection

      ```java
      @Component
      public class Foo {
        
        private Bar bar;
        
        @Autowired
        public void setBar(Bar bar) {
          this.bar = bar;
        }
      }
      ```

   2. ##### Constructor Injection

      ```java
      @Component
      public class Foo {
        
        private Bar bar;
        
        public Foo(Bar bar) {		// spring 4.3~ @autowired 필요 x
          this.bar = bar;
        }
      }
      ```

   3. ##### Field Injection

      ```java
      @Component
      public class Foo {
        
        @Autowired
        private Bar bar;
        
        public void doSomething() {
          bar.something();
        }
      }
      ```



Spring 4.3부터 특정 클래스에 생성자가 유일하고, 생성자로 주입받는 매개변수들이 Bean으로 등록되어 있다면 Bean을 자동으로 주입하도록 기능이 추가되어 있다. (@Autowired 안해줘도 bean 등록)

만약 이전 버전이라면 constructor 또는 setter에 @Autowired를 해줘야 한다.



- 필수적으로 사용해야 하는 매개변수 없이는 사용할 수 없도록 강제하기 위해서 **생성자**에서 @Autowired를 사용하도록 권장한다. 

- ##### 순환 참조

  - A -> B, B -> A 를 참조할 때 생성자 인젝션을 사용하면 주입 불가
  - 이런 경우에는 인스턴스를 먼저 만든 다음에 필드 인젝션 or 세터 인젝션을 하여 해결할 수 있다.



## Reference

- [예제로 배우는 스프링 입문 (개정판)](https://www.inflearn.com/course/spring_revised_edition/dashboard)
- [[Spring] Spring IoC와 DI](https://gangnam-americano.tistory.com/60)