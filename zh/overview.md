## Network > Floating IP > Overview

The floating IP is required for directly accessing the instance on the internet. It can be used by connecting to the same resource of the instance one-to-one.

When the instance is created, you get an IP address assigned from the specified subnet, and this is called the fixed IP. However, since this address is the address of a private network, it is not accessible on the internet. So if you want to directly access the instance from outside, you must use an IP address which is externally accessible. If the floating IP is used, the address and instance are connected one-to-one, making it directly accessible on the internet.

> [Note] To connect a floating IP to the instance, the subnet containing the instance must be connected to the routing table, <br>and that routing table must be connected to the internet through an internet gateway in order for "connection" action to be carried out.

The following is the features of the floating IP:

* The floating IP can be used by having assigned an IP from the specified network after selecting an external network. Currently, only a single “Public Network” is in operation.
* The floating IP must be used by connecting it to the resource identical to that of the instance or network interface.
* The floating IP is a static address, which does not change over time.
* The floating IP can be used by connecting it to another resources after disconnecting it from the previous resource.
* Just because the floating IP gets connected does not mean the fixed IP will disappear or be replaced with a floating IP.
* The floating IP begins to be charged as of the moment it was created. It is continuously charged unless deleted. (Connection with the instance does not matter.)
* It is charged if internet-bound traffic occurs.
* It is charged based on the usage if two instances within the identical VPC use the floating IP to communicate.

> [Note] If two instances in the identical VPC use the fixed IP to carry out local communication, no fee is charged.
