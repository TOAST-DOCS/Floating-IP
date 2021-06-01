## Network > Floating IP > コンソール使用ガイド

この文書ではコンソールでFloating IPを扱う時に必要な内容を記述しています。

## Floating IP

インスタンスが生成されると、指定したサブネットから1つのIPアドレスを割り当てられます。該当のIPアドレスはサブネットのアドレスのため、当然VPCに含まれます。これをFixed IPと呼びます。このアドレスはプライベートネットワークのアドレスのため、インターネットからアクセスできません。したがって外部から直接インスタンスにアクセスしたい時は、外部からアクセスできるIPアドレスを使用する必要があります。Floating IPはインターネットからインスタンスに直接アクセスするために必要な機能です。 Floating IPを使用すると、該当のアドレスとインスタンスが1:1で接続され、インターネットから直接アクセスできるようになります。詳細については[概要]を参照してください。  Floating IPを生成するためには、外部ネットワークを選択する必要がありますが、TOASTで現在"Public Network" 1個のみ運営しています。 

> [参考]インスタンスにFloating IPを接続するためには、インスタンスが含まれたサブネットがルーティングテーブルと接続されていて、 <br>
> 該当のルーティングテーブルがインターネットゲートウェイを通してインターネットに接続されている場合のみ"接続"動作を実行できます。

* Floating IPは生成と同時に課金されます。削除する前までは持続的に課金されます。 (インスタンス接続とは関係ありません)

* Floating IPが接続されるからといって、 Fixed IPがFloating IPに変更されるわけではありません。

* インターネットの方向にトラフィックが発生すると課金します。

* 同じVPC内の2個のインスタンスがFloating IPを使用して通信すると、使用量の分だけ課金します。

> [参考]同じVPC内の2個のインスタンスがFixed IPを使用してローカル通信をすると、課金しません。

### 複数のネットワークインターフェイスを持つインスタンスにFloating IP接続する

複数のネットワークインターフェイスを持つインスタンスは、ネットワークインターフェイスごとにFloating IPを接続できます。しかし最初以外のネットワークインターフェイスに接続したFloating IPでインスタンスに接続するには、インスタンスのRouting Rule設定が必要です。

**TOASTで提供する共用Linuxイメージ配布バージョン`2018.12.27`以上**で作成したインスタンスは、起動時にRouting Ruleを自動的に設定し、それぞれのネットワークインターフェイスに接続されたすべてのFloating IPを通してアクセスできます。
インスタンスに接続した後、次のようにRouting Ruleの設定状態を確認できます。
```
$ ip rule
0:      from all lookup local
100:    from { eth0のIPアドレス} lookup 1
200:    from { eth1のIPアドレス} lookup 2
300:    from { eth2のIPアドレス} lookup 3
...
32766:  from all lookup main
32767:  from all lookup default
```
上記のように`ip rule`コマンドを実行した時、ネットワークインターフェイスごとにRouting Ruleが設定されている場合、すべてのFloating IPを通してインスタンスにアクセスできます。

これ以外のイメージで作成したインスタンスは、次のようにインスタンス内にRouting Ruleを設定して、インスタンスに接続されたすべてのFloating IPを通してアクセスするようにできます。

最初のネットワークインターフェイス(eth0)に接続されたFloating IPを通してインスタンスに接続した後、 Floating IPを接続して接続しようとする残りのネットワークインターフェイスに対して次のコマンドを実行します。
```
ip rule add from {ネットワークインターフェイスIPアドレス}/32 table {テーブル番号} priority {優先順位}
ip route add default via {ネットワークインターフェイスのDefaultゲートウェイアドレス} table {テーブル番号}
ip route add {ネットワークインターフェイスのサブネットCIDR} dev {ネットワークインターフェイス名} table {テーブル番号}
```

例えば、インスタンスが持つネットワークインターフェイス情報が次のような時
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
`eth1`、`eth2`に対してFloating IPで接続するために、次のコマンドを通してRouting Ruleを設定します。

```
# eth1のFloating IP接続のためのRouting Rule設定
ip rule add from 172.16.0.37/32 table 2 priority 200
ip route add default via 172.16.0.1 table 2
ip route add 172.16.0.0/24 dev eth1 table 2

# eth2のFloating IP接続のためのRouting Rule設定
ip rule add from 10.254.0.90/32 table 3 priority 300
ip route add default via 10.254.0.1 table 3
ip route add 10.254.0.0/24 dev eth2 table 3
```
コマンド実行後、次のように設定されたRouting Ruleを確認できます。

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

上記のRouting Rule設定は、インスタンスを再起動すると初期化されるため、インスタンスの再起動時にRouting Ruleが自動的に設定されるように設定することを推奨します。
