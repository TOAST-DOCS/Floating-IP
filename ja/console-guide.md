## Network > Floating IP > コンソール使用ガイド

この文書ではコンソールでFloating IPを扱う時に必要な内容を記述します。

## Floating IP
### 作成

Floating IPは外部ネットワークを選択して指定されたネットワークからIPを割り当てられて使用できます。これを<b>IPプール</b>と表現し、NHN Cloudは現在"Public Network" 1個のみ選択できます。IPプールを選択した後、作成ボタンをクリックしてFloating IPを作成できます。Floating IPの作成は <b>インスタンス > 管理ページ</b> または <b>インスタンス > Floating IPページ</b>でも行うことができます。

### 削除保護設定
削除保護を設定することで、フローティングIPが誤って削除されるのを防ぐことができます。

### ラベル設定
ラベルは、フローティングIPを識別するために使用するオプションの文字列で、使用用途の区分や所有者の識別など、様々な目的に活用できます。
特定のサービスでは、ラベル値を基準にフローティングIPを参照したり、自動連動を行うことができ、そのために事前に定義された値を指定する必要がある場合があります。このような場合、ラベル値を変更すると、当該サービスの動作や連動状態に影響を与える可能性があるため、変更には注意が必要です。
ラベルは英数字のみ使用でき、最大64文字まで入力可能です。

### 接続と解除

インスタンスの状態と関係なくFloating IPを接続または、解除できます。 <b>インスタンス > 管理ページ</b>で対象インスタンスを選択し、<b>Floating IP管理</b> ボタンをクリックしてFloating IPを接続または解除できます。 Floating IPの解除は <b>Floating IP</b> ページでも行うことができます。

> [参考]インスタンスにFloating IPを接続するにはインスタンスが含まれるサブネットがルーティングテーブルと接続されていて、<br>該当ルーティングテーブルがインターネットゲートウェイを介してインターネットに接続されている必要があります。

### 削除
選択したフローティングIPを削除します。ただし、削除保護が設定されていたり、デバイスに接続されている場合は削除が制限されるため、まず保護を解除するか、接続を解除する必要があります。

### 複数のネットワークインターフェイスを持つインスタンスにFloating IPを接続する

複数のネットワークインターフェイスを持つインスタンスは、各ネットワークインターフェイスにFloating IPを接続できます。しかし最初を除いた残りのネットワークインターフェイスに接続したFloating IPにインスタンスに接続するにはインスタンスのRouting Rule設定が必要です。

**TOASTで提供する共用Linuxイメージ配布バージョン`2018.12.27`以上**で作成したインスタンスは、起動時にRouting Ruleを自動的に設定してそれぞれのネットワークインターフェイスに接続されたすべてのFloating IPを介してアクセスできます。

インスタンスに接続した後、次のようにRouting Rule設定を確認できます。
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
上記のようにip ruleコマンドを実行した時、各ネットワークインターフェイスごとにRouting Ruleが設定されている場合、すべてのFloating IPを介してインスタンスにアクセスできます。

これ以外のイメージで作成したインスタンスは、次のようにインスタンス内にRouting Ruleを設定してインスタンスに接続されたすべてのFloating IPを介してアクセスするようにできます。

最初のネットワークインターフェイス(eth0)に接続されたFloating IPを介してインスタンスに接続した後、 Floating IPを接続して接続しようとする残りのネットワークインターフェイスに対して次のようなコマンドを実行します。
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
`eth1`、`eth2`に対してFloating IPに接続するために以下のようなコマンドを利用してRouting Ruleを設定します。

```
# eth1のFloating IPに接続するためのRouting Rule設定
ip rule add from 172.16.0.37/32 table 2 priority 200
ip route add default via 172.16.0.1 table 2
ip route add 172.16.0.0/24 dev eth1 table 2

# eth2のFloating IPに接続するためのRouting Rule設定
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

上のRouting Rule設定はインスタンスを再起動すると初期化されるため、インスタンスが再起動する時にRouting Ruleが自動的に設定されるように設定することを推奨します。
