### find
* 몽고DB에서 find함수는 쿼리에 사용한다.
* 쿼리는 컬렉션에서 도큐먼트의 서브셋을 반환한다.

#### 반환받을 키 지정
* 조회 시 모든 키/값 정보가 필요하지는 않을 수 있다.
* 그럴 때는 find(또는 findOne)의 두 번째 매개변수에 원하는 키를 지정하면된다.
  * ex. ```db.collection.find({}, {"username": 1});```

#### 제약 사항
* 데이터베이스에서 쿼리 도큐먼트 값은 반드시 상수여야 한다.

### 쿼리 조건
* $lt
  * <
* $lte
  * <=
* $gt
  * >
* $gte
  * >=
* $ne
  * not equal
* $or
  * or
* $in
  * in
  * 쿼리 옵티마이저는 $or보단 $in을 더 효율적으로 다룬다.
* $nin
  * not in
* $not
  * 메타 조건 절 - 일치하지 않는 도큐먼트 찾을 때
* null
  * 아예 존재하지 않거나, 키가 null인 값을 쿼리하면 해당 키를 갖지 않는 도큐먼트도 반환한다.
  * 존재하는 값만 조회하고 싶으면 $exists를 사용하여 값이 null인 키만 찾는 조건을 추가해야한다.

### 배열 쿼리 조건
* $all
  * 2개 이상의 배열 요소가 일치하는 배열을 찾기 위해 사용
  * ex. ```db.collection.find({array : { $all : ["data1", "data2"] }})```
* $size
  * 특정 크기의 배열을 쿼리
  * ex. ```db.collection.find({array : { $size : 3 }})```
* $slice
  * 배열요소의 부분집합을 반환받기 위해 사용
  * 처음부터 10개 => ex. ```db.collection.find({array : { $slice : 10 }})```
  * 최근 10개 => ex. ```db.collection.find({array : { $slice : -10 }})```
