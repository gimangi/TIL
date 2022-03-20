# PSA

## PSA (Portable Service Abstraction)



##### Service Abstraction

- 서블릿을 사용하기 위해서 HttpServlet을 상속받고 메소드를 오버라이딩해서 사용하는 예제

  ```java
  public class OwnerCreateServlet extends HttpServlet {
  
  	@Override
  	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
  		super.doGet(req, resp);
  	}
  
  	@Override
  	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
  		super.doPost(req, resp);
  	}
  }
  ```

- Spring Web MVC의 형태

  ```java
  @Controller
  class OwnerController {
  	
    ...
      
  	@GetMapping("/owners/new")
  	public String initCreationForm(Map<String, Object> model) {
  		Owner owner = new Owner();
  		model.put("owner", owner);
  		return VIEWS_OWNER_CREATE_OR_UPDATE_FORM;
  	}
  
  	@PostMapping("/owners/new")
  	public String processCreationForm(@Valid Owner owner, BindingResult result) {
  		if (result.hasErrors()) {
  			return VIEWS_OWNER_CREATE_OR_UPDATE_FORM;
  		}
  		else {
  			this.owners.save(owner);
  			return "redirect:/owners/" + owner.getId();
  		}
  	}
    
    ...
      
  }
  ```

- HttpServlet를 직접 사용하지 않고 스프링 프로젝트를 만들어도 스프링 MVC 추상화 계층에 의해 보이지 않게 내부적으로 서블릿이 동작한다.

- 이러한 추상화 계층을 이용해서 복잡한 기술은 감추고 개발자에게 편의성을 제공하는 것
- `@Controller`의 `@Mapping`뿐만 아니라 `@Transactional`, `@Cacheable`의 예시도 있다.



##### Portable Service Abstraction

- Service Abstraction으로 제공되는 기술을 다른 기술 스택으로 간편하게 바꿀 수 있는 확장성을 가지고 있다. (기존의 코드를 바꾸지 않고 다른 기술 스택으로 교체)
  - ex) 의존성을 변경해주기만 하면 톰캣이 아니라 네티 기반으로 실행하게 변경 가능

- Spring은 Spring Web MVC, Spring Transaction, Spring Cache 등의 다양한 PSA를 제공한다. 

  

> 따라서 PSA가 적용된 잘 만든 인터페이스는 특정 기술 스택에 특화되어 있지 않고 좋은 확장성을 가진다.



## Reference

- [예제로 배우는 스프링 입문 (개정판)](https://www.inflearn.com/course/spring_revised_edition/dashboard)
- [[Spring] 스프링 PSA](https://atoz-develop.tistory.com/entry/Spring-%EC%8A%A4%ED%94%84%EB%A7%81-PSA)

- [[Spring] PSA(Portable Service Abstaction)란?](https://dev-coco.tistory.com/83)