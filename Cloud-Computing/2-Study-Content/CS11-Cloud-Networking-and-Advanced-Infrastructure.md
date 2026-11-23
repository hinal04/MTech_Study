# Cloud Networking and Advanced Infrastructure - Session 11

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**

---

## Table of Contents

1. [Cloud Networking Fundamentals](#cloud-networking-fundamentals)
2. [Virtual Private Cloud (VPC) Design](#virtual-private-cloud-vpc-design)
3. [Hybrid Cloud Connectivity](#hybrid-cloud-connectivity)
4. [Content Delivery Networks (CDN)](#content-delivery-networks-cdn)
5. [DNS and Global Load Balancing](#dns-and-global-load-balancing)
6. [Network Security and DDoS](#network-security-and-ddos)
7. [Advanced Networking Concepts](#advanced-networking-concepts)

---

## Cloud Networking Fundamentals

### Networking in Traditional Data Centers

**Challenges:**
- Fixed topology: Physical rack layout determines connectivity
- Scaling: Adding capacity requires recabling
- Latency: Constrained by physical distance
- Cost: Expensive network hardware
- Management: Complex manual configuration

### Cloud Networking Innovation

**Benefits:**
- Virtualized: Software-defined networking (SDN)
- Elastic: Scale network capacity on-demand
- Flexible: Configure topology via API
- Cost-effective: Pay per GB transferred
- Global: Connect across continents

### OSI Model Review (Application Focus)

| Layer | Name | Examples | Cloud Relevance |
|-------|------|----------|-----------------|
| 7 | Application | HTTP, DNS, SMTP | Load balancing, CDN |
| 6 | Presentation | Encryption, compression | TLS/SSL |
| 5 | Session | Connection management | Sticky sessions |
| 4 | Transport | TCP, UDP | ALB, NLB |
| 3 | Network | IP, routing | VPC routing, BGP |
| 2 | Data Link | Ethernet, MAC | Virtual switches |
| 1 | Physical | Fiber, copper | Data center links |

---

## Virtual Private Cloud (VPC) Design

### VPC Architecture

**Single VPC:**
```
AWS Account (us-east-1 Region)
│
└─ VPC: 10.0.0.0/16
   │
   ├─ Availability Zone (us-east-1a)
   │  └─ Public Subnet: 10.0.1.0/24
   │  └─ Private Subnet: 10.0.11.0/24
   │
   ├─ Availability Zone (us-east-1b)
   │  └─ Public Subnet: 10.0.2.0/24
   │  └─ Private Subnet: 10.0.12.0/24
   │
   └─ Internet Gateway (IGW): Connects to internet
   └─ NAT Gateway: Private to internet (one-way)
```

### Multi-VPC Architecture

**Use Case:** Separate environments, teams, or tenants

```
Account 1 (Production)      Account 2 (Development)
│                           │
└─ VPC-Prod: 10.0.0.0/16   └─ VPC-Dev: 10.1.0.0/16
   │                           │
   ├─ Resources                ├─ Resources
   └─ Peering Connection ◄────► (VPC Peering)
```

**Benefits:**
- Isolation: Production issues don't affect dev
- Blast radius: Misconfiguration contained
- Compliance: Separate audit trails
- Cost allocation: Track per environment

### Subnet Design

**Public Subnet:**
- Route table: 0.0.0.0/0 → Internet Gateway
- Instances: Get public IP addresses
- Use case: Web servers, load balancers, NAT gateways

**Private Subnet:**
- Route table: 0.0.0.0/0 → NAT Gateway (optional)
- Instances: No public IP
- Use case: Databases, application servers, caches

**Example Web Tier (Public):**
```
Route Table:
  Destination       Target
  10.0.0.0/16      Local
  0.0.0.0/0        igw-1234 (Internet Gateway)

Result:
✓ Instance can reach other VPC resources
✓ Instance can reach internet
```

**Example DB Tier (Private):**
```
Route Table:
  Destination       Target
  10.0.0.0/16      Local
  0.0.0.0/0        nat-5678 (NAT Gateway)

Result:
✓ Instance can reach other VPC resources
✓ Instance can initiate outbound connections (via NAT)
✗ Internet cannot initiate inbound connections
```

### VPC Peering vs Transit Gateway

**VPC Peering:**
```
VPC-A ◄──► VPC-B  (Direct connection)
Cost: $0.01/GB transferred
Limitation: Doesn't scale (many VPCs = many connections)
```

**Transit Gateway:**
```
       ┌─────────────────┐
       │ Transit Gateway │
       └────────┬────────┘
                │
       ┌────────┼────────┐
       │        │        │
   VPC-A    VPC-B    VPC-C
Cost: $0.05/GB transferred
Benefit: Hub-and-spoke topology, scales to 100+ VPCs
```

---

## Hybrid Cloud Connectivity

### Hybrid Cloud Architecture

**Definition:** Combine on-premise infrastructure with cloud

```
┌──────────────────┐          Internet          ┌────────────────────┐
│ Corporate       │◄──────── VPN/Direct ───────►│ AWS VPC            │
│ Data Center     │          Connection          │ (10.0.0.0/16)      │
│                 │                               │                    │
│ ├─ Servers      │                               │ ├─ EC2 Instances  │
│ ├─ Databases    │                               │ ├─ RDS Databases  │
│ ├─ Applications │  Synchronized Data            │ ├─ S3 Storage     │
│ └─ IP: 192.168  │◄────────────────────────────► │ ├─ DynamoDB       │
│    /16          │                               │ └─ Lambda         │
└──────────────────┘                               └────────────────────┘

Use Cases:
- Migrate gradually: Run app partly on-prem, partly in cloud
- Burst capacity: Use cloud for peak loads
- Compliance: Keep sensitive data on-prem
- Latency: Distribute processing geographically
```

### VPN Connection (Site-to-Site)

**Components:**
- **Virtual Private Gateway (VGW):** AWS side endpoint
- **Customer Gateway:** On-premise side endpoint
- **VPN Connection:** Encrypted tunnel

**Setup:**
```
On-Prem Gateway (BGP 65000)
    │
    │ IPSec Tunnel
    │ Encrypted
    ▼
AWS Virtual Private Gateway (BGP 64512)
    │
    └─ Routes traffic to VPC resources
```

**Data Flow:**
```
1. On-prem app sends packet to 10.0.0.0/16 (AWS VPC)
2. Customer gateway intercepts
3. Encrypts with IPSec
4. Sends through internet tunnel
5. VGW decrypts
6. Routes to destination EC2 instance
7. Response follows reverse path
```

**Characteristics:**
- **Encryption:** IPSec (AES-256)
- **Throughput:** 1.25 Gbps per tunnel
- **Latency:** Internet-dependent (typically 50-200ms)
- **Cost:** $0.05/hour + $0.09/GB transferred

### AWS Direct Connect

**Definition:** Dedicated physical network connection to AWS

```
┌─────────────────────┐
│ Corporate Office    │
│ IP: 192.168.0.0/16  │
└──────────┬──────────┘
           │
           │ Dark fiber (private connection)
           │ 1Gbps - 100Gbps
           │ No congestion
           │
┌──────────▼──────────┐
│ AWS Direct Connect  │
│ Location            │
└──────────┬──────────┘
           │
           │ AWS backbone (private)
           │ No internet hop
           │
┌──────────▼──────────┐
│ AWS VPC             │
│ 10.0.0.0/16         │
└─────────────────────┘
```

**vs VPN:**
| Aspect | VPN | Direct Connect |
|--------|-----|---|
| Encryption | IPSec | Optional |
| Latency | Variable | Consistent |
| Bandwidth | Up to 1.25 Gbps | Up to 100 Gbps |
| Cost | $0.05/hour | $0.30/hour + setup |
| Setup Time | Minutes | Weeks |
| Reliability | Internet-dependent | 99.99% SLA |

**Use Case:** Financial institutions transferring large datasets require Direct Connect's consistent performance.

---

## Content Delivery Networks (CDN)

### CDN Motivation

**Problem:** Download large file from distant server

```
User in Sydney downloading video from server in New York
├─ Physical distance: ~16,000 km
├─ Speed of light travel time: ~53 ms
├─ Latency: Likely 200+ ms (round-trip)
├─ Throughput: Limited by cross-Atlantic capacity
└─ Experience: Slow, buffering, poor UX
```

**Solution:** CDN edge locations near users

```
┌─────────────────────────────────────┐
│ Origin Server (New York)            │
│ Master copy of content              │
└──────────────┬──────────────────────┘
               │
      ┌────────┴────────┐
      │                 │
┌─────▼─────┐    ┌─────▼─────┐
│ Edge PoP  │    │ Edge PoP  │
│ (Sydney)  │    │ (London)  │
│ Cache     │    │ Cache     │
└─────▲─────┘    └─────┬─────┘
      │                │
  User in           User in
  Sydney            London
  
  5ms latency        2ms latency
  Good UX!         Excellent UX!
```

### CDN Architecture

**Components:**
1. **Origin Server:** Master copy (AWS, your data center)
2. **Edge Locations (PoPs):** Regional caches (CloudFront has 450+ PoPs)
3. **Regional Edge Caches:** Intermediate layer
4. **Viewers:** End users requesting content

**Request Flow:**
```
1. User requests: GET /video/movie.mp4
2. Browser checks: Is it in cache?
3. If not, request to nearest edge PoP
4. Edge PoP checks: Is video in my cache?
5. If not, fetch from origin (backhaul)
6. Edge caches for TTL (Time-To-Live)
7. Serve to user
8. Next user gets from cache (cache hit)
```

### CDN Cache Control

**TTL (Time-To-Live):**
```
Scenario 1: News website (content changes hourly)
└─ Set TTL: 1 hour
└─ Cache hit rate: ~95%
└─ Origin load: Minimal

Scenario 2: Streaming service (content static for months)
└─ Set TTL: 365 days
└─ Cache hit rate: ~99%+
└─ Origin load: Negligible
```

**Invalidation:**
```
If you update /video/movie.mp4:
1. Manual invalidation: Tell CDN to purge cache
2. Wait for TTL: Next cache expiry (slow)
3. Versioned URLs: /video/movie-v2.mp4 (immediate)
```

**Cost Considerations:**
- Regional variation: Egress from US-East cheaper than Europe
- Origin Shield: Extra caching layer (~25% cost increase, reduces origin load)
- Compression: Gzip reduces bandwidth by 60-80%

---

## DNS and Global Load Balancing

### DNS Fundamentals

**Problem:** 93.184.216.34 is hard to remember. Use domain name instead.

**Solution:** Domain Name System (DNS)

```
1. Browser: "What's the IP for example.com?"
   │
2. Local DNS Resolver: "Let me ask the nameservers"
   │
3. Recursive Query:
   ├─ Root Nameserver: "Ask .com TLD server"
   ├─ TLD Server: "Ask authoritative server for example.com"
   ├─ Authoritative: "IP is 93.184.216.34"
   │
4. Resolver returns IP to browser
   │
5. Browser connects to 93.184.216.34
```

**DNS Record Types:**
- **A:** IPv4 address (93.184.216.34)
- **AAAA:** IPv6 address (2606:2800:220:1:248:1893:25c8:1946)
- **CNAME:** Alias (www.example.com → example.com)
- **MX:** Mail server
- **TXT:** Text record (SPF, DKIM for email authentication)

### Global Load Balancing with DNS

**Use Case:** Route users to nearest data center

```
Authoritative DNS Server (Route 53)
├─ Geolocation Routing:
│  ├─ User in USA → 203.0.113.1 (US data center)
│  ├─ User in Europe → 198.51.100.1 (EU data center)
│  └─ User in Asia → 192.0.2.1 (Asia data center)
│
└─ Health Checks:
   ├─ US server healthy? YES → Route to it
   ├─ US server unhealthy? NO → Route to backup
   └─ Failover automatic
```

**DNS Failover:**
```
Normal:
www.example.com → 203.0.113.1 (Primary)

Primary fails (health check):
www.example.com → 198.51.100.1 (Secondary)

Result: Automatic geographic failover
```

### Application Load Balancer (ALB) vs Network Load Balancer (NLB)

**ALB (Layer 7 - Application):**
```
┌─────────────────────────────────┐
│ ALB (Port 80, 443)              │
│ Examine HTTP headers, paths     │
└──────────┬──────────────────────┘
           │
      ┌────┼────┬─────┐
      │    │    │     │
      ▼    ▼    ▼     ▼
  Prod  Dev  API  Admin
  Pool  Pool  Pool  Pool
  
Router rules:
- GET /api/* → API Pool
- GET /admin/* → Admin Pool (admin IPs only)
- GET / → Prod Pool
```

**NLB (Layer 4 - Transport):**
```
┌─────────────────────────────────┐
│ NLB (Port 443)                  │
│ Examine IP, port only           │
│ Ultra-high performance          │
└──────────┬──────────────────────┘
           │
      ┌────┼─────────┐
      │    │         │
      ▼    ▼         ▼
  Gaming  IoT  Real-time
  Pool    Pool  Comms Pool
  
Throughput: 7M packets/sec (vs ALB 25K req/s)
Latency: Sub-millisecond
```

---

## Network Security and DDoS

### Advanced DDoS Mitigation

**Volumetric Attack Prevention:**
```
Incoming flood: 500 Gbps UDP packets

Traditional approach:
- Try to process: Fail
- Upgrade bandwidth: Expensive

CloudFront + AWS Shield approach:
1. CloudFront distributed globally (can absorb 300+ Gbps)
2. Scrub traffic at edge (filter malicious patterns)
3. Origin only sees clean traffic
4. Origin unaffected
```

**Rate Limiting Example:**
```
AWS WAF Rule:
- If IP sends >100 requests/sec
- Then block for 5 minutes

Result:
- Legitimate users (10 req/sec): Allowed
- Bot attack (10,000 req/sec): Blocked
```

### Advanced Networking Concepts

**Equal-Cost Multi-Path (ECMP):**
```
┌─────────────────────────────────┐
│ To Route Data Center D1         │
│ 4 equal-cost paths available    │
└──────────┬──────────────────────┘
           │
      ┌────┼────┬─────┬─────┐
      │    │    │     │     │
      ▼    ▼    ▼     ▼     ▼
      25%  25%  25%   25%  
   Path1 Path2 Path3 Path4
   
Result: 4x bandwidth aggregation
        Low latency (independent paths)
        Fault tolerance (if 1 path fails, use 3)
```

**BGP (Border Gateway Protocol):**
- Routing protocol for the internet
- Autonomous Systems (ASes) exchange route information
- AWS announces its IP ranges via BGP
- Cloud providers manipulate BGP for traffic engineering

---

## Summary

**Key Networking Concepts:**
- **VPCs:** Virtualized networks, subnet design, isolation
- **Connectivity:** VPN for cost-effective, Direct Connect for performance
- **Global Reach:** CDN for content, DNS for routing, ALB for distribution
- **Security:** DDoS protection, rate limiting, WAF
- **Performance:** Global load balancing, edge caching, low-latency delivery

**Design Principle:** Match networking solution to workload requirements (cost vs performance vs reliability)

