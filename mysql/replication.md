### Replication

* 성능을 높이기 위해 MySQL 을 사용할 떄 이중화 구성(Master-Slave)을 하는 경우가 있다.
* 데이터를 복제하는 방법은 크게 ```동기```방식과 ```비동기```방식이 있다.
* 동기방식
    - Master 노드에 데이터 변경이 발생할 경우 Slave 노드까지 동시에 적용되는 것을 보장하는 방식
    - Master 노드에 장애가 발생하더라도 데이터 정합성 문제 없이 Slave 노드를 이용하여 서비스를 이어갈 수 있다.
* 비동기방식
    - Master 노드의 변경과 Slave 노드로의 적용이 시차를 두고 동기화를 하는 방식
    - Master 노드에 장애가 발생하더라도 변경된 데이터가 아직 Slave 노드에 반영되지 못한 상황일 수 있기 때문에 곧 바로 Slave 노드를 이용하여 서비스를 이어갈 경우 데이터 정합성에 문제가 발생할 수 있다.
* 즉, 성능과 정합성이라는 두 가지 요소 중 어느 것을 중요하게 취급할 것인지에 따라 선택하여 사용하면 된다.

### MySQL Replication 동작

* MySQL 은 기본적으로 비동기 복제 방식을 사용한다.
* Master 노드에서 변경되는 데이터의 이력을 로그(Binary Log)에 기록하면, Replication Master Thread 가 비동기적으로 이를 읽어서 Slave 노드에 전송하는 방식
* 필요한 요소
    - Master 노드에서 변경을 기록하기 위한 ```Binary Log```
    - Binary Log 를 읽어서 Slave 노드로 데이터를 전송하기 위한 ```Master Thread```
    - Slave 노드에서 데이터를 수신하여 Relay Log 에 기록하기 위한 ```I/O Thread```
    - Relay Log 를 읽어서 해당 데이터를 Slave 노드에 적용하기 위한 ```SQL Thread(Slave Thread)```
      <img src="https://user-images.githubusercontent.com/39120763/161996924-10ec53dc-a0be-4e5d-a2f8-8d6c368495a0.png" width="800" height="500">

* [이미지 참조 주소](http://cloudrain21.com/wordpress/wp-content/uploads/2019/10/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA-2019-10-09-%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE-9.01.41-1024x684.png)

* 순서
    1. 클라이언트에서 Commit 수행
    2. Connection Thread 는 데이터베이스에 해당 트랜잭션에 대한 준비를 수행한다.
    3. Commit 을 수행하기 전에 Binary Log 에 변경사항 기록
    4. 데이터베이스에 트랜잭션 Commit 수행
    5. Master Thread 는 시간에 구애받지 않고 비동기적으로 Binary Log 를 읽어서 Slave 로 전송
    6. Slave I/O Thread 는 Master Thread 로부터 수신한 변경 데이터를 Relay Log 에 기록한다.
    7. Slave 노드의 SQL Thread 는 Relay Log 에 기록된 변경된 데이터를 읽어서 데이터베이스에 반영한다.

### Semi-Sync vs Asynchronous
* MySQL 에서는 semi-sync 방식도 제공한다.
* Master 노드에서 Slave 노드로 전달된 변경 내역이 Relay Log 에 기록이 되었다는 메세지를 받고나서 Master DB Commit 을 진행하는 것을 말한다.
* 비동기 방식에 비하면 성능이 조금 저하되지만 완전한 동기방식보다는 성능 저하가 덜 하고 Slave 노드까지는 변경이력이 잘 전달 되었기 때문에 Master 노드와 Slave 노드 사이의 데이터에 대한 동기화나 안정성 측면에서 비동기 방식보다 더 보장할 수 있다.
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FkxUek%2FbtqTADxldPq%2FjvSmbesX3yaRo7fPGoMYK0%2Fimg.png" width="800" height="500">

* [이미지 참조 주소](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FkxUek%2FbtqTADxldPq%2FjvSmbesX3yaRo7fPGoMYK0%2Fimg.png)

* [참조](https://it-sunny-333.tistory.com/148)
