## Oracle Cloud Infrastructure (OCI) Networking — Teaching Notes 

### 1. What You Will Learn

- The basics of Virtual Cloud Networking (VCN) in OCI
- How to create gateways and connect networks
- How to secure your resources using subnets, security lists, and network security groups
- How to connect to resources inside and outside OCI (internet, on-premises, other regions)
- Basics of DNS and Load Balancer in OCI

### 2. Introduction to OCI Networking

- **Region:** A geographic location where Oracle Cloud's data centers are located (over 26 worldwide).
- **Availability Domain:** A separate, independent data center in a region. Used for high availability.
- **Fault Domain:** Extra isolation within an availability domain to keep resources even safer.

### 3. VCN (Virtual Cloud Network)

- **VCN:** Like your own private network in the cloud—just as you'd have a physical network in an office/building.
- **Subnet:** A smaller "section" in your VCN. Subnets can be public (can reach internet) or private (can’t).
- **Each VCN lives in one region.** Subnets can't overlap — every IP range must be unique inside the VCN.
- **CIDR Block:** The range of IP addresses your VCN or subnet can use (e.g., 10.0.0.0/16).
- **RC1918:** The recommended private IPv4 ranges for your internal VCN (e.g., 10.0.0.0/8, 172.16.0.0/12).

### 4. Gateways & Connecting Networks

- **Internet Gateway:** Lets resources in your VCN go to the internet and vice versa (bi-directional).
- **NAT Gateway:** Lets resources inside (private) go out to the internet, but not the other way around (one-way).
- **Service Gateway:** Allows your VCN to talk to Oracle services (like Object Storage) over a private connection rather than the internet.
- **Dynamic Routing Gateway (DRG):** Connects your VCN to other networks or on-premises data centers (supports VPN, FastConnect).

### 5. Subnets in Detail

- **Regional Subnet:** Can be used across all availability domains in a region.
- **AD-specific Subnet:** Fixed to one availability domain.
- **Subnet Size:** You can make a subnet larger or smaller, but subnets can’t overlap IP ranges.

### 6. VNICs & IP Addresses

- **VNIC (Virtual Network Interface Card):** Network adapter attached to a compute instance.
- Each instance has at least 1 VNIC, but you can add more.
- A VNIC provides a **primary private IP** and can have up to 31 **secondary private IPs**.
- **Public IPs:** Assigned optionally to private IPs in public subnets; makes a resource reachable from outside.

### 7. Public IP Types

- **Ephemeral:** Temporary; goes away when the resource is deleted.
- **Reserved:** Kept until you release it, even if a resource is deleted.
- **BYOIP (Bring Your Own IP):** You can bring IP blocks you own into OCI.

### 8. Security

Two main security layers:

#### a. Security Lists (applied at subnet level)
- Like a firewall: Controls which traffic can come in/out for a whole subnet.
- Default rules are strict: you must enable additional ports (e.g., HTTP 80, HTTPS 443) if needed.
- **Stateful:** Only need to add one rule per direction (if you allow incoming, outgoing is implied).
- **Stateless:** Must specify both directions separately.

#### b. Network Security Groups (NSG) (applied to selected VNICs)
- Fine-grained controls, only apply to selected resources (not all in subnet).
- OCI recommends using NSGs for more precise security.

You can use both security lists and NSGs at the same time. Everything is denied by default; you have to allow the traffic you want.

### 9. Bastion Service

- Lets you securely connect (using SSH) to private instances (e.g., those with no public IP) through a bastion host on a public subnet.
- Oracle provides Bastion as a managed service, no need to run your own compute instance.

### 10. Peering (Connecting VCNs)

#### a. Local Peering
- Connect two VCNs inside the same region (even across tenancies).
- Use **Local Peering Gateway (LPG)** or **DRG**.

#### b. Remote Peering
- Connect VCNs in different regions.
- Uses **DRG** in each region.

#### c. Transit Routing
- A "hub-and-spoke" setup: a central (hub) VCN connects to multiple "spoke" VCNs and/or on-premises networks.

### 11. Connecting to the Outside World

- **Internet Gateway:** Connect to/from internet.
- **NAT Gateway:** Outbound-only internet (for patches, updates).
- **VPN Connect (IPsec):** Secure connection to on-premises or remote networks. Free, but depends on your own internet bandwidth and has no SLA.
- **FastConnect:** Private, dedicated link with guaranteed bandwidth and SLA. Used for high-performance, reliable connections (1Gbps, 10Gbps, 100Gbps).
- Use **VPN** for testing/dev, upgrade to **FastConnect** for production/high volume.

### 12. DNS in OCI

- **DNS (Domain Name System):** Translates human-friendly names (example.com) into IP addresses computers use.
- **OCI Managed DNS:** Secure, global, lets you manage domain records (A, AAAA, CNAME, etc.), delegate zones, and upload existing zone files.
- **Public DNS:** Accessible by everyone.
- **Private DNS:** Only accessible inside your VCN(s) or on-premises (can set up listeners/forwarders for hybrid environments).
- **Traffic Management (Steering):** Lets you set policies for failover, geo-based routing, canary testing, load balancing etc.

### 13. Load Balancer

- **Purpose:** Distributes incoming requests to multiple backend servers for performance and availability.
- **Public Load Balancer:** Has a public IP, routes internet traffic to backend servers.
- **Private Load Balancer:** No public IP; used inside your private network or with DRG for on-premises access.
- Supports TCP, HTTP, HTTP2, WebSocket, SSL termination, session persistence.
- **Fixed and Flexible Shapes:** Choose fixed bandwidth or auto-scale as needed.
- **Redundancy:** Creates a failover load balancer in a second availability domain for high availability.
- **Health Checks:** Regularly monitors backend servers; removes unhealthy servers automatically.
- **Policies:** Decide how traffic gets distributed (Round Robin, Least Connections, IP Hash, etc.).
