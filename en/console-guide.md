## Network > Floating IP > Console User Guide

This document explains about what is required when handling the floating IP in the console.

## Floating IP
### Creation

Floating IP can be used by getting an IP assigned from the specified network after selecting an external network. This is called <b>IP pool</b>, and NHN Cloud can select only one "Public Network" as of now. To create a floating IP, click the Create button after selecting the IP pool. You can also create a floating IP in the <b>Instance > Management page</b> or <b>Instance > Floating IP page</b>.

### Connecting and disconnecting

A floating IP can be connected or disconnected regardless of the instance status. You can select a target instance in the <b>Instance > Management page</b>, and click the <b>Manage floating IP</b> button to connect or disconnect the floating IP. You can also disconnect the floating IP from the <b>Floating IP</b> page.

> [Note] To connect a floating IP to the instance, the subnet containing the instance must be connected to the routing table, <br>and that routing table must be connected to the internet through an internet gateway in order for "connection" action to be carried out.

### Connecting a floating IP to the instance with multiple network interfaces

The instance with multiple network interfaces can have a floating IP connected for each network interface. However, in order to access the instance with the floating IP connected to the rest of the network interfaces except for the first one, the routing rule must be established for that instance.

**An instance created with the public Linux image deployment version `2018.12.27` or higher** automatically sets the routing rule upon booting, so it is accessible by all floating IPs that are connected to each network interface.

After accessing the instance, you can see whether the routing rule has been set as follows:
```
$ ip rule
0:      from all lookup local
100:    from { eth0 IP address } lookup 1
200:    from { eth1 IP address } lookup 2
300:    from { eth2 IP address } lookup 3
...
32766:  from all lookup main
32767:  from all lookup default
```
As above, if the routing rule has been set for each network interface when the ip rule command was run, the instance is accessible by all floating IPs.

As for instances created with other images, you can set the routing rule within the instance as the following so that they can be accessed through all floating IPs connected to the instance.

Access the instance through the floating IP connected to the first network interface (eth0), and run the following commands for the remaining network interfaces to which you want to access by connecting the floating IP.
```
ip rule add from {network interface IP address}/32 table {table number} priority {priority}
ip route add default via {network interface's default gateway address} table {table number}
ip route add {network interface's subnet CIDR} dev {network interface name} table {table number}
```

For example, if the instance has network interface information similar to the following:
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
Set the routing rule through the following commands to access, using the floating IP for `eth1`, `eth2`.

```
# Routing rule setting for accessing with eth1's floating IP
ip rule add from 172.16.0.37/32 table 2 priority 200
ip route add default via 172.16.0.1 table 2
ip route add 172.16.0.0/24 dev eth1 table 2

# Routing rule setting for accessing with eth2's floating IP
ip rule add from 10.254.0.90/32 table 3 priority 300
ip route add default via 10.254.0.1 table 3
ip route add 10.254.0.0/24 dev eth2 table 3
```
After running the command, you should be seeing the routing rules set as follows:

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

Since the routing rule setting above will be reset when the instance is rebooted, you may want to set the routing rule to be automatically set upon instance reboot.

