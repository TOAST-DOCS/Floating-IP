## Network > Floating IP > Console Guide

This document describes what is required to deal with Floating IPs in the console.

## Floating IP 

When an instance is created, an IP address is assigned from a designated subnet. The IP address is certainly included to VPC, as it is the subnet address, and we call it Fixed IP. As it is a private network address, an access from the internet is not available. Therefore, to access the internet directly from outside, an accessible IP address from outside is required. Floating IP allows direct access to an instance from the internet, by linking the address to the instance 1:1. For more details, refer to [Overview]. To create a floating IP, an external network must be selected and TOAST currently operates only one "public network".   

> [Note] To connect a floating IP to an instance, a subnet including the instance must be connected to a routing table,  <br>
> and the routing table must be connected to the internet through an internet gateway, so as to perform "connection". 

* Charges for a floating IP begin from the moment it is created, and charging shall continue before it is deleted (which is irrelevant to instance connection).  

* A connection to a floating IP does not mean a change of a fixed IP to a floating IP. 

* Traffic occurred to the internet direction shall be charged. 

* Two instances, within a same VPC, communicating via floating IP shall be charged by the usage.  

> [Note] Two instances, within a same VPC, communicating locally via fixed IP shall not be charged. 

### Associating Floating IP to Instances with Many Network Interfaces 

An instance with a multiple number of network interfaces can have each of its network interface associated with floating IP. However, in order to access the instance with a floating IP which is associated to other network interfaces, except the first one, the routing rule setting must be set for the instance.  

 An instance which is created with **`Dec.27,2018` or later version of TOAST Common Linux Image Deployment ** can be accessed via all floating IPs associated with each network interface, by configuring the auto routing rule for a booting. 

*Access an instance, and check if routing rule is set, as below: 
```
$ ip rule
0:      from all lookup local
100:    from { IP address of eth0 } lookup 1
200:    from { IP address of eth1 } lookup 2
300:    from { IP address of eth2 } lookup 3
...
32766:  from all lookup main
32767:  from all lookup default
```
When the `ip rule` command is executed and the routing rule is set for each network interface, then you can access instance via all floating IPs. 

An instance that is created by other images can be accessed via all floating IPs associated with the instance, by setting the routing rule within instance, as below: 

Access instance via floating IP which is associated to the first network interface (eth0), and execute the commands as below to other network interfaces trying to access via the floating IP. 
```
ip rule add from {IP address for network interface}/32 table {table number} priority {priority}
ip route add default via {default gateway address for network interface} table {table number}
ip route add {Subnet CIDR of network interface} dev {name of network interface} table {table number}
```

For instance, if an instance has the following network interface information
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
To access `eth1`, `eth2` via floating IP, set routing rule with the following commands 

```
# Setting Routing Rule to Access eth1 with Floating IP 
ip rule add from 172.16.0.37/32 table 2 priority 200
ip route add default via 172.16.0.1 table 2
ip route add 172.16.0.0/24 dev eth1 table 2

# Setting Routing Rule to Access eth2 with Floating IP 
ip rule add from 10.254.0.90/32 table 3 priority 300
ip route add default via 10.254.0.1 table 3
ip route add 10.254.0.0/24 dev eth2 table 3
```
Then, you can find the routing rule set as below. 

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

Since the routing rule setting returns to default along with instance rebooting, it is recommended to set auto routing rule when an instance is rebooted. 
