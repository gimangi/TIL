# AOP

## AOP (Aspect Oriented Programming)

- 관점 지향 프로그래밍
- OOP의 한계
  -  OOP의 목적은 책임과 관심사에 따라 클래스를 분리 -> cohesion 증가, coupling 감소
  - 하지만 Logging, Securiy, Transaction 등 공통된 기능들이 흩어지는 단점이 있는데 이런 공통된 부가 기능을 **관심사** 라고 함
- 관심사들을 **Aspect**로 모듈화하고 비즈니스 로직에서 분리하여 재사용 -> AOP



## AOP 주요 개념

- Aspect : 관심사를 모듈화한 것
- Target : Aspect를 적용하는 곳
- Advice : 실질적으로 어떤 일을 해야할지
- JointPoint : Advice가 적용될 위치 (메소드 진입 시점, 생성자 호출 시점 등)
- PointCut : JointPoint의 상세한 스펙을 정의



## 다양한 AOP 구현 방법

- 컴파일 타임 by **AspectJ**
  - 컴파일 A.java --(AOP)--> A.class
- 바이트코드 조작 by **AspectJ**
  - A.java -> A.class --(AOP)--> 메모리
  - 클래스 로더에 특정한 옵션을 붙여서 메모리에 로드될 때 기능을 넣는다.
- 프록시 패턴 by **Spring AOP**



## 프록시 패턴

- 기존 클래스의 코드를 수정하지 않고 부가기능을 추가할 수 있다.

  ```java
  public interface Payment {
    void pay(int amount);
  }
  ```

  ```java
  public class Store {
    Payment payment;
    
    public Store(Payment payment) {
      this.payment = payment;
    }
    
    public void buySomthing(int amount) {
      payment.pay(amount);
    }
  }
  ```

  ```java
  public class Cash implements Payment {
    
    @Override
    public void pay(int amount) {
      System.out.println(amount + "현금결제");
    }
  }
  ```

  여기서 소요된 시간을 측정하는 StopWatch 기능을 추가한 CashPerf 클래스를 만든다.

  ```java
  public class CashPerf implements Payment {
    
    Payment cash = new Cash();
    
    @Override
    public void pay(int amount) {
      StopWatch stopWatch = new StopWatch();
      stopWatch.start();
      
      cash.pay(amount);
      
      stopWatch.stop();
      System.out.println(stopWatch.prettyPrint());
    }
  }
  ```

  이런 식으로 기존 Cash 클래스를 수정하지 않고 감싸서 기능을 추가할 수 있다.



- 하지만 스프링 어노테이션 `@Transactional`에서 프록시 패턴으로 트랜잭션을 처리해주기 때문에 직접 구현할 필요는 없다.



## 스프링 @AOP 적용

스프링의 어노테이션을 이용해서 메소드 처리 시간을 로깅하는 예제



- 어노테이션 인터페이스

  ```java
  @Target(ElementType.METHOD) // 어디에 쓸 수 있는지
  @Retention(RetentionPolicy.RUNTIME) // 어노테이션 정보를 언제까지 유지할 것인지
  public @interface LogExcutionTime {
  }
  ```

- 어노테이션을 처리하기 위한 Aspect

  ```java
  @Component // Bean으로 등록
  @Aspect // aspect로 등록
  public class LogAspect {
    
    Logger logger = LoggerFactory.getLogger(LogAspect.class);
    
    @Around("@annotation(LogExecutionTime)")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
      StopWatch stopWatch = new StopWatch();
      stopWatch.start();
      
      Object proceed = jointPoint.proceed();
      
      stopWatch.stop();
      logger.info(stopWatch.prettyPrint());
      
      return proceed;
    }
  }
  ```

  - joinPoint : 위의 코드에서는 어노테이션이 붙어있는 메소드 (Target)

  이제 `@LogExecutionTime` 어노테이션을 사용하면 실행시간이 로그에 표시된다.



## Reference

- [예제로 배우는 스프링 입문 (개정판)](https://www.inflearn.com/course/spring_revised_edition/dashboard)
- [[Spring] 스프링 AOP (Spring AOP) 총정리 : 개념, 프록시 기반 AOP, @AOP](https://engkimbs.tistory.com/746)
- [AOP 입문자를 위한 개념 이해하기](https://tecoble.techcourse.co.kr/post/2021-06-25-aop-transaction/)