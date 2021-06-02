## Network > Floating IP > 개요

본 문서에서는 콘솔에서 플로팅 IP를 다룰 때 필요한 내용을 기술합니다.

## 플로팅 IP

인스턴스가 생성되면 지정한 서브넷으로 부터 하나의 IP 주소를 할당 받습니다. 해당하는 IP 주소는 서브넷의 주소이기 때문에 당연히 VPC에 포함됩니다. 이것을 Fixed IP라고 합니다. 이 주소는 프라이빗 네트워크의 주소이기 때문에 인터넷에서 액세스가 불가능합니다. 따라서 외부에서 직접 인스턴스를 액세스 하고 싶다면 외부에서 접근이 가능한 IP 주소를 사용해야 합니다. 플로팅 IP는 인터넷에서 인스턴스를 직접 액세스하기 위해 필요한 기능입니다. 플로팅 IP를 사용하면 해당 주소와 인스턴스가 1:1로 연결되어 인터넷에서 직접 액세스가 가능해집니다. 자세한 내용은 [개요]를 참고하세요.  플로팅 IP를 생성하기 위해서는 외부 네트워크를 선택해야 하는데 TOAST에서 현재 "Public Network" 1개만 운영하고 있습니다. 

> [참고] 인스턴스에 플로팅 IP를 연결하기 위해서는 인스턴스가 포함된 서브넷이 라우팅 테이블과 연결되어 있고, <br>
> 해당 라우팅 테이블이 인터넷 게이트웨이를 통해 인터넷에 연결 되어 있는 경우만 "연결" 동작을 수행할 수 있습니다.

* 플로팅 IP는 생성과 함께 과금이 됩니다. 삭제하기 전까지는 지속적으로 과금됩니다. (인스턴스 연결과는 무관합니다.)

* 플로팅 IP가 연결된다고 해서 Fixed IP가 플로팅 IP로 변경되는 것은 아닙니다.

* 인터넷 방향으로 트래픽이 발생하면 과금합니다.

* 동일 VPC 내 두 인스턴스가 플로팅 IP를 사용하여 통신하게 되면 사용량 만큼 과금합니다.

> [참고] 동일 VPC 내 두 인스턴스가 Fixed IP를 사용하여 로컬 통신을 하게 되면 과금하지 않습니다.

### 여러 개의 네트워크 인터페이스를 갖는 인스턴스에 플로팅 IP 연결하기

여러 개의 네트워크 인터페이스를 갖는 인스턴스는 각 네트워크 인터페이스마다 플로팅 IP를 연결할 수 있습니다. 그러나 첫 번째를 제외한 나머지에 네트워크 인터페이스에 연결한 플로팅 IP로 인스턴스에 접속하기 위해서는 인스턴스의 Routing Rule 설정이 필요합니다.

**TOAST에서 제공하는 공용 Linux 이미지 배포 버전 `2018.12.27` 이상**으로 생성한 인스턴스는 부팅 시 Routing Rule을 자동으로 설정하여 각각의 네트워크 인터페이스에 연결된 모든 플로팅 IP를 통해 접근이 가능합니다. 

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

위 Routing Rule 설정은 인스턴스를 재부팅하면 초기화 되므로, 인스턴스 재부팅 시 Routing Rule이 자동으로 설정되도록 설정하는 것이 좋습니다.
