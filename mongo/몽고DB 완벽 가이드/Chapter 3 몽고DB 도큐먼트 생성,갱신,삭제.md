### 몽고DB 도큐먼트 생성, 갱신, 삭제
> 출처 : 몽고DB 완벽 가이드
> https://search.shopping.naver.com/book/catalog/32481967929?query=%EB%AA%BD%EA%B3%A0DB%20%EC%99%84%EB%B2%BD%20%EA%B0%80%EC%9D%B4%EB%93%9C&NaPm=ct%3Dl9kzjuew%7Cci%3D125366acd074c1ce57232f761da2eca95628632a%7Ctr%3Dboksl%7Csn%3D95694%7Chk%3D559faca4d89afad75f19921863aab8de637fe73b

### 도큐먼트 삽입
#### insertOne
* 몽고DB에 데이터를 추가하는 기본적인 방법
* ex. ```db.movies.insertOne({title: "제목"})```
```sql
db.collection.insertOne(
   <document>,
   {
      writeConcern: <document>
   }
)
```

#### write Concern 이란?
> https://www.mongodb.com/docs/manual/reference/write-concern/
* 특정 개수 이상의 Replication에 대한 쓰기 작업을 위해 요청한 승인 여부를 설정하는 옵션
* ```{ w: <value>, j: <boolean>, wtimeout: <number> }```
* WiredTiger는 MVCC(MultiVerseion Concurrency Control)를 사용한다.
  * MVCC란?
    * https://www.mongodb.com/docs/manual/core/wiredtiger/#snapshots-and-checkpoints 
    * 동시 접근을 허용하는 데이터베이스에서 동시성을 제어하기 위해 사용하는 방법
    * MVCC 모델에서 데이터에 접근하는 사용자는 접근한 시점에서 데이터베이스의 Snapshot을 읽는다.
    * 이 Snapshot 데이터에 대한 변경이 완료될 때까지 만들어진 변경사항은 다른 DB 사용자가 볼 수 없다.
    * 사용자가 데이터를 업데이트하면 이전의 데이터를 덮는게 아니라 새로운 버전의 데이터를 UNDO 영역에 생성한다.
    * 이렇게 하나의 데이터에 대해 여러 버전의 데이터가 존재하고, 사용자는 마지막 버전의 데이터를 읽게 된다.
* 체크포인트는 데이터 파일과 트랜잭션 로그가 동기화되는 시점을 의미한다.
* 3.6버전부터 몽고디비는 60초 간격으로 체크포인트를 생성(즉, 스냅샷 데이터를 디스크에 기록)한다.
* journaling 이란?
  * https://www.mongodb.com/docs/manual/core/journaling/
  * WiredTiger는 체크포인트를 사용하여 디스크의 일관된 데이터를 제공하고 마지막 체크포인트에서 복구할 수 있도록 한다.
  * 하지만 체크포인트 사이에 몽고DB가 예기치 않게 종료되는 경우 마지막 체크포인트 이후에 발생한 정보를 복구하기 위해 저널링을 사용한다.
  * WiredTiger는 쓰기 작업이 발생할때마다 journal record를 생성하며 그 안에는 해당 쿼리와 인덱스 변경에 대한 내용이 포함된다.
  * 따라서, 저널은 체크포인트 사이의 모든 데이터 변경 작업 사항을 유지하기 때문에 재해상황시에 저널 파일을 이용하여 마지막 체크포인트 이후의 모든 변경상황을 재실행해서 영속성을 보장할 수 있다.
* w Option
  * Replica Set에 속한 멤버 중 지정된 수만큼의 멤버에게 데이터 쓰기가 완료되었는지 확인한다.
  * ex. 3이라고 설정했을 때 primary 1개 secondary 5개라면 primary 1 + secondary 2 데이터 쓰기가 완료되면 응답한다.
* j option
  * 저널링이 기록된 후 완료를 결정하는 옵션
  * ex. w = 3, j = true로 설정 시 디스크의 저널까지 기록이 완료된 후 response 한다. 
* wtimeout
  * primary에서 secondary로 데이터 동기화 시 timeout 값을 설정하는 옵션
  * 타임아웃을 넘어가게되면 primary에 기록되어도 error를 리턴한다.
  
#### insertMany
* 여러 도큐먼트를 컬렉션에 삽입
* [책에서 소개하는 내용]
  * 몽고DB는 48메가바이트보다 큰 메시지를 허용하지 않으므로 한 번에 일괄 삽입할 수 있는 데이터의 크기에는 제한이 있다.
  * 48메가바이트보다 큰 삽입을 시도하면 많은 드라이버는 삽입된 데이터를 48메가바이트 크기의 일괄 삽입 여러개로 분할한다.
* [공식 문서]
  * https://www.mongodb.com/docs/manual/reference/limits/#mongodb-limit-Write-Command-Batch-Limit-Size
  * 문서에서는 10만개의 연산까지 허용한다고 나와있다.
  * 10만개가 넘으면 그룹으로 분리해서 나눠서 실행한다고 되어있다.
* insertMany는 ordered 값을 false로 하면 성능을 향상시키기 위해 순서를 재배열할 수 있다.

Parameter|type|Description
---|---|---
document|document|컬렉션에 삽입할 document 배열
writeConcern|document|위에서 설명
ordered|boolean|입력한 순서대로 삽입할 지 결정하는 값 (default: true)

```sql
  db.collection.insertMany(
   [ <document 1> , <document 2>, ... ],
   {
      writeConcern: <document>,
      ordered: <boolean>
   }
)
```

#### 삽입 유효성 검사
> https://www.mongodb.com/docs/manual/core/schema-validation/

* 몽고DB는 삽입된 데이터의 최소한의 검사를 수행한다.
* 도큐먼트의 기본 구조를 검사해 "_id" 필드가 존재하지 않으면 새로 추가하고, 모든 도큐먼트는 16메가바이트보다 작아야하므로 크기를 검사한다.
* 혹은 도큐먼트가 너무 크진 않은지, UTF-8 문자열인지, 인식할 수 없는 데이터인지 확인한다.

### 도큐먼트 삭제
#### deleteOne
> https://www.mongodb.com/docs/manual/reference/method/db.collection.deleteOne/

```
db.collection.deleteOne(
   <filter>,
   {
      writeConcern: <document>,
      collation: <document>,
      hint: <document|string>        // Available starting in MongoDB 4.4
   }
)
```
* deleteOne은 필터와 일치하는 첫 번째 도큐먼트를 삭제한다.

#### deleteMany
> https://www.mongodb.com/docs/manual/reference/method/db.collection.deleteMany/

```
db.collection.deleteMany(
   <filter>,
   {
      writeConcern: <document>,
      collation: <document>
   }
)
```
* deleteMany는 필터와 일치하는 모든 도큐먼트를 삭제한다.

#### drop
* 모든 도큐먼트를 제거하는 방법은 ```db.collection.deleteMany({})``` 를 입력하면된다.
* 하지만 drop을 쓰면 훨씬 빠르다.
* delete 혹은 drop을 사용하여 제거한 데이터는 복구할 수 없다.

### 도큐먼트 갱신
#### updateOne
> https://www.mongodb.com/docs/manual/reference/method/db.collection.updateOne/

```
db.collection.updateOne(
   <filter>,
   <update>,
   {
     upsert: <boolean>,
     writeConcern: <document>,
     collation: <document>,
     arrayFilters: [ <filterdocument1>, ... ],
     hint:  <document|string>        // Available starting in MongoDB 4.2.1
   }
)
```
* 필터 도큐먼트를 첫 번째 매개변수, 수정자 도큐먼트를 두 번째 매개변수로 사용
* 갱신 요청 두 개가 동시에 발생하면 서버에 먼저 도착한 요청이 적용된 후 다음 요청이 적용된다. (동시성 문제가 발생할 일은 없음)
  * Wired Tiger 스토리지 엔진 때문에
* upsert 옵션 사용 가능
* 

#### replaceOne
> https://www.mongodb.com/docs/manual/reference/method/db.collection.replaceOne/

```
db.collection.replaceOne(
   <filter>,
   <replacement>,
   {
     upsert: <boolean>,
     writeConcern: <document>,
     collation: <document>,
     hint: <document|string>                   // Available starting in 4.2.1
   }
)
```
* replaceOne는 도큐먼트를 새로운 것으로 완전히 치환한다.
* 스키마 마이그레이션에 유용

### 갱신한 도큐먼트 반환
#### findOneAndUpdate
> https://www.mongodb.com/docs/manual/reference/method/db.collection.findOneAndUpdate/

```
db.collection.findOneAndUpdate(
   <filter>,
   <update document or aggregation pipeline>, // Changed in MongoDB 4.2
   {
     projection: <document>,
     sort: <document>,
     maxTimeMS: <number>,
     upsert: <boolean>,
     returnDocument: <string>,
     returnNewDocument: <boolean>,
     collation: <document>,
     arrayFilters: [ <filterdocument1>, ... ]
   }
)
```

* 3번 쨰 매개변수인 returnNewDocument 필드를 true로 설정하면 갱신된 도큐먼트를 치환한다.

#### findOneAndReplace
> https://www.mongodb.com/docs/manual/reference/method/db.collection.findOneAndReplace/

```
db.collection.findOneAndReplace(
   <filter>,
   <replacement>,
   {
     projection: <document>,
     sort: <document>,
     maxTimeMS: <number>,
     upsert: <boolean>,
     returnDocument: <string>,
     returnNewDocument: <boolean>,
     collation: <document>
   }
)
```

#### findOneAndDelete
> https://www.mongodb.com/docs/manual/reference/method/db.collection.findOneAndDelete/

```
db.collection.findOneAndDelete(
   <filter>,
   {
     writeConcern: <document>,
     projection: <document>,
     sort: <document>,
     maxTimeMS: <number>,
     collation: <document>
   }
)
```