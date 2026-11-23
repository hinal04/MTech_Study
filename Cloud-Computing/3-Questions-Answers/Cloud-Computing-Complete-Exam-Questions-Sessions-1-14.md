# Cloud Computing - Complete Exam Questions
## CSI ZG527 / SE ZG527 / SS ZG527 - Sessions 1-14 Practical Scenario-Based Questions
## 25+ Questions for Complete Exam Preparation

---

## **SECTION A: SESSIONS 1-4 (Fundamentals to IaaS)**

### **Q1. Cloud Adoption Decision: Startup vs On-Premise (8 marks)**

**Scenario:**
A startup with 50 employees plans to build a SaaS platform. They must choose between:
- Option A: Build on-premise data center
- Option B: Migrate to AWS cloud

**Current situation:**
- Estimated initial users: 1,000
- Projected growth: 10x in 2 years
- Budget: $100K initial investment
- Team: 2 engineers, 1 DevOps person

**Requirements:**
- 99.9% uptime
- Global access (India + US customers)
- Expected data: 1 TB total
- Transactions: 100/sec average, 500/sec peak

**Questions:**

(a) **Analyze NIST cloud characteristics. Which would benefit the startup most? Why?** (2 marks)

(b) **Calculate 3-year TCO for both options. Show assumptions.** (3 marks)

(c) **Which deployment model (Public/Private/Hybrid) would you recommend? Justify.** (2 marks)

(d) **What are the risks if staying on-premise with 10x growth projected?** (1 mark)

---

### **Q2. Virtualization Architecture Decision (8 marks)**

**Scenario:**
Company currently runs 500 virtual machines on VMware vSphere (Type 1 hypervisor). They want to migrate to AWS but are concerned about:
- Performance impact
- Hypervisor compatibility
- Cost differences
- Migration complexity

**Current environment:**
- 500 VMs, 70% utilization average
- Mixed workloads (web, database, batch processing)
- Current hypervisor: VMware ESXi

**Questions:**

(a) **Explain the difference between Type 1 and Type 2 hypervisors. Why did company use Type 1?** (2 marks)

(b) **Analyze: Paravirtualization vs Full Virtualization in AWS context.** (2 marks)

(c) **Design migration strategy for 500 VMs. What tools would you use?** (2 marks)

(d) **If 100 VMs are performance-critical, should they use Dedicated Instances? Why/why not?** (2 marks)

---

### **Q3. IaaS Architecture for E-Commerce Platform (8 marks)**

**Scenario:**
Design AWS IaaS architecture for e-commerce with:
- 100 million products
- 1 million concurrent users
- Global users (India, US, Europe)
- Real-time inventory
- Payment processing
- Order tracking

**Non-functional requirements:**
- 99.99% availability
- <100ms latency globally
- Automatic scaling
- Secure payment data

**Questions:**

(a) **Design the four-layer IaaS stack (Hardware/Virtualization/Resource Management/Service Management).** (3 marks)

(b) **For compute layer, recommend instance type and auto-scaling policy. Justify.** (2 marks)

(c) **Implement IAM with least privilege for 3 teams: Developers, Ops, Finance. Show policies.** (2 marks)

(d) **Handle global latency requirement using AWS regions. How would you route traffic?** (1 mark)

---

### **Q4. IaaS Services Integration (8 marks)**

**Scenario:**
Build a data analytics platform on AWS IaaS:

**Data sources:**
- 10 TB of data stored in S3 (logs, transactions)
- Real-time data from IoT devices (100K sensors)
- Database with 1 billion records (customer data)
- Streaming: 10K events/second

**Processing requirements:**
- Complex queries across all sources
- Dashboard updates (sub-second)
- Monthly reports
- Price: Under $5K/month

**Questions:**

(a) **Choose appropriate services for each layer: Compute, Storage, Data, Networking.** (3 marks)

(b) **Design data architecture: S3 (data lake), RDS (operational DB), DynamoDB (real-time). Why each?** (2 marks)

(c) **Implement access control: Encrypt sensitive data, audit all access, least privilege.** (2 marks)

(d) **Calculate cost breakdown. Where would you optimize?** (1 mark)

---

## **SECTION B: SESSIONS 5-7 (Services to Orchestration)**

### **Q5. EC2 and Networking Design (8 marks)**

**Scenario:**
Multi-tier application with:
- Web tier: 100 concurrent users, static content
- App tier: Business logic, 10 connections/user
- Database tier: 1 TB PostgreSQL

**Current issues:**
- Web requests slow (1 second)
- Database connections failing randomly
- Security: Some developers have EC2 access they don't need

**Questions:**

(a) **Design VPC with public/private subnets. Show CIDR ranges, NAT gateway placement.** (3 marks)

(b) **Configure security groups for 3-tier architecture. What traffic allowed/blocked?** (2 marks)

(c) **Implement Auto-scaling: When to scale web tier? What metric and threshold?** (2 marks)

(d) **Performance: Why request slow? Implement solution (ALB, caching, etc.).** (1 mark)

---

### **Q6. Container Deployment with Kubernetes (8 marks)**

**Scenario:**
Microservices application (10 services) must run on Kubernetes:
- Order service
- Payment service
- Inventory service
- Notification service
- etc.

**Requirements:**
- Each service: Different scaling needs
- Rolling deployments (zero downtime)
- Cross-service communication
- Persistent data for some services

**Current state:**
- Running on Docker locally
- Need to move to production Kubernetes

**Questions:**

(a) **Design Kubernetes architecture: Nodes, pods, deployments, services. How many replicas per service?** (3 marks)

(b) **Implement networking: How do services discover each other? Load balancing?** (2 marks)

(c) **Handle stateful services (database, cache): What Kubernetes primitives? PersistentVolumes?** (2 marks)

(d) **Deployment strategy: Show rolling update procedure. How handle failure during deployment?** (1 mark)

---

### **Q7. PaaS and SaaS Selection (7 marks)**

**Scenario:**
Company needs to quickly launch a customer-facing portal (web app + mobile app) in 2 weeks.

**Decision: Build custom or use PaaS/SaaS?**

**Build custom (IaaS approach):**
- Provision servers
- Configure network
- Deploy application
- Manage updates
- Handle scaling
- Estimated time: 8 weeks

**Use Elastic Beanstalk (PaaS):**
- Upload application
- Configure environment
- Deploy
- Automatic scaling, updates, monitoring
- Estimated time: 2 weeks

**Use Firebase (BaaS):**
- Write frontend
- Use Firebase backend (database, auth, storage)
- Deploy
- Estimated time: 1 week

**Questions:**

(a) **Given 2-week deadline, which approach (IaaS/PaaS/BaaS)? Justify trade-offs.** (2 marks)

(b) **If using Elastic Beanstalk: Configure environment for 1M users, global distribution.** (2 marks)

(c) **If using Firebase: What limitations compared to custom solution? When acceptable?** (2 marks)

(d) **Migration path: If outgrow PaaS, how to migrate to IaaS with minimal disruption?** (1 mark)

---

## **SECTION C: SESSIONS 8-11 (Security, Networking, Advanced Topics)**

### **Q8. Cloud Security and Compliance (8 marks)**

**Scenario:**
Healthcare company storing patient medical records (HIPAA regulated) on AWS.

**Data sensitivity:**
- Patient names, SSN: Highly sensitive
- Medical history: Sensitive
- Treatment plans: Sensitive
- Insurance info: Sensitive

**Compliance requirements:**
- HIPAA: Encrypt PHI, audit logs, access control
- Data breach: Notify within 60 days
- Data retention: Keep 7 years, then delete

**Attack vectors:**
- Insider threat: Admin could access data
- External attack: Hacker breaks into system
- Data loss: Accidental deletion
- Ransomware: Files encrypted, ransom demanded

**Questions:**

(a) **Design encryption strategy: At-rest, in-transit, in-use. Which data needs what level?** (3 marks)

(b) **Implement IAM with MFA, audit logging, least privilege for doctors, nurses, admins.** (2 marks)

(c) **Disaster recovery + compliance: Backup strategy for 7-year retention. Cost?** (2 marks)

(d) **Incident response: If breach detected, what's your 72-hour notification plan?** (1 mark)

---

### **Q9. Advanced Networking with CDN (8 marks)**

**Scenario:**
Video streaming service (Netflix-like) with:
- 100M hours watched/day globally
- Users in India, USA, Europe
- 500 TB of video content
- Peak: 1M concurrent streams

**Current problem:**
- Download latency: 2-5 seconds
- Bandwidth costs: $500K/month
- Geographic bottleneck: Central server in US, users everywhere

**Solution:** Implement CDN + Global Load Balancing

**Questions:**

(a) **Design CDN architecture using CloudFront. How many edge locations? Cache TTL?** (2 marks)

(b) **Implement global load balancing: Route users to nearest edge location. Use Route 53 geolocation routing.** (2 marks)

(c) **Cost analysis: CDN investment vs bandwidth savings. Break-even point?** (2 marks)

(d) **Failover strategy: If US edge location fails, how route traffic to backup?** (2 marks)

---

### **Q10. Hybrid Cloud and Connectivity (7 marks)**

**Scenario:**
Financial company with on-premise data center (IP: 192.168.0.0/16) needs hybrid cloud:
- Sensitive data stays on-premise
- Non-sensitive data in AWS (10.0.0.0/16)
- Applications split between both

**Requirements:**
- Latency: <50ms across hybrid network
- Bandwidth: 10 Gbps sustained
- Reliability: 99.95% uptime
- Encryption: All data in transit encrypted

**Questions:**

(a) **Compare VPN vs Direct Connect. For this scenario, which? Why?** (2 marks)

(b) **Design site-to-site VPN: Configure customer gateway, VGW, routing. Show CIDR design.** (2 marks)

(c) **Implement automatic failover: If primary connection fails, switch to backup (secondary path).** (2 marks)

(d) **Disaster recovery: If on-prem destroyed, how recover critical data from AWS? RTO/RPO?** (1 mark)

---

## **SECTION D: SESSIONS 12-14 (Serverless, Big Data, Operations)**

### **Q11. Serverless Architecture for Event Processing (8 marks)**

**Scenario:**
Real-time event processing system:
- 1 million events/second (user actions, clicks, page views)
- Need to: Aggregate, detect anomalies, trigger alerts
- Must process instantly
- Low cost (startup budget: $100K/month max)

**Technology options:**
- Option A: 100 EC2 instances ($10K/month)
- Option B: Kubernetes cluster ($8K/month)
- Option C: Serverless (Lambda + Kinesis) (estimated $2K/month)

**Questions:**

(a) **Why serverless best for this scenario? Compare cost, ops burden, scaling.** (2 marks)

(b) **Design serverless architecture: Kinesis → Lambda → DynamoDB. How many shards? Lambda concurrency?** (2 marks)

(c) **Implement event-driven pipeline: Detect anomalies, store alerts, notify users. Show data flow.** (2 marks)

(d) **Handle failure: What if Lambda crashes? Implement retry, DLQ (dead-letter queue).** (2 marks)

---

### **Q12. Big Data Analytics Platform (8 marks)**

**Scenario:**
Build analytics for 10B+ records (10 years of transactions):

**Data:**
- Size: 100 TB raw
- Structure: Transactions, customers, products, locations
- Queries: "Top 100 products by revenue in region, compare YoY"

**Current challenges:**
- PostgreSQL: Can't handle data size
- Query time: 30 minutes (too slow for dashboards)
- Cost: Expensive storage

**Questions:**

(a) **Choose technology: Redshift vs EMR vs BigQuery? Why? Cost per month?** (2 marks)

(b) **Design data warehouse: Star schema with fact and dimension tables.** (2 marks)

(c) **Optimize query performance: Indexing, compression, partitioning strategy.** (2 marks)

(d) **Implement: Can add new data source (IoT sensors, 100K/sec streaming)? Architecture change?** (2 marks)

---

### **Q13. Stream Processing for Real-Time Analytics (8 marks)**

**Scenario:**
Stock exchange feeds real-time trades (100K/sec). Need:
- Real-time dashboards (updated every second)
- Alert: If stock moves >5% in 1 minute
- Track portfolio value (sum of holdings × price)

**Questions:**

(a) **Why batch processing inadequate? Design streaming solution: Kinesis → Lambda → Redis cache.** (2 marks)

(b) **Implement 1-minute windowing: Detect >5% moves. Show pseudocode.** (2 marks)

(c) **Maintain state: Track customer holdings. If Lambda crashes, what happens to state? Recovery?** (2 marks)

(d) **Scale to 1M concurrent portfolios being tracked. How optimize? Memory, latency?** (2 marks)

---

### **Q14. Cloud Cost Optimization and Operations (8 marks)**

**Scenario:**
Company currently pays $50K/month for AWS. CEO demands: "Reduce by 25% without impacting performance."

**Current infrastructure:**
- 200 EC2 instances: $20K
- 50 TB RDS: $15K
- 100 TB S3: $5K
- Data transfer: $5K
- Other: $5K

**Unknown issues:**
- Some instances undersized, others oversized
- No Reserved Instances purchased
- Unused storage not cleaned up
- Data transfer excessive

**Questions:**

(a) **Analyze: Where are "quick wins" for cost reduction? Prioritize efforts.** (2 marks)

(b) **Right-sizing: 20% of instances are oversized. Expected savings if fixed?** (2 marks)

(c) **Reserved Instances: Purchase 1-year RIs for 60% of baseline. Savings vs On-Demand?** (2 marks)

(d) **Final plan: Show how reach 25% reduction. What's not done and why?** (2 marks)

---

## **SECTION E: MEGA INTEGRATION SCENARIOS**

### **Q15. Complete Cloud Migration: Enterprise On-Prem to AWS (10 marks)**

**Scenario:**
Financial services company: 500 employees, 2000 VMs, $50M revenue/year

**Current infrastructure:**
- Primary: Datacenter in New York (3000 sq ft, 500 servers)
- DR: Datacenter in New Jersey (standby, 10% of primary)
- Network: Private WAN between locations
- Compliance: SOC 2 Type II certified

**Migration goal:**
- Shut down physical datacenters
- Move to AWS (Multi-region, multi-AZ)
- Maintain SOC 2 compliance
- Zero downtime for critical systems
- Timeline: 18 months

**Current pain points:**
- Datacenter rent: $500K/year
- Staff (40 ops people): $5M/year
- Hardware aging (5-year lifespan expiring)
- Scaling requires 2-3 month hardware procurement

**Questions:**

(a) **Calculate 5-year TCO: On-premise (renewal cycle) vs AWS. Justify choice.** (3 marks)

(b) **Design migration approach: 3-phase (Plan, Migrate, Optimize). Timeline for each phase.** (3 marks)

(c) **Post-migration: Ops model change. How many people needed? What roles?** (2 marks)

(d) **Risk mitigation: Identify 5 risks and mitigation strategy for each.** (2 marks)

---

### **Q16. Global SaaS Architecture on AWS (10 marks)**

**Scenario:**
Build global SaaS application (like Salesforce):
- Multi-tenant (100K customers)
- Regions: US, EU, APAC
- Requirements: <50ms latency, 99.99% SLA, automatic scaling, compliance (GDPR, SOC2)
- Estimated scale: 10M active users, 1B transactions/day

**Technology choices required:**
- Compute: Serverless vs Containers?
- Database: SQL vs NoSQL? Single region or multi-region?
- Storage: S3? DynamoDB? ElastiCache?
- Security: Encryption, MFA, audit logging?

**Questions:**

(a) **Design complete architecture: Compute, database, storage, networking layers.** (3 marks)

(b) **Multi-region deployment: Replicate infrastructure across 3 regions. How sync data?** (2 marks)

(c) **Compliance (GDPR): EU user data must stay in EU region. Implement data residency.** (2 marks)

(d) **Disaster recovery + business continuity: 15-min RTO, 1-hour RPO. Design backup strategy.** (3 marks)

---

### **Q17. ML Pipeline on Cloud: From Data to Production (9 marks)**

**Scenario:**
Build recommendation engine using ML:

**Data:**
- 100M user profiles (behaviors, preferences)
- 10M product catalog
- 100B user-product interactions
- 1M new interactions/day

**Goal:**
- Recommend products to users in real-time
- Improve recommendations over time
- Sub-100ms latency for recommendations
- Scale to 100M concurrent users

**Questions:**

(a) **Design data pipeline: S3 (data lake) → preprocessing → training → deployment.** (3 marks)

(b) **Training: Use SageMaker with Spark on EMR. Training time / cost estimates for 100B records?** (2 marks)

(c) **Serving: Deploy model endpoint for real-time predictions. Performance and cost?** (2 marks)

(d) **Monitoring: How detect model degradation? Retraining strategy?** (2 marks)

---

### **Q18. FinOps Implementation: Cost Management Across Organization (8 marks)**

**Scenario:**
Large enterprise: $500M/year revenue, 10K employees, 50 teams

**Current cloud spend:** $5M/month ($60M/year)

**Problems:**
- Teams don't see their own costs
- No budget controls
- Waste: Idle resources, unused services
- No visibility into cost drivers

**Goal:** Implement FinOps framework to reduce spend to $3.5M/month (30% reduction)

**Questions:**

(a) **Design cost allocation: Tags, resource naming, chargeback model.** (2 marks)

(b) **Identify 5 cost optimization opportunities. Expected savings for each.** (2 marks)

(c) **Implement budget controls: Alerting, enforcement, approval workflows.** (2 marks)

(d) **Organizational structure: Create FinOps function. Roles, responsibilities, reporting lines.** (2 marks)

---

## Study Guide

**Topics by Priority:**

**Must Know (Exam Basics):**
- NIST 5 characteristics of cloud
- IaaS vs PaaS vs SaaS (differences and use cases)
- Hypervisors (Type 1 vs Type 2)
- Virtualization benefits/limitations

**High Priority (Exam Core):**
- AWS services (EC2, S3, RDS, Lambda, Kinesis)
- Networking (VPC, security groups, routing)
- Security (IAM, encryption, compliance)
- Scaling and auto-scaling

**Important (Scenarios & Integration):**
- Multi-tier architecture design
- Disaster recovery and business continuity
- Cost optimization strategies
- Migration planning

**Nice to Have (Advanced):**
- Advanced networking (CDN, global load balancing)
- Kubernetes orchestration details
- Big data processing frameworks
- ML pipeline architecture

**Exam Strategy:**

1. **Read scenario carefully** - Understand context and constraints
2. **Identify constraints** - Cost, latency, compliance, scale
3. **Choose right tools** - Match technology to requirements
4. **Design architecture** - Show data flow, components, scaling
5. **Justify choices** - Explain trade-offs and alternatives
6. **Draw diagrams** - Visualize architecture when possible
7. **Estimate costs** - Calculate cloud spend for designs

**Total Questions:** 18 mega-scenarios (25-30+ marks equivalent)
**Recommended Study Time:** 20-25 hours
**Format:** Each question requires design thinking, not memorization

