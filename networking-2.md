## 1. Ways to Connect OCI to the Outside World

- **Three main options:**
    - **FastConnect:** A private, direct, and fast physical connection between your on-premises data center and Oracle Cloud. Guarantees high bandwidth (up to 100 Gbps in some places) and predictable performance. No data charges—only the port is billed.
    - **Site-to-Site VPN:** Uses the public internet to create an encrypted tunnel between your site and OCI. Usually supports up to 800 Mbps and actual speed depends on the internet.
    - **Public Internet:** Use the regular internet for access to and from OCI. Most variable speed, latency, and reliability.

## 2. More About FastConnect

- **Why use FastConnect?**
    - Reliable, high-speed, low-latency connection.
    - Good for workloads needing consistent performance, large transfers, or sensitive data.
- **Types of FastConnect:**
    1. **Direct:** Physically plugged into Oracle's data center.
    2. **Through a Partner:** Uses an Oracle networking partner (check if available in your region).
    3. **Third-party Provider:** Your network provider connects you to Oracle, even if they're not a direct partner.
- **Routing:** Uses Border Gateway Protocol (BGP) for dynamic routing between your network and OCI.

## 3. Site-to-Site VPN

- **What is it?** A free, encrypted tunnel over the internet connecting your corporate network to your OCI VCN.
- **How does it work?**
    - Connect your on-premise router to OCI’s Dynamic Routing Gateway (DRG).
    - Two tunnels by default for redundancy (use two routers if possible).
    - Uses IPSec standard for encryption. Supports IKEv1 and v2.
    - You can use static or dynamic routing (BGP).
- **Use cases:** Secure remote access, backup for FastConnect, quick connections, testing, or smaller workloads.
- **Limitations:** Not as predictable or fast as FastConnect, depends on your internet connection.

## 4. Dynamic Routing Gateway (DRG)

- **What is a DRG?** A virtual router in OCI that connects your cloud VCNs to external networks (on-prem, other regions, or other clouds).
- **Capabilities:**
    - Attach up to 300 VCNs to a DRG.
    - Enables **remote peering:** Connects VCNs in different regions or tenancies.
    - **Fine-grained routing:** Each attached resource (VCN, VPN, FastConnect, remote peer) has its own route table.
    - **Redundancy:** Recommended to use multiple connections (FastConnect/private circuits/IPSec tunnels) for high availability.
    - **Legacy DRG vs. New DRG:** You can upgrade, but be careful—it is a one-way upgrade and may cause temporary downtime unless you have redundancy.
    - **ECMP (Equal Cost Multi-Path) routing:** Supports traffic load balancing across multiple equal-cost paths.

## 5. Peering Connections

- **Remote Peering:** Connect VCNs across regions or tenancies using DRGs, enabling private communication.
    - **No overlapping subnets:** CIDR blocks must be unique across networks to work properly.
    - You can complete a connection even if subnets overlap, but you won't be able to route between those overlapping segments.

## 6. DNS in OCI

- **DNS role:** Converts easy-to-remember names (like example.com) to IP addresses.
- **How does DNS lookup work?**
    - When your computer needs to find a website, it asks servers in a specific order: your ISP’s DNS (recursive), then root servers, then top-level domain servers (.com, .net, etc.), then the actual authoritative DNS server (like Oracle).
    - Once the address is found, every server in the path can remember (cache) it for next time.
- **OCI DNS service:** 
    - **Public DNS:** Manages domains/records accessible by anyone on the internet.
    - **Private DNS:** Manages domains/records only available inside your VCN or on-premises, supports "split horizon" (the same name can have different IPs for inside and outside).
    - Allows zone imports/exports, creation of many record types (A, AAAA, CNAME, PTR, etc.).
    - **Traffic Management (Steering):** 
        - Lets you control DNS responses for failover, active/standby sites, geo-routing, and canary testing.
        - Use for cloud migration, gradual rollouts, or serving different content to users based on region.

## 7. Load Balancer in OCI

- **Purpose:** Distributes incoming traffic across multiple backend servers for high availability, performance, and reliability.
- **Public vs. Private Load Balancer:**
    - **Public:** Has a public IP; receives internet traffic.
    - **Private:** Only accessible within the VCN or via on-premises connectivity.
- **Features:**
    - Supports TCP, HTTP, HTTP/2, WebSocket, SSL termination, session stickiness.
    - Offers both **fixed** and **flexible** shapes:
        - **Fixed:** Set bandwidths (like 10, 100, 400, 8000 Mbps).
        - **Flexible:** Set minimum and maximum; auto-scales within that range.
    - **High availability:** Automatically creates standby load balancer in a separate availability domain for failover.
    - **Health checks:** Periodically test backend server health; automatically remove unhealthy servers.
    - **Traffic Distribution Policies:**
        - **Round Robin:** Distributes requests evenly.
        - **IP Hash:** Same client IP always goes to the same server.
        - **Least Connections:** Server with the fewest active connections receives traffic.

## 8. Best Practices & Important Notes

- Use FastConnect for critical, high-performance needs.
- Use multiple VPN tunnels or FastConnect circuits for redundancy and high availability.
- For DNS and load balancer zones, carefully plan your network and failover strategy.
- Always check region support when selecting partners or deploying services.

