# Thread



## 멀티 스레드 개념

- 프로세스 : 실행 중인 하나의 애플리케이션

  하나의 프로세스에 오류가 발생해도 다른 프로세스에 영향을 미치지 않는다. (독립적)

- 스레드 : 하나의 코드 실행 흐름

  멀티 스레드는 하나의 프로세스 내부에 생성되므로 하나의 스레드가 예외를 발생시키면 프로세스 자체가 종료될 수 있다.



#### 메인스레드

- 모든 자바 애플리케이션에서 main() 메소드를 실행하면서 시작되고, 메소드가 끝나면 스레드도 종료된다.

- 메인스레드는 작업 스레드들을 만들어서 병렬로 코드를 실행할 수 있다.
- 싱글 스레드 : 메인스레드 종료 -> 프로세스 종료, 멀티 스레드 : 실행 중인 스레드 있으면 프로세스 종료x



## 작업 스레드 생성과 실행

#### 1. Thread 클래스로부터 직접 생성 

```java
Thread thread = new Thread(Runnable target);
```

start() 메소드를 호출하면 Runnable의 run() 메소드가 실행된다.



- Runnable

  - 작업 스레드가 실행할 수 있는 인터페이스

  - run() 메소드만 있음

  - 익명 객체나 람다식을 주로 사용

    ```java
    Thread thread1 = new Thread( new Runnable() {	// 익명 객체
      public void run() {
        // 코드
      }
    } );
    
    Thread thread2 = new Thread( () -> { 	// 람다식
      // 코드
    } );
    ```



#### 2. Thread 하위 클래스로부터 생성

Thread를 상속하여 run() 메소드를 오버라이딩한다.

```java
public class WokerThread extends Thread {
  @Override
  public void run() {
 		// 코드   
  }
}
```



- 스레드의 이름 : 메인 스레드는 "main", 이외 스레드는 자동으로 "Thread-n"으로 설정되어 있으며, `setName()` 메소드를 이용해 설정할 수 있다.
- `Thread.currentThread()` : 현재 스레드



## 스레드 우선순위

- ##### 동시성 (Concurrency)

  - 멀티 작업을 위해 하나의 코어에서 멀티 스레드가 번갈아가며 실행하는 성질

    (한 코어에서 여러 스레드가 돌아가면서 실행됨)

  - 싱글 코어에서 멀티스레드 작업을 하면 동시성 작업을 통해 병렬적으로 실행되는 것처럼 보이게 된다.

- ##### 병렬성 (Parallelism)

  - 멀티 작업을 위해 멀티 코어에서 개별 스레드를 동시에 실행하는 성질 

    (여러 스레드가 여러 코어에서 실행됨)



### 스레드 스케줄링

- 스레드의 개수가 코어의 수보다 많을 경우, 스레드를 어떤 순서에 의해 동시성으로 실행할 것인가를 결정

#### 자바의 스레드 스케줄링

- 우선순위 (Priority)
  - 우선순위가 높은 스레드가 실행 상태를 더 많이 가짐
  - `setPriority()` 메소드를 이용해 1~10까지의 정수 중 하나로 설정 가능 (Thread 클래스 상수도 있음)
- 순환 할당(Round-Robin)
  - Time Slice를 정해서 하나의 스레드가 정해진 시간만큼 실행되도록

> 스레드 스케줄링은 위 두가지 방법을 함께 사용해서 JVM에서 제어된다 (코드 제어 불가)



## 동기화 메소드와 동기화 블록

여러 스레드가 하나의 객체를 참조할 경우 의도와 다르게 객체의 상태가 변경될 수 있다.

- ##### 임계 영역 (critical section)

  - 하나의 스레드만 실행할 수 있는 코드 영역
  - 다른 스레드는 동기화 메소드나 블록이 끝날 때까지 대기한다.

- ##### 동기화 메소드

  - 스레드가 동기화 메소드를 실행하는 즉시 다른 스레드가 임계 영역을 실행하지 못하도록 객체에 잠금이 일어난다.
  - 메소드 종료시 잠금이 풀린다.
  - 동기화 메소드는 메소드 전체 내용이 임계 영역이다. 

  ```java
  public synchronized void method() {
    // 임계 영역
  }
  ```

- ##### 동기화 블록

  - 메소드 일부 내용만 임계 영역으로 만들고 싶을 때 사용한다.
  - 공유객체가 자신인 경우 this를 넣으면 된다.

  ```java
  synchronized (lockObject) {
      // 임계 영역
  }
  ```

  

## 스레드 상태

- 객체 생성 (`State.NEW`)
- 실행 대기 (`State.RUNNABLE`)
  - `start()` 메소드 호출 시 진입
  - 스케줄링 되지 않아서 실행을 기다리고 있는 상태
- 실행
  - 스레드 스케줄링으로 선택된 스레드가 `run()` 메소드를 실행 중인 상태
- 일시 정지
  - `State.WATING` : 다른 스레드가 통지할 때까지 기다리는 상태
  - `State.TIMED_WAITING` : 주어진 시간 동안 기다리는 상태 (`Thread.sleep()`)
  - `State.BLOCKED` : 사용하고자 하는 객체의 락이 풀릴 때까지 기다리는 상태
- 종료 (`State.TERMINATED`)
  - 스레드 실행 종료



## 스레드 상태 제어

- `sleep()` : 호출한 스레드를 주어진 시간동안 일시 정지 상태로 만든다.

- `yield()` : 호출한 스레드는 실행 대기 상태로 돌아가고, 동일하거나 높은 우선순위를 가진 다른 스레드가 실행 기회를 가진다.

  ```java
  public class ThreadA extends Thread {
    public boolean stop = false;
    public boolean work = true;
    
    public void run() {
      while (!stop) {
        if (work) {
          System.out.println("ThreadA 작업");
        } else {
          Thread.yield();		// work가 false되면 불필요한 루프를 실행하지 않고 다른 스레드에 실행을 양보
        }
      }
     System.out.println("ThreadA 종료")
    }
  }
  ```

- `join()` :  다른 스레드가 종료될 때까지 일시정지

  ```java
  public class JoinExample {
    public static void main(String[] args) {
      SumThread sumThread = new SumThread();
      sumThread.start();
      
      try {
        sumThread.join();			// sumThread가 종료될 때까지 메인 스레드는 일시정지
      } catch (InterruptedException e) {
      }
      
      System.out.println(sumThread.getSum());
    }
  }
  ```

- `wait()`, `notify()`, `notifyAll()` : 스레드 간 협업

  - Object 클래스에 선언된 메소드
  - 동기화 메소드 또는 블록 내에서만 사용 가능
  - `wait()` : 잠금을 해제하고, 일시정지 상태로 만든다.
  - `notify()` : `wait()`에 의해 일시정지된 스레드 중 한 개를 Runnable 상태로 만든다.
  - `notifyAll()` : `wait()`에 의해 일시정지된 스레드 모두를 Runnable 상태로 만든다.

  ```java
  synchronized (lockObject) {
    while (!condition) {
      lockObject.wait();		// condition이 충족될 때까지 wait 반복
    }
    // 실행할 코드
  }
  ```

  ```java
  synchronized (lockObject) {
    setCondition(true);		// condition 설정
    lockObject.notify();
  }
  ```

- `stop` : 스레드를 즉시 종료시키는 `stop()` 메소드는 스레드가 사용중이던 자원이 불안전하게 남기 때문에 deprecate.

  따라서 <u>stop 플래그</u>를 만들어서 유도한다.

  ```java
  public class XThread extends Thread {
    private boolean stop;
    
    public void run() {
      while (!stop) {
        // 반복 실행할 코드
      }
      // 스레드가 사용한 자원 정리
    }
  }
  ```

- `interrupt()` : 스레드가 일시정지 상태에 있을 때 InterruptedException을 발생시킨다.

  ```java
  public class ThreadB extends Thread {
    
    public void run() {
      try {
        while (true) {
          System.out.println("실행 중");
          Thread.sleep(1);
        }
      } catch (InterruptedException e) {
      }
      
      System.out.println("자원 정리");
      System.out.println("실행 종료");
    }
  }
  ```

  ```java
  ThreadB threadB = new ThreadB();
  threadB.start();
  
  threadB.interrupt();	// Thread.sleep으로 일시정지 상태에 들어갔을 때, InterruptedException이 발생하여 루프를 탈출하게 된다.
  ```



## 데몬 스레드

- 데몬 (daemon) 스레드 : 주 스레드의 작업을 돕는 보조적인 역할을 수행하는 스레드
- 주 스레드 종료 시 데몬 스레드도 같이 종료
- `setDaemon()` 메소드를 통해 설정 가능

- 스레드의 `start()`가 호출되기 전에 데몬으로 설정해야 함.

```java
public class AutoSaveThread extends Thread {
  public void save() {
    // 변경 사항 저장
  }
  
  @Override
  public void run() {
  	while (true) {
      ...
      save();
    }
  }
}
```

```java
public static void main(String[] args) {
  AutoSaveThread autoSaveThread = new AutoSaveThread();
	autoSaveThread.setDaemon(true);	// 메인스레드 종료 시 데몬스레드인 AutoSaveThread도 종료
	autoSaveThread.start();
}
```



## 스레드 그룹

- 관련된 스레드를 묶어서 관리할 목적으로 이용된다.
- JVM 실행 시 system 스레드 그룹 -> main 스레드 그룹을 생성한다.
- 스레드는 반드시 하나의 스레드 그룹에 포함 (default는 자신을 생성한 스레드와 같은 스레드 그룹)

```java
ThreadGroup tg = new ThreadGroup(String name);	// 현재 스레드가 속한 그룹의 하위 그룹으로 생성
ThreadGroup tg = new ThreadGroup(ThreadGroup parent, String name);
```

이와 같이 스레드 그룹을 생성할 수 있고, 스레드의 생성자에 매개변수로 스레드 그룹을 넘겨주면 스레드 그룹에 포함된다.

- 스레드 그룹의 `interrupt()`를 사용하면 그룹 내 모든 스레드에 대해서 이뤄지며, 예외처리는 해주지 않는다.



## 스레드풀

### 스레드 폭증

- 병렬 작업 처리가 많아지면 스레드 개수가 증가

- 스레드 생성과 스케줄링으로 인해 자원 사용이 증가하고 성능이 저하

  

### 스레드풀

- 작업 처리에 사용되는 스레드를 제한된 개수만큼 정해놓음

- 작업 큐에 들어오는 작업들을 하나씩 스레드가 맡아서 처리

- 작업 처리가 끝난 스레드는 다시 작업 큐에서 작업을 할당받고 처리

  

### ExcutorService 인터페이스, Executors 클래스

- java.util.concurrent 패키지에서 제공

- Executors의 static 메소드를 이용해서 ExecutorService 구현 객체 (스레드풀) 생성

  - `newCachedThreadPool()` : 초기 스레드, 코어 스레드 수 = 0, 작업 개수 증가하면 새 스레드 생성

  - `newFixedThreadPool(int nThreads)` : 코어 스레드 수 = nThreads이며 

    [^코어 스레드]: 최소한 유지해야할 스레드 수

- ThreadPoolExecutor 객체를 직접 생성할 수도 있음

  

### 스레드풀 종료

- main 메소드가 종료되어도 애플리케이션 프로세스는 종료되지 않는다. 
- 스레드풀을 종료시켜 모든 스레드들이 종료되어야 한다.
  - `shutdown()` : 처리 중인 작업 및 작업 큐에 대기 중인 모든 작업을 처리한 뒤 스레드풀 종료
  - `shutdownNow()` : 작업 처리 중인 스레드를 interrupt해서 중지시키고 스레드풀 종료. 작업 큐에 남은 미처리된 작업들(`List<Runnable>`)을 반환
  - `awaitTermination(long timeout, TimeUnit unit)` : `shutdown()` 호출 이후 시간 내에 완료하면 true, 못하면 스레드 interrupt 후 false

### 

### 작업 생성과 처리 요청

- task는 Runnable(리턴 x) 또는 Callable(리턴 o) 구현 클래스로 표현
- 작업 처리 요청 : ExecutorService의 작업 큐에 Runnable 또는 Callable 객체를 넣는 행위
  - `void execute(Runnable command)` : 처리 결과 x
  - `Future submit(Runnable task or Callable<V> task)` : 처리 결과를 Future로 리턴



### 블로킹 방식의 작업 완료 통보

- ##### 지연 완료 (pending completion) 객체 : Future

  -  Future 객체는 작업이 완료될 때까지 블로킹(지연)되었다가 반환되기 때문에 이를 지연 완료 객체라고 한다.
  - Future의 `get()` 메소드를 이용해 작업 결과를 얻을 수 있다.

- 스레드가 작업을 완료하기 전까지는 `get()`이 블로킹되므로 다음 코드 실행 불가

- 따라서 다른 스레드에서 `get()` 호출해야한다.



### 리턴값이 없는 작업 완료 통보

- `submit(Runnable task)`를 사용

  ```java
  ExecutorService executorService = Executors.newFixedThreadPool(
  	Runtime.getRuntime().availableProcessors()
  );
  
  Future future = executorService.submit(new Runnable() {
  	@Override
    public void run() {
      // 생략
    }
  });
  
  try {
    future.get();
  } catch (InterruptedException e) {
    // 작업 처리 도중 스레드가 interrupt 될 경우
  } catch (ExecutionException e) {
    // 작업 처리 도중 예외 발생
  }
  
  executorService.shutdown();
  ```



### 리턴값이 있는 작업 완료 통보

- `sumbmit(Callable<V> task)`를 이용하여 위와 같이 하면 된다. 차이점으로 Callable\<T\>의 `call()`은 T를 반환한다.



### 작업 완료 순으로 통보

- CompletionService의 `poll()`과 `take()`를 사용한다.

  - `poll()` 은 완료된 작업이 없을 경우 null을 즉시 리턴
  - `take()`는 완료된 작업이 없을 경우 블로킹

  ```java
  ExecutorService executorService = Executors.newFixedThreadPool(
  	Runtime.getRuntime().availableProcessors()
  );
  
  CompletionService<Integer> completionService = new ExecutorCompletionService<Integer>(executorService);
  
  for (int i=0; i<3; i++) {	// 스레드풀에 3개의 Callable 작업 요청
    completionService.submit(new Callable<Integer> {
    @Override
    public Integer call() throws Exception {
      // 생략
    }
  	});
  }
  
  executorService.submit(new Runnable() {		// 스레드풀의 스레드에서 실행되도록 함
    @Override
    public void run() {
      while (true) {
        try {
          Future<Integer> future = completionService.take();	// 작업이 완료되는 순서대로 가져온다.
          int value = future.get();
          System.out.printlnt("처리 결과 : " + value);
        } catch (Exception e) {
          break;
        }
      }
    }
  });
  ```



### 콜백 방식의 작업 완료 통보

- **콜백** : 스레드가 작업을 완료하면 특정 메소드를 자동 실행하는 기법

- ##### 블로킹 방식과 콜백 방식의 차이

  - 블로킹 방식 : 작업이 완료될 때까지 블로킹됨
  - 콜백 방식 : 작업 처리를 요청한 후 결과를 기다릴 필요 없이 다른 기능 수행

 

```java
CompletionHandler<V, A> callback = new CompletionHandler<V, A>() {	// attachment가 필요 없으면 void로 설정
  @Override
  public void completed(V result, A attachment) {  
  }
  @Override
  public void failed(Throwable exc, A attachment) {
  }
};
```



## Reference

- 신용권, 『이것이 자바다』, 한빛미디어(2015)
- [How to work with wait(), notify() and notifyAll() in Java?](https://howtodoinjava.com/java/multi-threading/wait-notify-and-notifyall-methods/)