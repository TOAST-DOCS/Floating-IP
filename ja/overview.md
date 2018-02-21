## Network > Floating IP > Overview

### 개요
VPC(Virtual Private Cloud)는 논리적으로 격리된 가상환경에서 프라이빗 네트워크를 구성할 수 있습니다. 프라이빗 네트워크는 인터넷 연결이 제한되기 때문에 내부에서 인터넷에 연결하기 위하여 인터넷 게이트웨이를 사용해야하며 외부에서 직접 연결하는 방법은 없습니다.
<br>
Floating IP는 프라이빗 네트워크에 포함된 인스턴스가 인터넷에서 직접 액세스가 가능하도록 퍼블릭 IP 주소를 연결하는 기능입니다.

### 동작
Floaing IP는 프로젝트로 할당 한 이후에 사용할 수 있습니다. 인스턴스에 적용과 무관하게 프로젝트 할당과 함께 과금이 발생하며 트래픽이 발생하는 경우 별도 과금됩니다. 이 기능은 NAT(Network Address Translation)를 사용하여 동작합니다. Floating IP를 인스턴스에 적용한다고 해도 인스턴스의 IP 주소가 변경되는 것은 아니며 연결 해제 후 해당 IP를 다른 인스턴스에 적용할 수 있습니다.

<https://en.wikipedia.org/wiki/Network_address_translation>


### 주요 기능

토스트 클라우드 Floating IP는 인터넷에서 인스턴스로 직접 액세스가 가능하도록 합니다.

* 할당 받은 Floating IP 주소는 해제 이전까지 반복하여 사용할 수 있습니다.

* 할당 받은 Floating IP를 인스턴스 뿐만 아니라 Load Balancer에도 사용할 수 있습니다.

* Floaing IP 개수 및 사용량에 따라 과금됩니다.