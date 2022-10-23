### 몽고DB 소개
> 출처 : 몽고DB 완벽 가이드
> https://search.shopping.naver.com/book/catalog/32481967929?query=%EB%AA%BD%EA%B3%A0DB%20%EC%99%84%EB%B2%BD%20%EA%B0%80%EC%9D%B4%EB%93%9C&NaPm=ct%3Dl9kzjuew%7Cci%3D125366acd074c1ce57232f761da2eca95628632a%7Ctr%3Dboksl%7Csn%3D95694%7Chk%3D559faca4d89afad75f19921863aab8de637fe73b

#### 손쉬운 사용
* 도큐먼트 지향 데이터베이스
* 도큐먼트 지향 모델은 복잡한 계층 관계를 하나의 레코드로 표현할 수 있다.

#### 확장 가능한 설계
* 도큐먼트 지향 데이터 모델은 데이터를 여러 서버에 더 쉽게 분산하게 해준다.
* 도큐먼트를 자동으로 재분배하고 사용자을 올바른 장비 라우팅함으로써 클러스터 내 데이터 양과 부하를 조절할 수 있다.

#### 확장 가능한 설계 상세 설명
![image](https://www.mongodb.com/docs/manual/images/sharded-cluster-production-architecture.bakedsvg.svg)
> 출처 : https://www.mongodb.com/docs/manual/sharding/

* Shard
  * 몽고DB의 단일 인스턴스
* Mongos
  * 다수 구성된 샤드의 인터페이스 역할
  * 클러이언트 요청이 올바른 샤드로 도달하도록 라우팅 하는 것이 주 기능
* Config Servers
  * 전체 클러스터의 메타 데이터, 구성 설정 등을 저장하는 서버
* 순서
  1. 애플리케이션은 실행할 쿼리에 대해 Mongos(라우터)와 통신
  2. Mongos는 구성 서버를 참조하여 해당 샤드에 쿼리를 보내는 데 필요한 데이터 셋이 포함된 샤드 확인
  3. 쿼리 결과 응답

#### 다양한 기능
* 인덱싱
* 집계
* 특수한 컬렉션 유형
  * TTL(Time-to-Live) 컬렉션 지원
* 파일 스토리지
  * 큰 파일과 파일 메타데이터를 편리하게 저장하는 프로토콜 지원
* 복잡한 조인
  * 3.2 버전에서 $lookup 집계 연산자를 통한 제한된 조인 지원
  * 3.6 버전에서 관련 없는 서브쿼리와 여러 조건으로 보다 복잡한 조인 지원
  * 사실상 복잡한 조인은 몽고디비에 존재하지 않는다.

#### 고성능
* 몽고디비에서는 동시성과 처리량을 극대화하기 위해 WiredTiger 스토리지 엔진을 사용했다. 
  * **책에서는 기회적 락을 사용했다고 표현했는데, 이 부분은 아무리 찾아보고 읽어봐도 이해가 안된다.**
  * **일단 해당 부분은 공식 문서를 통해서 따로 정리하였고, 나중에 지인한테 물어봐야겠다.**

#### 고성능에 대한 상세 설명
> 참조 : https://www.mongodb.com/docs/manual/core/wiredtiger/
> 참조 : https://stackoverflow.com/questions/34613068/what-are-intention-shared-and-intention-exclusive-locks-in-mongodb

* 몽고디비 잠금 수준
  * Global : 모든 데이터베이스가 잠기는 수준
  * Database : 언급된 데이터베이스가 잠기는 수준
  * Collection : 컬렉션 수준에서 잠기는 수준
  * Document : 특정 도큐먼트만 잠기는 수준
* 잠금 모드
  * Shared (S)
    * 공유 잠금 또는 읽기 잠금
    * 현재 스레드가 참조하고 있는 데이터를 다른 스레드가 변경하지 못하도록 방어하는 것이 목적
    * 읽기만 하는 경우에는 모두가 접근 가능
  * Exclusive (X)
    * 배타적 잠금 또는 쓰기 잠금
    * 현재 스레드가 변경하려고 하는 데이터를 다른 스레드가 변경하지 못하도록 방어하는 것이 목적
    * 변경중인 데이터를 다른 스레드가 읽는 것도 허용하지 않음
  * Intent Shared (IS)
    * 하위 수준 잠금 전에 획득한 상위 수준 잠금
    * 예를들어, 데이터베이스-level에서 Intent-Shared 락이 적용되면 컬렉션 혹은 도큐먼트 수준에서 Shared 잠금이 된다.
  * Intent Exclusive (IX)
    * 하위 수준 잠금 전에 획득한 상위 수준 잠금
    * 예를들어, 데이터베이스-level에서 Intent-Exclusive 락이 적용되면 컬렉션 혹은 도큐먼트 수준에서 Exclusive 잠금이 된다.
* WiredTiger 스토리지 엔진은 Document Level Concurrency 지원한다.
* 쓰기 연산에서 document-level 동시성을 지원한다.
  * 즉, 여러 사용자가 같은 시간에 컬렉션의 다른 document를 동시에 수정할 수 있다.
* 대부분 읽기와 쓰기 작업에서 Optimistic 동시성 제어를 사용한다.
  * 즉, 데이터를 읽는 시점에는 Lock을 걸지 않고, 수정 시점에 값이 변경되었는지 정밀하게 검사한다.
  * global-level, document-level, collection-level에서 intent lock를 사용한다.