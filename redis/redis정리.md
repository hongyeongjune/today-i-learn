### Redis 정리 (번역)
#### 참조 : https://architecturenotes.co/redis/

### What is Redis?
Redis(**Re**mote **Di**ctionary **S**ervice)는 오픈 소스 key-value 데이터베이스 서비이다.

#### Data Structure
> https://redis.com/redis-enterprise/data-structures

보통 레디스는 MySQL 또는 PostgreSQL과 같은 'real' 데이터베이스 앞단에서 캐시로 사용되어 애플리케이션의 성능을 높여준다.

#### 레디스는 전통적으로 어떻게 사용되었을까?
1. 레디스를 MySQL앞단에 두어 사용
2. 데이터가 레디스 캐시에 있는지 찾아보고 있으면 return
3. 데이터가 없다면 MySQL(Disk)에서 데이터를 찾아 반환하고 그 데이터를 레디스 캐시에 저장한다.

그러나 많은 사례에서 레디스는 기본 데이터베이스로 사용할 수 있는 충분한 보증을 제공한다.  
레디스의 다양한 플러그인과 HA 설정과 함께 레디스는 상당한 양의 데이터와 작업량을 처리할 수 있다.  
또한, 메모리의 성능이 좋아지면서 캐시와 데이터 저장소의 경계가 모호해졌다.  
이는 메모리의 데이터를 읽고 조작하는 방식이 HDD나 SSD에 접근하는 것 보다 훨씬 빠르다는 것을 의미한다.  

