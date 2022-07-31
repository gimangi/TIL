# MVC 패턴

**Model View Controller 패턴**

- **Model**

  : 뷰에 필요한 데이터를 모두 모델에 담아서 전달한다. 따라서 뷰는 비즈니스 로직이나 데이터 접근을 몰라도 된다.

- **View**

  : 모델에 담겨있는 데이터를 사용해서 화면을 그리는 역할을 맡는다. (HTML을 생성)

- **Controller**

  : HTTP 요청을 받아서 파라미터를 검증하고, 비즈니스 로직을 실행한다. 그리고 뷰에 전달할 결과 데이터를 조회해서 모델에 담는다.





## References

- [스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)

