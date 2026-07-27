
### Cloud networking like the VPC, Subnet, Security group, NACL, Route table, Internet gateway, NAT gateway, VPN gateway, Direct connect.
Networking in the cloud to understand we need to know Network Function.

Application load balancer (application layer l7).  
Network load balancer (layer 4).  
Application Gateway - It can be application layer gateway and also loadbalancer.  
Classic load balancer.  
CDN.  
Firewalls - State aware (detects the outgoing request and allow the matching incoming response to the request) or stateless (configure the outbound and inbound that I want to provide for the network service).   
Subnet - It allow to segregate the network into smaller portions. It is done by the way you implement the IP addressing scheme. We can put the subnet mask to define which portaion of the IP address is the subnet and which portion is the host address for the device.      
NAT Network Address Translation - It a way to use the private IP addresses. These are IP addresses that start liek 10.x.x.x, 172.16.x.x, 192.168.x.x. We use the private IP address and allow to connect to the internet using the NAT gateway inbetween the internet and the private host that translates the private to public and maps it to the TCP or UDP.  This allows the devices in the private network to communicate with the internet while still keeping their private IP addresses hidden from the public internet.  
VPC Virtual Private Cloud - It will allow to create networks or subnetworks within the cloud. There are 2 networks - local network that uses the internet to access the cloud and the network in the cloud. It needs **VPC peering** means connection between your different virtual orivate cloud in the cloud account. In local network there is subnet and VPC as a subnet within the cloud account. There can be multiple subnets within a VPC.  
There are Transit Gateways its like a hub and spoke routing service that ar build in the cloud to allow to route between VPC and on-premise networks.   
Virtual NIC Network Interface Card - It is a virtual network adapters attached to instances or networks. It will include MAC addresses, IP addresses (public/private), VLAN assignment, VLAN assignment, Security group assignment.
### Networking to the cloud not in the cloud.
There are private connection and public connection to the cloud.
VPN - The cloud provider document L2TP/IPSec, SSL VPN, Site-to-Site VPN, Client-to-Site VPN.  
HTTPS - SSL/TLS.  
Private Connections.  
Dedicated connections - The dedicated connection to connect to the cloud provider. The features - high speed, Reliability, Security. The private connection between your LAN to teh cloud service provider.

docs/images/CloudKubernetes/VPNConnection.png
VPNConnection.png
