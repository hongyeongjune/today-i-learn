### 몽고DB 기본
> 출처 : 몽고DB 완벽 가이드
> https://search.shopping.naver.com/book/catalog/32481967929?query=%EB%AA%BD%EA%B3%A0DB%20%EC%99%84%EB%B2%BD%20%EA%B0%80%EC%9D%B4%EB%93%9C&NaPm=ct%3Dl9kzjuew%7Cci%3D125366acd074c1ce57232f761da2eca95628632a%7Ctr%3Dboksl%7Csn%3D95694%7Chk%3D559faca4d89afad75f19921863aab8de637fe73b

#### 몽고DB 설치 (Docker 활용)
* 먼저, Docker Image를 pull 받는다.
* ```docker pull mongo``` 명령어를 입력하면 가장 최근 버전의 이미지를 pull 받는다.
> https://hub.docker.com/_/mongo/tags
* docker-compose.yml
  ```yaml
  version: "3"
    services:
      mongodb:
        image: mongo
        restart: always
        container_name: local-mongodb
        ports:
          - "27017:27017"
        environment:
          - MONGO_INITDB_ROOT_USERNAME=root
          - MONGO_INITDB_ROOT_PASSWORD=00000000
        volumes:
          - ./data/mongodb:/data/db
  ```
* 그 다음, docker-compose 파일을 실행한다. ```docker-compose up -d```

#### 몽고DB Studio 3T와 연결
* 몽고DB를 로컬에 실행시킨 후 몽고DB IDE인 Studio 3T와 연결할 수 있다.
* Studio 3T를 설치 후, host에는 localhost를 port에는 docker-compose에 입력한 포트번호를 입력한다.
* from URI에는 ```mongodb://{username}:{password}@{ip_address}:{port}/?authSource=admin``` 다음과 같이 입력하고 Test Connection이 성공하면 끝이다.

#### 도큐먼트
* 데이터의 기본 단위 (= 관계형 데이터베이스의 행과 유사)
* ex. ```{"greeting", "Hello, world!"}```

#### 컬렉션
* 도큐먼트의 모음
* 컬렉션은 동적 스키마를 가진다.
  * 하나의 컬렉션에 모두 다른 구조를 가진 도큐먼트를 가질 수 있다.
  * 그러나, 한 컬렉션에 도큐먼트의 일관성을 가지는 것이 훨씬 나을 수 있다.
    * 같은 컬렉션에 다른 종류의 도큐먼트를 저장하면 개발자와 관리자에게 번거로운 일이 생길 수 있음.
    * 컬렉션별로 목록을 뽑으면 컬렉션 내 특정 데이터형별로 쿼리해서 목록을 뽑을 때보다 훨씬 빠르다.
    * 강튼 종류의 데이터를 하나의 컬렉션에 모아두면 데이터 지역성이 좋다.
    * 인덱스를 만들면 도큐먼트는 특정 구조를 가져야한다.
* 애플리케이션에서 프로그래밍 언어로 몽고DB의 유효성 검사 기능, 객체-도큐먼트 매핑 라이브러리를 활용할 수 있다.

#### 데이터베이스
* 몽고DB는 컬렉션에 도큐먼트를 그룹화할 뿐 아니라 데이터베이스에 컬렉션을 그룹 지어 놓는다.
* 몽고DB의 단일 인스턴스는 여러 데이터베이스를 호스팅할 수 있으며, 각 데이터베이스를 완전히 독립적으로 취급할 수 있다.
* admin
  * admin db는 인증과 권한 부여의 역할
* local
  * 단일 서버에 대한 정보를 저장
* config
  * 샤딩된 몽고DB 클러스터는 config 데이터베이스를 사용해 각 샤드의 정보를 저장한다.

#### 내장 도큐먼트
* 도큐먼트 키에 대한 값이 될 수 있는데 이를 내장 도큐먼트라고 한다.
```json
{
    "name" : "John Doe",
    "address" : {
        "street" : "123 Park Street",
        "city" : "Anytown",
        "state" : "NY"
    }
}
```
* 몽고DB에서는 더 많은 데이터 반복이 생길 수 있다는 단점이 있다.
* 관계형 데이터베이스와 다르게 연관관계를 맺지 않기 때문에 내장 도큐먼트를 사용하면 똑같은 값이 계속 저장될 수 있다.

#### _id와 ObjectId
* 하나의 컬렉션에 모든 도큐먼트는 고유의 "_id" 값을 가지며, 이 값은 컬렉션 내 모든 도큐먼트가 고유하게 식별되게 한다.
* 일반 DB처럼 자동 증가하는 기본 키를 사용하지 않는 것은 몽고DB의 분산 특성 때문이다.
  1. 여러 서버에 걸쳐 자동으로 증가하는 기본 키를 동기화하는 것은 어렵고 시간이 오래 걸린다.
  2. 분산 DB로 설계되었기 때문에 샤딩된 환경에서 고유 식별자를 생성하는 것이 중요하다.
* ObjectId는 12바이트 스토리지를 사용하며 24자리 16진수 문자열 표현이 가능하다.
  * 0 1 2 3 => 타임 스탬프
  * 4 5 6 7 8 => 랜덤 값
  * 9 10 11 => 카운터 (랜덤 시작 값)
