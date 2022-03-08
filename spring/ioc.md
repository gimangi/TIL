# IoC



## IoC (Inversion of Control)

- 제어의 역전
- 자기가 사용할 의존성을 직접 만들어서 사용하던 기존 방식이 아니라, 제어권을 외부에 넘기는 것
- 테스트에도 용이함



- 예시

  ```java
  @Controller
  public class SampleController {
    SampleRepository sampleRepository;
    
    public SampleController(SampleRepository sampleRepository) {
      this.sampleRepository = sampleRepository;
    }
    
    public void doSomthing() {
      sampleRepository.something();
    }
  }
  
  // 테스트 코드
  public class SampleControllerTest {
    
    @MocBean
    private SampleRepository sampleRepository;
    
    @Test
    public void testDoSomething() {
      SampleContorller sampleController = new SampleController(sampleRepository);
    }
    sampleController.doSomething();
  }
  ```

  

## IoC 컨테이너

[^Bean]: IoC 컨테이너에 의해 생성되고 관리되는 객체

- Bean을 만들고 엮어주며 제공한다.
- IoC 컨테이너에 속해있는 Bean끼리의 의존성을 주입해준다.



1. #### BeanFactory

   - IoC 컨테이너의 기능을 정의하고 있는 인터페이스
   - Bean의 생성 및 의존성 주입, lifecycle 관리 등의 기능 제공

   

2. #### ApplicationContext

   - BeanFactory 인터페이스를 상속받아 BeanFactory가 제공하는 기능 외에 AOP, 메세지 처리, 이벤트 처리 등의 기능을 제공

   

   - ex) context로부터 bean 정보를 불러오기

   ```java
   @Autowired
   ApplicationContext applicationContext;
   
   @Test
   public void getBeanTest() {
     OwnerController bean = applicationContext.getBean(OwnerController.class);
     assertThat(bean).isNotNull();
   }
   ```

   - ex) controller에서 applicationContext 주입 받기

   ```java
   @Controller
   class SampleController {
     
     private final SampleRepository repository;
     private final ApplicationContext context;
     
     public SampleController(SampleRepository repository, ApplicationContext context) {
       this.repository = repository;
       this.context = context;
     }
     
     @GetMapping("/bean")
     @ResponseBody
     public String bean() {
       return "bean: " + context.getBean(SampleController.class) + "\n"
         + "repository: " + this.repository;
       // SampleController 인스턴스의 해시값이 출력됨 (같은 값이 나옴)
     }
   }
   ```

   > bean은 자동으로 주입되므로 위의 예제처럼 context로부터 직접 불러올 필요는 없다.

   

   - 이처럼 인스턴스 하나를 애플리케이션 전반에서 재사용하는 것을  **singleton scope**이라고 한다.



## Bean



#### Bean으로 만드는 방법

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

   

#### Bean을 불러오는 방법

1. @Autowired 사용
2. ApplicationContext.getBean 메소드 사용

> @Autowired를 주로 사용 (권장)



#### Bean의 Scope

1. ##### Singleton Scope

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

   

2. ##### Prototype Scope

   - @Scope("prototype")으로 빈의 스코프를 설정할 수 있다.

   ```java
   @Component @Scope("prototype")
   public class Proto {
   }
   ```

   - IoC에서 빈을 받아올 때마다 매번 인스턴스를 새로 생성한다.

     (getBean을 할 때마다 다른 인스턴스가 반환됨.)

   

   > 싱글톤 빈은 한번만 생성되고 이 때 프로토타입 빈의 주입도 완료되기 때문에, 싱글톤 빈에서 프로토타입 빈을 참조하는 경우 인스턴스가 업데이트 되지 않는 문제가 생길 수 있다. (이는 proxyMode, objectProvider를 통해 해결할 수 있다.) 



## Dependency Injection (의존성 주입)



#### IoC를 구현하는 방법

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
- [[Spring] 빈의 Scope - 싱글톤과 프로토타입](https://atoz-develop.tistory.com/entry/Spring-%EB%B9%88%EC%9D%98-Scope-%EC%8B%B1%EA%B8%80%ED%86%A4%EA%B3%BC-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85)