# Spring Web MVC

## Spring ?
[Spring 공식문서 Chekrry Pick 바로가기](./SPRING_DOC_CHERRY_PICK.md)

## MVC (Model-View-Controller) **아키텍쳐 패턴**
1. View : 보여지는 것
2. Controller : 입력하는 것
3. Model : 그 외의 모든 것
   - 관심사를 분리/ 비즈니스 로직이 들어가게 되는 것

## Controller
```java
@RestController
public class WelcomeController {

    @GetMapping("/")
    public String home() {
        return "Hello, world!";
    }
}
```

? 간단하다.  

> RestController vs Controller  
> RestController 는 Controller 를 포함한 다른 것들도 포함된 것이다. 가령, ResponseBody 도 같이 들어가기 때문에, 응답이 자동으로 들어가게 된다.  
