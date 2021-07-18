2021.01.19

### **MappingJacksonJsonView**

Java 개체에서 JSON 매핑 라이브러리로 JSON 데이터 생성하게 된다.

DispatcherServlet이 클라이언트에게 응답을 제공하기 위해 CNVR에게 요청을 위임한다.

CNVR은 모든 정의된 ViewResolver를 참조하여 제공해야될 데이터에 대한 View가 존재하는지, 콘텐츠를 생성하는 View가 있는지 확인한다. (Multiple Views)

<br/>

Spring MVC는 동일한 데이터에 대해 여러 뷰를 가지고 있다. (그리고 CNVR를 통해 결정한다.)뷰를 찾기 위해서 탐색을 진행하는 것에 대해 순서를 지정할 수 있으나 결국 선택되는 첫번째 뷰를 지정하고 사용하게 된다.

MappingJacksonJsonView를 사용하기 위해서는 해당 객체를 Bean으로 등록하고 ModelAndView, Model를 사용하면 되는데, 해당 데이터를 호출할 때 jsonView라는 문자열을 생성자에 넘기거나 setViewName("jsonView"); 를 사용한다.

```jsx
ModelandView mv = new ModelAndView("jsonView");

or 

mv.setViewName("jsonView");
```

<br/>

### **MappingJacksonHttpMessageConverter**

반환되는 Entity, Dto Object의 getter()를 이용하여 필드 값을 특정하고, Json 으로 직렬화하거나 반대로 Object로 역직렬화하는 행위를 수행한다. 

<br/>

JSON으로 값을 반환할 때 @ResponseBody를 해당 메서드에 지정하면 되는데, 이것의 의미는 해당 Method의 반환 값이 HTTP Response에 직접 직렬화 되었음을 의미한다.

@RequestBody를 사용하면 HTTP 요청 본문이 Object로 역 직렬화 되었음을 의미한다.