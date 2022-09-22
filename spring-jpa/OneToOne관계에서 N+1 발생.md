#### 참조
> https://stackoverflow.com/questions/1444227/how-can-i-make-a-jpa-onetoone-relation-lazy

### OneToOne 관계에서 N+1 문제가 발생하는 이유
Fetch 타입을 LAZY로 설정했을 때 해당 연관관계를 조회하지 않으면 하이버네이트 프록시를 사용하기 때문에 직접 값을 꺼내지 않는 이상 N+1 문제가 나타나지 않는다.  
하지만, 실제로 주인이 아닌 엔티티에서 조회해보면 OneToOne 관계를 맺은 엔티티의 값을 꺼내지 않았는데도 N+1문제가 발생한다.  
왜냐하면, 프록시 객체를 만들기 위해서는 OneToOne 관계를 맺은 엔티티 객체가 null 인지 값이 존재하는지를 알아야하는데, 주인이 아닌 엔티티 입장에서는 알 수가 없기 떄문이다.  

> 지연 로딩을 설정하여도 즉시 로딩으로 동작하는 이유는 JPA의 구현체인 Hibernate 에서 프록시 기능의 한계로 지연 로딩을 지원하지 못하기 때문에 발생한다.  
> 참조 - 김영한님의 JPA ORM 프로그래밍
 
> The reason for this is that owner entity MUST know whether association property should contain a proxy object or NULL and it can't determine that by looking at its base table's columns due to one-to-one normally being mapped via shared PK, so it has to be eagerly fetched anyway making proxy pointless.  
> 참조 - https://stackoverflow.com/questions/1444227/how-can-i-make-a-jpa-onetoone-relation-lazy