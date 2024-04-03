## Network > Floating IP > 콘솔 사용 가이드

본 문서에서는 콘솔에서 플로팅 IP를 다룰 때 필요한 내용을 기술합니다.

## 플로팅 IP
### 생성

플로팅 IP는 외부 네트워크를 선택하여 지정된 네트워크로부터 IP를 할당받아 사용할 수 있습니다. 이를 <b>IP 풀</b>이라 표현하며 NHN Cloud는 현재 "Public Network" 1개만 선택할 수 있습니다. IP 풀을 선택 후 생성 버튼을 클릭하여 플로팅 IP를 생성할 수 있습니다. 플로팅 IP에 대한 생성은 <b>인스턴스 > 관리 페이지</b> 또는 <b>인스턴스 > 플로팅 IP 페이지</b>에서도 할 수 있습니다.

### 연결과 해제

인스턴스의 상태와 관계없이 플로팅 IP를 연결하고 해제할 수 있습니다. <b>인스턴스 > 관리 페이지</b>에서 대상 인스턴스를 선택하고, <b>플로팅 IP 관리</b> 버튼을 클릭하여 플로팅 IP를 연결하거나 해제할 수 있습니다. 플로팅 IP에 대한 해제는 <b>Floating IP</b> 페이지에서도 할 수 있습니다.

> [참고] 인스턴스에 플로팅 IP를 연결하기 위해서는 인스턴스가 포함된 서브넷이 라우팅 테이블과 연결되어 있고, <br>해당 라우팅 테이블이 인터넷 게이트웨이를 통해 인터넷에 연결 되어 있는 경우만 "연결" 동작을 수행할 수 있습니다.

### 여러 개의 네트워크 인터페이스를 갖는 인스턴스에 플로팅 IP 연결하기

여러 개의 네트워크 인터페이스를 갖는 인스턴스는 각 네트워크 인터페이스마다 플로팅 IP를 연결할 수 있습니다. 그러나 첫 번째를 제외한 나머지에 네트워크 인터페이스에 연결한 플로팅 IP로 인스턴스에 접속하기 위해서는 인스턴스의 Routing Rule 설정이 필요합니다.

**NHN Cloud에서 제공하는 공용 Linux 이미지 배포 버전 `2018.12.27` 이상**으로 생성한 인스턴스는 부팅 시 Routing Rule을 자동으로 설정하여 각각의 네트워크 인터페이스에 연결된 모든 플로팅 IP를 통해 접근이 가능합니다. 

인스턴스에 접속 후, 다음과 같이 Routing Rule 설정 여부를 확인할 수 있습니다.
```
$ ip rule
0:      from all lookup local
100:    from { eth0의 IP 주소 } lookup 1
200:    from { eth1의 IP 주소 } lookup 2
300:    from { eth2의 IP 주소 } lookup 3
...
32766:  from all lookup main
32767:  from all lookup default
```
위와 같이 ip rule 명령을 실행했을 때 각 네트워크 인터페이스 별 Routing Rule 설정이 되어 있다면 모든 플로팅 IP를 통해 인스턴스에 접근이 가능합니다.

이 외의 이미지로 생성한 인스턴스는, 다음과 같이 인스턴스 내에 Routing Rule을 설정하여 인스턴스에 연결된 모든 플로팅 IP를 통해 접근하도록 할 수 있습니다.

첫 번째 네트워크 인터페이스(eth0)에 연결된 플로팅 IP를 통해 인스턴스에 접속한 후, 플로팅 IP를 연결하여 접속하려는 나머지 네트워크 인터페이스들에 대해 다음과 같은 명령을 실행합니다.
```
ip rule add from {네트워크 인터페이스 IP 주소}/32 table {테이블 번호} priority {우선순위}
ip route add default via {네트워크 인터페이스의 Default 게이트웨이 주소} table {테이블 번호}
ip route add {네트워크 인터페이스의 서브넷 CIDR} dev {네트워크 인터페이스 이름} table {테이블 번호}
```

예로, 인스턴스가 갖는 네트워크 인터페이스 정보가 다음과 같다고 할 때
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1454 qdisc pfifo_fast state UP qlen 1000
    link/ether fa:16:3e:8d:71:d6 brd ff:ff:ff:ff:ff:ff
    inet 192.168.100.132/24 brd 192.168.100.255 scope global dynamic eth0
       valid_lft 86379sec preferred_lft 86379sec
    inet6 fe80::f816:3eff:fe8d:71d6/64 scope link
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1454 qdisc pfifo_fast state UP qlen 1000
    link/ether fa:16:3e:06:96:2f brd ff:ff:ff:ff:ff:ff
    inet 172.16.0.37/24 brd 172.16.0.255 scope global dynamic eth1
       valid_lft 86381sec preferred_lft 86381sec
    inet6 fe80::f816:3eff:fe06:962f/64 scope link
       valid_lft forever preferred_lft forever
4: eth2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1454 qdisc pfifo_fast state UP qlen 1000
    link/ether fa:16:3e:06:ac:10 brd ff:ff:ff:ff:ff:ff
    inet 10.254.0.90/24 brd 10.254.0.255 scope global dynamic eth2
       valid_lft 86386sec preferred_lft 86386sec
    inet6 fe80::f816:3eff:fe06:ac10/64 scope link
       valid_lft forever preferred_lft forever
```
`eth1`, `eth2` 에 대해 플로팅 IP로 접속하기 위해 아래와 같은 명령을 통해 Routing Rule을 설정합니다.

```
# eth1의 플로팅 IP 접속을 위한 Routing Rule 설정
ip rule add from 172.16.0.37/32 table 2 priority 200
ip route add default via 172.16.0.1 table 2
ip route add 172.16.0.0/24 dev eth1 table 2

# eth2의 플로팅 IP 접속을 위한 Routing Rule 설정
ip rule add from 10.254.0.90/32 table 3 priority 300
ip route add default via 10.254.0.1 table 3
ip route add 10.254.0.0/24 dev eth2 table 3
```
명령 실행 후 다음과 같이 설정된 Routing Rule을 확인할 수 있습니다.

```
$ ip rule													
0:	from all lookup local
200:	from 172.16.0.37 lookup 2 	
300:	from 10.254.0.90 lookup 3 	
32766:	from all lookup main
32767:	from all lookup default

$ ip route show table 2					
default via 172.16.0.1 dev eth1
172.16.0.0/24 dev eth1  scope link

$ ip route show table 3
default via 10.254.0.1 dev eth2
10.254.0.0/24 dev eth2  scope link
```

위 Routing Rule 설정은 인스턴스를 재부팅하면 초기화되므로, 인스턴스 재부팅 시 Routing Rule이 자동으로 설정되도록 하는 것이 좋습니다.
