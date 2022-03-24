# Bean

IoC 컨테이너에 의해 생성되고 관리되는 객체



## Bean으로 만드는 방법

1. Component Scanning

   - @ComponentScan의 범위 내에서 @Component를 Bean으로 등록한다.
     - SpringBoot의 경우 @SpringBootApplication에 @ComponentScan이 있다

   - @Component
     - @Repository
       - Repository 인터페이스를 구현한 클래스를 찾아서 이를 만들어서 등록하는 과정이 일어난다. 
     - @Service
     - @Controller
     - @Configuration
     - 등등 @Component로 설정되어 있고 직접 어노테이션을 붙여서 등록 가능

2. 직접 Bean으로 등록

   - @Configuration으로 설정 파일을 등록
   - @Bean을 통해 Bean으로 등록할 수 있음

   ```java
   @Configuration
   public class SampleConfig {
   	
     @Bean
     public SampleController sampleController() {
       return new SampleController();
     }
   }
   ```

   

## Bean을 불러오는 방법

1. @Autowired 사용
2. ApplicationContext.getBean 메소드 사용

> @Autowired를 주로 사용 (권장)



## Bean의 Scope

1. ### Singleton Scope

   - 애플리케이션 전반에 걸쳐 해당 빈의 인스턴스를 하나만 생성하여 사용한다.

   - @Component 어노테이션을 통해 빈으로 등록한다.

   ```java
   @Component
   public class Foo {
   }
   
   @Component
   public class Bar {
     
     @Autowired
     private Foo foo;
     
     public Foo geFoo() {
       return foo;
     }
   }
   
   @Component
   public class AppRunner implements ApplicationRunner {
     
     @Autowired
     Foo foo;
     
     @Autowired
     Bar bar;
     
     @Override
     public void run(ApplicationArguments args) throws Exception {
       System.out.println(bar.getFoo());
     }
   }
   ```

   

2. ### Prototype Scope

   - @Scope("prototype")으로 빈의 스코프를 설정할 수 있다.

   ```java
   @Component @Scope("prototype")
   public class Proto {
   }
   ```

   - IoC에서 빈을 받아올 때마다 매번 인스턴스를 새로 생성한다.

     (getBean을 할 때마다 다른 인스턴스가 반환됨.)

   

   > 싱글톤 빈은 한번만 생성되고 이 때 프로토타입 빈의 주입도 완료되기 때문에, 싱글톤 빈에서 프로토타입 빈을 참조하는 경우 인스턴스가 업데이트 되지 않는 문제가 생길 수 있다. (이는 proxyMode, objectProvider를 통해 해결할 수 있다.) 





## Reference

- [예제로 배우는 스프링 입문 (개정판)](https://www.inflearn.com/course/spring_revised_edition/dashboard)
- [[Spring] Spring IoC와 DI](https://gangnam-americano.tistory.com/60)
- [[Spring] 빈의 Scope - 싱글톤과 프로토타입](https://atoz-develop.tistory.com/entry/Spring-%EB%B9%88%EC%9D%98-Scope-%EC%8B%B1%EA%B8%80%ED%86%A4%EA%B3%BC-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85)

