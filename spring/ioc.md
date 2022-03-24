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



## Reference

- [예제로 배우는 스프링 입문 (개정판)](https://www.inflearn.com/course/spring_revised_edition/dashboard)
- [[Spring] Spring IoC와 DI](https://gangnam-americano.tistory.com/60)