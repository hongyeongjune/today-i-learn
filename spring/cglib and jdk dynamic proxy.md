### CGLIB and JDK Dynamic Proxy

* 친한 친구가 최근에 면접을 볼 기회가 생겨서 면접 연습할 겸 저와 모의 면접을 진행하였습니다.  
* 그 과정에서 제가 Spring AOP, @Transactional 그리고 JDK Dynamic Proxy, CGLIB에 대해 질문을 했고, JDK Dynamic Proxy는 하나 이상의 인터페이스로 구현되어 있을 때, 아니라면 CGLIB로 동작한다고 알려주었습니다.
* 실제로 Spring AOP 공식 문서를 살펴보면 다음과 같이 나와있습니다.
> https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop-proxying  
> If the target object to be proxied implements at least one interface, a JDK dynamic proxy is used.  
> All of the interfaces implemented by the target type are proxied.  
> If the target object does not implement any interfaces, a CGLIB proxy is created.  

```java
public interface CglibService {
    void doSomething();
}

@Service
public class CglibServiceImpl implements CglibService {
    @Transactional
    @Override
    public void doSomething() {
    }
}

@RestController
@RequiredArgsConstructor
public class CglibRestController {

    private final CglibService cglibService;

    @GetMapping("/cglib")
    public void doSomething() {
        cglibService.doSomething();
    }
}
```

* 우선 이 코드에서 먼저 확인해야 할 부분은 해당 코드가 왜 Spring AOP로 동작하는지에 대해서 알아야합니다.
* @Transactional 어노테이션을 사용하면 별도의 트랜잭션 관련 코드없이 트랜잭션에 대한 공통 코드를 AOP를 통해 대신 처리해주게 됩니다.
* Spring에서는 프록시 패턴을 통해 AOP를 구현하게 됩니다.

#### @Transactional 이 사용되는 곳에서 동작하는 Aspect

#### Around를 사용하여 invokeWithInTransaction 메서드를 통해 AOP 진행

#### 트랜잭션 실행 -> 구현 서비스 실행 -> 트랜잭션 해제

* 따라서, @Transactional 어노테이션을 사용하면 Spring AOP로 동작하게됩니다.


* 처음에 스프링을 실행하면, CglibServiceImpl의 doSomething() 메서드는 Spring AOP로 동작하기 때문에 createAopProxy 메서드를 실행하게 됩니다.
* 따라서, 첫 실행 시에는 인터페이스 혹은 Proxy 객체가 아니기 때문에 CGLIB로 생성하게 됩니다.

* 따라서 위의 코드를 실행 후 http://localhost:8080/cglib 를 실행하면, 다음 코드를 실행해서 확인해보면, CglibService에 EnhancerBySpringCGLIB이 적혀있는 것을 확인할 수 있습니다.

* 그렇다면 여기서 한가지 의문이 들 수도 있습니다. (실제로 제 친구도 했던 질문입니다.)

* ```인터페이스를 상속받아서 사용하였는데 왜 CGLIB으로 도는건지 이해가 잘 되지 않는다.``` 라는 생각을 가질 수 있습니다.
* 사실 Spring Boot는 기본적으로 `spring.aop.proxy-target-class = true (default)`옵션으로 인해 CGLIB를 사용하게 되어 있습니다.
* 그 이유에 대해서는 아래 링크를 통해 확인을 하였습니다.
> https://stackoverflow.com/questions/54980004/why-choose-cglib-proxying-as-the-default-after-springboot-2-0  
> Interface based proxying would occasionally cause hard to track down ClassCastExceptions.   
> Specifically, it was possible that a @Bean would be replaced with a JDK proxy and then could not be injected in its original class form.  
> Since Spring Framework has a shaded copy of CGLIB these days, there was very little reason not to use it out-of-the-box.

* 그럼 관련 설정을 하고나서 다시 실행해보겠습니다.
* 위 그림과 같이 JDK Dynamic Proxy로 들어온 것을 확인할 수 있습니다.