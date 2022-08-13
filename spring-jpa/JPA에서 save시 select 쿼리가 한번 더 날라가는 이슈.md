#### 문제 상황
새롭게 추가한 테이블에 대해 데이터를 insert 하게되면 insert 쿼리 하나만 날라가야하는데 이상하게 select 쿼리가 한번 날라가고 insert 쿼리가 날라갔습니다.

**example**
```java
@Entity
@Table(name = "webtoon")
public class Webtoon {
    @Id
    @Column(name = "uuid")
    private String uuid;

    private String name;
}

@Entity
@Table(name = "web_novel")
public class WebNovel {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer no;

    private String name;
}
```

#### 이유
WebNovel의 경우는 PK 값이 Auto Increment로 되어있습니다.
반면, Webtoon 테이블은 uuid 값이 유니크한 값이기 때문에 Auto Increment가 아닌 uuid강 PK로 잡혀있습니다.

Spring Data Jpa 에서 save() 메서드를 살펴보면 다음과 같습니다.
![image](https://user-images.githubusercontent.com/39120763/184470708-cf5eecc1-d128-4093-8359-13efe5189a2a.png)

save() 메서드를 살펴보면, entity의 상태가 isNew이면, persist를 진행하고 아닌 경우에는 merge를 진행하는 것을 확인할 수 있습니다.

**isNew 란?**
다음 그림을 보면 isNew는 해당 entity의 @Id(PK) 값의 유무로 true/false를 반환하는 것을 확인할 수 있습니다.
id 가 null 이면 true / null 이 아니면 false를 반환하고 있는데요.
Webtoon테이블의 uuid 경우는 id 값이 유니크한 uuid로 들어있기 때문에 false를 반환하게 됩니다.
따라서 save() 메서드에서 merge를 진행하게 됩니다.a
![image](https://user-images.githubusercontent.com/39120763/184470711-95e419a7-3710-4913-8efa-ccd7ad0a09af.png)


merge() 메서드는 영속성 컨텍스트에 해당 엔티티가 있는지 먼저 확인합니다.
엔티티가 없으면 select를 통해서 엔티티를 조회하고, select로 반환되는 값이 없으면 해당 엔티티를 insert하게 됩니다.

#### 결론
여태까지 Auto Increment로 된 테이블만 사용해왔어서 위와 같은 일이 일어나지 않았었는데, 이렇게 Auto Increment를 사용하지 않는 경우에는 위와 같은 상황이 생길 수 있어서 유의하고 사용하면 좋을 것 같습니다.

#### 추가 검증

#### Auto Increment 시 저장
![image](https://user-images.githubusercontent.com/39120763/184470713-3d600b29-54f1-42b6-ad44-a50d0fbd3073.png)
![image](https://user-images.githubusercontent.com/39120763/184470715-c83ad50b-9c15-4e5f-a6ca-3b903ce6db20.png)


#### Auto Increment 없이 저장
![image](https://user-images.githubusercontent.com/39120763/184470716-f9e93ec3-437c-4ac3-9b83-db721dc662b3.png)
![image](https://user-images.githubusercontent.com/39120763/184470718-2e8f43f7-aa31-4cd8-b191-4e2001d42bd9.png)


#### Auto Increment 없이 저장하는데 이미 같은 PK가 디비에 존재할 경우
![image](https://user-images.githubusercontent.com/39120763/184470725-01c13f55-2fe6-422c-9232-c18e1ccad8e7.png)
