AWS Shield provides protections against Distributed Denial of Service (DDoS) attacks for AWS resources at the network and transport layers and at the application layer. 

> A DDoS attach is an attack in which multiple compromised systems try to flood a target with traffic. A DDoS attack can prevent legitimate end users from accessing the target services and can cause the target to crash due to overwhelming traffic volume.

Classes of attacks that Shield detects are:
- Network volumetric attacks (layer 3): A sub category of infrastructure layer attack vectors that attempt to saturate the capacity of the targeted network or resource.
- Network protocol attacks (layer 4): Attach vectors that abuse a protocol to deny service to the targeted resource. A common example of a network protocol is a TCP SYN flood, which can cause exhaustion connection state on resources like servers, load balancers, or firewalls.
- Application layer attacks (layer 7): Attack vectors that attempt to deny service to legitimate users by flooding an application with queries that are valid for the target, such as web request flodos.