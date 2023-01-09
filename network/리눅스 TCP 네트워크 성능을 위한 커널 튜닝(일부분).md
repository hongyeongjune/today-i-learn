* 참조
> https://meetup.nhncloud.com/posts/53

### Backlogs
![image](https://danielfm.me/images/production-ingress/tcp-diagram.png)
> https://danielfm.me/images/production-ingress/tcp-diagram.png

* 네트워크 각각 처리 과정 앞에는 Queue 가 존재한다.
* 네트워크 패킷 처리량이 급증했을 때 이 Queue 의 크기가 이보다 작다면 넘치는 패킷은 처리되지 않고 버려지게 된다.
* 특히, in-bound-queue 가 넘쳐서 버려지는 패킷은 애플리케이션에서 전혀 알 수 없기 때문에 대규모 패킷 처리가 필요한 서버에서는 적당히 in-bound-queue 의 크기를 증가시켜얗 ㅏㄴ다.
* 이 명령어로 증가시킬 수 있다. ```$ sysctl -w net.core.netdev_max_backlog="${Value}"```

#### Somaxconn
* listen()으로 바인딩 된 서버 소켓에서 accept()를 기다리는 소켓 개수의 hard limit
* accept()을 기다리는 ESTABLISHED 상태의 소켓(즉, connection completed)을 위한 queue
* 아파치 웹 서버 경우 listen() 시작 시 지정되는 기본값은 511 이다.
* 즉, 정리하면 somaxconn 은 client 의 연결 요청은 accept() 를 호출하기 전에 queue 에 쌓이게 된다.
* queue 가 작으면 client 의 연결 요청이 drop 될 수 있으므로 다음 항목을 크게 설정해 주어야 한다.
* 이 명령어로 증가시킬 수 있다. ```$ sysctl -w net.core.somaxconn="1024"```

#### net.ipv4.tcp_mem
* 커널에서 TCP 를 위해 사용할 수 있는 메모리 크기를 지정하는 값 = TCP 소켓의 전체 값
* net.ipv4.tcp_rmem
  * read buffer
  * ```$ sysctl -w net.ipv4.tcp_rmem="253952 253952 16777216"```
* net.ipv4.tcp_wmem
  * write buffer
  * ```$ sysctl -w net.ipv4.tcp_wmem="253952 253952 16777216"```
