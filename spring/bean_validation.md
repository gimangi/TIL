# Bean Validation

기존 복잡한 validation 코드를 어노테이션 기반으로 간단하게 구현할 수 있도록 도와준다.



### 검증 어노테이션

- `@NotBlank` : 빈값 / 공백만 있는 경우를 허용하지 않음
- `@NotNull` : null을 허용하지 않음
- `@Range(min = 100, max = 10000)` : 범위 안의 값이어야함
- `@Max(1000)` : 범위 최대 설정

> javax.validation은 표준 인터페이스고, org.hibernate.validator의 어노테이션도 실무에서 대부분 사용하므로 자유롭게 사용해도 된다. 



### ConstraintViolation을 이용한 Bean Validation 사용법

```java
ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
Validator validator = factory.getValidator();

Set<ConstraintViolation<Item>> violations = validator.validate(item);
for (ConstraintViolation<Item> violation : violation) {
  System.out.println(violations.getMessage());
}
```

이러한 방식으로 Bean Validation을 직접 사용할 수 있다.

하지만 스프링과 연동해서 사용하면 이를 직접 구현할 필요는 없다.



### 스프링 부트와 Bean Validation 

1. 의존성 추가

   ```java
   implmentation 'org.springframework.boot:spring-boot-starter-validation'	
   ```

​	javax.validation은 인터페이스이므로 구현체가 필요하다.

​	스프링부트에서 해당 의존성을 추가하면 hibernate.validator가 구현체로 동작하게 된다.

​	의존성을 추가하면 스프링 부트는 **자동으로 `LocalValidatorFactoryBean`을 글로벌 Validator로 등록**한다.



2. `@Valid` or `@Validated` 적용

   검증할 데이터에 어노테이션을 붙여서 Validator가 검증을 진행한다.

   - `@Valid` : javax.validation에 정의된 표준 인터페이스
   - `@Validated` : 스프링 전용



// JSON 기반 처리할 거라서 이후에 오류 처리 등 추가할 예정



## References

- [스프링 MVC 2편 - 백엔드 웹 개발 활용 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)

