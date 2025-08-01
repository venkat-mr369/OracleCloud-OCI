# Global Prime Video Application Architecture on OCI – End-to-End Technical Blueprint

## 1. Network Foundation

### 1.1 Virtual Cloud Network (VCN)

**What:** Your private, isolated cloud network home for all infrastructure resources in a region.

**Why:**  
- Core to securely segment workloads by environment, compliance need, or business function.
- Multi-region VCN deployment supports global expansion and data locality compliance.
- Facilitates cloud-to-cloud, on-prem, and cross-region connectivity.

**Design Example:**  
- Typically, deploy **1 VCN per major region** where users/content reside.

### 1.2 Subnet Strategy Per Region

**Subnet Table:**

| Subnet Type              | Count | Primary Purpose/Functionality                                                                  |
|--------------------------|-------|------------------------------------------------------------------------------------------------|
| Public Subnets           | 2     | Hosts internet-facing gateways, load balancers, edge/CDN points, and secure bastions.           |
| Private App Subnet       | 1     | Core application servers and microservices pods/VMs—processing user requests and business logic.|
| Private DB Subnet        | 1     | Dedicated for relational database clusters, NoSQL (catalog/session) nodes—isolated from the public internet. |
| Private Analytics Subnet | 1     | ETL workers, analytics engines, and machine learning tools—processing internal/reporting data.   |
| Restricted/Admin Subnet  | 1     | Jump/bastion hosts, monitoring stacks, backup, management/automation endpoints **only**.         |

#### Why a Restricted/Admin Subnet?

- **Purpose:**  
  - Deeply isolates operational and admin resources from both public and application layers.
- **Benefits:**  
  1. **Extra security:** Closed to user/app traffic, minimizes attack surface for admin tools.
  2. **Principle of least privilege:** Only select IPs/roles permitted, easier compliance reporting.
  3. **Protection of monitoring and config automation:** Ensures that even in the event of a breach on app tiers, admin operations remain secure and unaffected.
- **What runs here:**  
  - Jump hosts, CI/CD and config management servers, Prometheus/ELK monitoring, backups.

## 2. Connectivity & Security

### 2.1 Firewalls, Security Lists, NSGs

**What:** Layered security (subnet, instance/service, and API levels).

**Why:**  
- Micro-segmentation for zero-trust networking and regulatory alignment.
- Protects all resources—from user APIs to backup servers—and supports global scaling.

## 3. Application Services & Workloads

### 3.1 Compute (OKE/Kubernetes, Bare Metal/VMs)

**What:**  
- **Microservices platform:** Media streaming, user auth, search, recommendations, payment, etc., each deployed as autoscaling Kubernetes pod/service.
- **Paired with:** Language packs—services load regional/localization data for seamless multi-language support.

**Why:**  
- Enables modular, independently deployable features (reduce “blast radius”).
- Supports rolling updates, blue-green/canary releases, and region by region upgrades.

## 4. Storage and Content Delivery

### 4.1 Object Storage (Media, Assets, Backups)

**What:**  
- Stores all streaming video, images, subtitles, and archives.
- Integrates with **CDN** for accelerated global content delivery.

**Why:**  
- Petabyte scale, cost-effective, secure, and geo-replicated for regional availability.

### 4.2 Block & File Storage

**Block:** High-performance storage for DBs, caches, indexing/search engines.  
**File:** Shared for collaborative media workflows, transcoding teams.

## 5. Data Persistence & Acceleration

### 5.1 Relational (Autonomous/Exadata DBs)

**What/Why:**  
- User profiles, payments, transactions—ensures integrity, availability, compliance.

### 5.2 NoSQL (OCI NoSQL, Redis Cache)

**What/Why:**  
- Fast access to catalogs, watchlists, sessions, preferences, personalized feeds.
- Redis/OCI Cache for hot data with ultra-low latency.

## 6. Platform Load Balancing & Service Exposure

### 6.1 API Gateway & Load Balancer

**What:**  
- API Gateway: Unified entry point for REST/gRPC, enforcing authentication, rate limits.
- Load Balancer: Distributes frontend and backend traffic, SSL termination, integrated WAF.

**Why:**  
- Ensures resilience, geo-balancing, protects APIs from attack/abuse.

## 7. Security, Identity, and Compliance

### 7.1 OCI IAM & Key Management

**What:**  
- IAM: Centralized users, groups, policies for granular access.
- Key Management: Root-of-trust for encrypted storage, DBs, application secrets.

**Why:**  
- Meets compliance (GDPR, PCI, etc.), enables secure operational handoffs, and automates certificate/credential rotation.

## 8. Analytics, Insights, and Monitoring

### 8.1 Data Warehousing & Analytics

**What:**  
- Autonomous Data Warehouse: Collects playback stats, engagement data, regional/language analytics.
- Real-time log/event streaming from CDN and app microservices.
- Oracle Analytics Cloud for dashboards/KPIs.

**Why:**  
- Guides content investments, personalizes user experience, assures service quality.

### 8.2 Monitoring & Observability

**What:**  
- OCI Monitoring, Prometheus, ELK for full-stack metrics, traceability, and alerting.

**Why:**  
- Proactive failure detection, root cause analysis, supports >99.99% uptime SLAs.

## 9. Autoscaling, Operations, and Cost Control

**What:**  
- Automatic scaling of pods/VMs, database read replicas, and cache nodes.
- OCI Budgets for financial guardrails.

**Why:**  
- Balances performance SLAs with efficient resource use, responding to traffic surges or regional demand.

## 10. Sample Scale & Resource Estimation Table

| Resource/Layer          | Per Region     | Global Total (6 regions) |
|------------------------|----------------|--------------------------|
| VCNs                   | 1              | 6                        |
| Subnets (all types)    | 6              | 36                       |
| Load Balancers         | 4              | 24                       |
| RDBMS Clusters         | 1              | 6                        |
| NoSQL Clusters         | 2              | 12                       |
| Caching Clusters       | 2              | 12                       |
| Analytics Warehouse    | 1              | 6                        |
| Object Storage (TB)    | 1,000+         | 6,000+                   |
| App Microservices      | 500–1,000 pods | 6,000+ pods              |
| Admin Jump Servers     | 1–2            | 6–12                     |
| Monitoring Nodes       | 2-3            | 12-18                    |

## 11. User/Application Flow (Textual Diagram)

1. **User Request** enters global Load Balancer in closest region.
2. Routed to API Gateway (Public Subnet).
3. API Gateway forwards to application pods (Private App Subnet), in the user’s language preference.
4. App pods access cache (for hot data), then RDBMS/NoSQL (user records, preferences, catalog).
5. Streaming pods serve media files direct from Object Storage & CDN for ultra-low latency playback.
6. All system operations, backups, and logs flow into the Restricted/Admin subnet for secure monitoring and disaster recovery.

## 12. Summary of Subnet Rationale

- **Public**: Exposed, protected front-end.  
- **Private (App/DB/Analytics)**: Core business logic, never internet reachable.  
- **Restricted/Admin**: Safety boundary for management, monitoring, and operational continuity—critical for both compliance and business assurance.
