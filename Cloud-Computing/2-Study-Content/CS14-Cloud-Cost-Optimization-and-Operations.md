# Cloud Cost Optimization and Operations - Session 14

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**

---

## Table of Contents

1. [Cloud Cost Fundamentals](#cloud-cost-fundamentals)
2. [Cost Modeling and Analysis](#cost-modeling-and-analysis)
3. [Cost Optimization Strategies](#cost-optimization-strategies)
4. [FinOps Framework](#finops-framework)
5. [Cloud Operations](#cloud-operations)
6. [Monitoring and Governance](#monitoring-and-governance)

---

## Cloud Cost Fundamentals

### On-Premise vs Cloud Cost Structure

**On-Premise Data Center:**
```
Capital Expenditure (CapEx):
├─ Servers: $2 million (upfront)
├─ Storage: $500,000
├─ Networking equipment: $300,000
└─ Total: $2.8 million initial investment

Operational Expenditure (OpEx):
├─ Staff (5 people): $750,000/year
├─ Power/Cooling: $150,000/year
├─ Maintenance: $200,000/year
├─ Upgrades: $500,000/year
└─ Total: $1.6 million/year

Over 5 years:
└─ Total cost: $2.8M + (1.6M × 5) = $10.8 million

Challenges:
├─ Large upfront investment (risk)
├─ Capacity planning (over/under provisioned)
├─ Long procurement cycles (6-12 months)
├─ Aging infrastructure replacement
```

**Cloud (AWS):**
```
Capital Expenditure (CapEx):
└─ $0 (no servers purchased)

Operational Expenditure (OpEx):
├─ Compute (200 instances × $0.10/hour): $14,400/year
├─ Storage (100 TB × $0.023/GB): $2,300/year
├─ Data transfer: $1,000/year
├─ Lambda/Serverless: $500/year
└─ Total: ~$18,200/year

Benefit:
├─ Pay-as-you-go (no large upfront)
├─ Scalable (add resources instantly)
├─ No staff needed (AWS manages)
└─ Total cost/year: 1/4 of on-prem
```

### AWS Pricing Models

**On-Demand:**
```
EC2 t2.medium:
├─ Cost: $0.0464/hour
├─ Usage: Pay for every hour
├─ Flexibility: Stop anytime
├─ Best for: Unpredictable workloads
└─ Example: 730 hours/month = $33.87/month
```

**Reserved Instances (RI):**
```
EC2 t2.medium (1-year):
├─ Cost: $0.0334/hour (28% discount)
├─ Commitment: Pay for 1 year upfront or monthly
├─ Flexibility: Limited
├─ Best for: Baseline capacity
└─ Example: $0.0334 × 730 = $24.38/month

vs. On-Demand:
└─ Savings: $33.87 - $24.38 = $9.49/month (28%)
```

**Savings Plans:**
```
1-year commitment:
├─ Compute savings plan: 20% discount across instance family
├─ EC2 instance savings plan: 25% discount (specific instance)
├─ Database savings plan: 20% discount (RDS, Redshift, etc.)
└─ Flexibility: Can change instance type/region/OS

Recommendation:
├─ 70% of workload: Reserved Instance (predictable)
├─ 20% of workload: Savings Plan (variable)
└─ 10% of workload: On-Demand (peaks)
```

**Spot Instances:**
```
EC2 t2.medium (Spot):
├─ Cost: $0.0139/hour (70% discount!)
├─ Catch: AWS can reclaim with 2-minute warning
├─ Best for: Fault-tolerant, batch processing
├─ Example: Hadoop, rendering, non-critical tasks

Use Case:
├─ EMR cluster: Can be stopped mid-job
├─ Batch processing: Can retry later
├─ NOT for: Real-time services
```

---

## Cost Modeling and Analysis

### Total Cost of Ownership (TCO) Calculator

**Example: Migrate 1000 VMs from on-prem to AWS**

**On-Premise (3-year TCO):**
```
Infrastructure:
├─ Servers: $5 million (1000 × $5,000)
├─ Storage: $1 million
├─ Networking: $500,000
├─ Virtualization software: $300,000
└─ Subtotal: $6.8 million

Operations (3 years):
├─ IT staff (10 FTE × $120,000): $3.6 million
├─ Facility (power, cooling, rent): $1.2 million
├─ Maintenance contracts: $600,000
└─ Subtotal: $5.4 million

Depreciation:
└─ Salvage value (year 3): ($6.8M - 20% salvage) = $5.4M loss

Total 3-Year Cost: $6.8M + $5.4M + $5.4M = $17.6M
Average Annual Cost: $5.9M/year
```

**AWS (3-year TCO):**
```
Compute (1000 instances × 24/7):
├─ On-Demand rate: 1000 × $0.10 × 730 hours × 36 months = $26.3M
├─ With Reserved Instances (60%): 60% × 26.3M × 0.70 = $11.0M
└─ Blended cost: $15.3M

Storage (10 PB):
├─ S3 Standard: 10,000 TB × $0.023/GB × 36 months = $8.3M
├─ S3 Standard-IA (infrequent): 50% at cheaper rate = $6.2M
└─ Cost: $6.2M

Data Transfer (assume 100 TB/month):
├─ 100 TB × $0.09/GB × 36 months = $324,000
└─ Cost: $324K

Network (ALB, VPN):
└─ Cost: $200K

Migration Services:
├─ AWS DMS, DataSync: $500K
└─ Cost: $500K

Total 3-Year Cost: $15.3M + $6.2M + $324K + $200K + $500K = $22.5M
Average Annual Cost: $7.5M/year

Surprising Result: AWS MORE expensive!
```

**Wait, Why Consider Cloud?**

```
On-premise ($5.9M/year):
├─ Fixed cost (must pay regardless)
├─ Scaling: Buy new hardware (CapEx hit)
└─ Ops staff: Critical bottleneck

AWS ($7.5M/year):
├─ Variable cost (adjust to demand)
├─ Scaling: Automatic, pay-as-you-go
├─ Ops staff: Minimal (AWS manages)
└─ Additional benefits: Disaster recovery, global reach, agility

Decision: Depends on:
- Growth trajectory
- Variability in demand
- Disaster recovery needs
- Time-to-market importance
```

### Cost Analysis Tools

**AWS Cost Explorer:**
```
Analyze:
├─ Cost by service (top services by bill)
├─ Cost by account (which team overspending)
├─ Cost by tag (cost per project)
└─ Cost trends (is it increasing/decreasing)

Example Report:
  Service        Cost      % of Total
  EC2           $4,500    35%
  RDS           $3,000    23%
  S3            $2,000    15%
  Lambda        $1,500    12%
  Other         $1,500    12%
  Total         $12,500
```

**AWS Budgets:**
```
Set budget:
└─ Monthly EC2 budget: $5,000

Monitor:
├─ Current spend: $3,200 (64% of budget)
├─ Forecast: $4,800 (96% of budget)
└─ Alert: Notify when exceeding threshold

Action:
└─ If forecasted to exceed → Increase Reserved Instances
```

---

## Cost Optimization Strategies

### Right-Sizing

**Problem: Overprovisioned Instances**

```
Current Setup:
├─ Instance: m5.2xlarge (8 vCPU, 32 GB RAM)
├─ Monthly cost: $300
├─ Actual utilization:
│  ├─ CPU: 10% average
│  ├─ Memory: 15% average
│  └─ Network: 2% average

Recommendation:
├─ Instance: m5.large (2 vCPU, 8 GB RAM)
├─ Monthly cost: $75
├─ Savings: $225/month ($2,700/year)
└─ Performance: Unchanged (spare capacity)
```

**Analysis:**
```
CloudWatch metrics (last 7 days):
├─ CPU: Max 15%, Average 8%
├─ Memory: Max 20%, Average 12%
└─ Network: Max 5%, Average 1%

Action:
├─ Downsize to m5.large
├─ Monitor for 2 weeks
├─ If performance good → Keep
├─ Reverse if needed (easy in cloud)
```

### Reserved Instances

**Strategy:**
```
1. Analyze historical usage
   └─ 60% of workload is baseline (predictable)
   
2. Purchase Reserved Instances for 60%
   └─ 1-year term (40% discount)
   └─ Save $X per month
   
3. Use On-Demand for other 40%
   └─ Flexible, scales up/down
   
4. During peaks:
   ├─ 60% from RI (guaranteed, cheap)
   └─ 40% from On-Demand (more expensive but flexible)
   
5. Result:
   ├─ Blended rate: Somewhere between RI and On-Demand
   └─ Optimization: Balance cost and flexibility
```

**Blended Rate Example:**
```
Baseline (60%): 100 instances
├─ All 100 on Reserved: $0.0334/hr × 100 × 730 hrs = $2,438/month

Peak (40%): Additional 67 instances
├─ All 67 on On-Demand: $0.0464/hr × 67 × 730 hrs = $2,274/month

Total: $2,438 + $2,274 = $4,712/month

Blended Rate:
└─ $4,712 / (167 instances × 730 hrs) = $0.0387/hr
└─ Discount: (0.0387 / 0.0464) = 83% of on-demand rate
```

### Auto-Scaling and Resource Management

**Without Auto-Scaling:**
```
Baseline: 10 servers (always on)
├─ Cost: 10 × $0.10 × 730 = $730/month

Peak hours (5 PM - 11 PM):
├─ Need: 50 servers
├─ To ensure: Provision 50 always
├─ Wasted: 40 servers during off-peak
├─ Cost: 50 × $0.10 × 730 = $3,650/month
```

**With Auto-Scaling:**
```
Baseline (off-peak): 10 servers
├─ Cost: 10 × $0.10 × 730 = $730/month

Peak hours: Auto-scale to 50 servers
├─ Cost: 40 × $0.10 × (5 hrs × 20 days) = $400/month

Monthly cost: $730 + $400 = $1,130
Savings: $3,650 - $1,130 = $2,520/month (69% reduction)
```

### Data Transfer Costs

**Often Overlooked Expense:**

```
Daily operations:
└─ 1 TB/day data transfer out = $90/day × 30 = $2,700/month

Optimization:
├─ CloudFront (edge caching): 70% cache hit rate
│  └─ Reduces egress 70% → $810/month
├─ S3 Transfer Acceleration: Faster, cheaper paths
├─ VPC endpoints: Access AWS services without egress charges
└─ Result: Save 60% on data transfer
```

---

## FinOps Framework

### FinOps Pillars

**Inform (Measure):**
```
Establish baseline:
├─ Current monthly spend: $50,000
├─ Cost per customer: $5
├─ Cost per transaction: $0.001
└─ Cost per GB processed: $0.05

Track trends:
├─ Month 1: $50,000
├─ Month 2: $52,000 (+4%)
├─ Month 3: $48,000 (-8%)
└─ Investigate why
```

**Optimize (Action):**
```
Based on findings:
├─ Instance oversized: Right-size (priority)
├─ Unused storage: Delete (quick win)
├─ No Reserved Instances: Purchase (savings)
├─ Data transfer excessive: Restructure (medium effort)
└─ Expected savings: 20-30%
```

**Operate (Sustain):**
```
Continuous improvement:
├─ Monthly cost reviews
├─ Quarterly optimization sweep
├─ FinOps team meeting
├─ Set cost goals ($45K target)
└─ Track against goals
```

### Cost Allocation and Chargeback

**Problem: Multiple teams, unclear cost responsibility**

```
Shared account: $50,000/month
├─ Team A (web): ?
├─ Team B (data): ?
├─ Team C (ops): ?
└─ Who's responsible for overspend?
```

**Solution: Resource Tags and Cost Allocation**

```
Tag all resources:
├─ Environment: production / staging / dev
├─ Team: team-a / team-b / team-c
├─ Project: website / analytics / bigdata
└─ CostCenter: engineering / ops / sales

Grouped cost report:
  Team A    Team B    Team C
  $20,000   $22,000   $8,000

Chargeback to teams:
└─ Each team pays their share
```

---

## Cloud Operations

### Infrastructure as Code (IaC)

**Manual Deployment (Error-Prone):**
```
1. SSH to server
2. Install dependencies
3. Copy application
4. Configure settings
5. Start service
6. Document changes (maybe)

Issues:
├─ Manual errors
├─ Configuration drift
├─ No version history
└─ Disaster recovery nightmare
```

**IaC Deployment (Repeatable):**
```
Terraform file:
resource "aws_instance" "web" {
  ami           = "ami-0c94855ba95c574c8"
  instance_type = "t2.micro"
  tags = {
    Name = "web-server"
  }
}

Deploy:
├─ terraform apply
└─ Identical infrastructure every time

Benefits:
├─ Version controlled (git)
├─ Repeatable (same every deploy)
├─ Disaster recovery (re-create infrastructure)
└─ Testing (spin up, test, destroy)
```

**IaC Tools:**
- Terraform: Multi-cloud, most popular
- CloudFormation: AWS-specific
- Ansible: Configuration management
- Pulumi: Code-based (Python, Go, etc.)

### Disaster Recovery and Business Continuity

**RPO (Recovery Point Objective):** How much data loss is acceptable?

```
Daily backups:
├─ Database backed up at midnight
├─ Disaster at 2 PM → Lost 14 hours of data
└─ RPO: 24 hours
```

**RTO (Recovery Time Objective):** How long until back online?

```
Restore from backup:
├─ Restore time: 4 hours
├─ Application startup: 30 minutes
├─ Data validation: 30 minutes
└─ RTO: 5 hours
```

**High Availability vs Disaster Recovery:**

| Aspect | HA | DR |
|--------|----|----|
| Goal | 99.99% uptime | Recover from disaster |
| Scope | Same region | Different region |
| RTO | Seconds | Hours |
| RPO | Seconds | Minutes-Hours |
| Cost | Higher | Lower |
| Complexity | High | Very High |

---

## Monitoring and Governance

### CloudWatch Monitoring

**Key Metrics:**
```
Application:
├─ Request latency
├─ Error rate
├─ Throughput (requests/sec)
└─ System health

Infrastructure:
├─ CPU utilization
├─ Memory usage
├─ Disk I/O
└─ Network throughput

Business:
├─ Revenue per request
├─ Customer count
├─ Feature usage
└─ Cost per customer
```

### Governance and Compliance

**Access Control:**
```
IAM policies limit who can:
├─ Create resources (no production modifications without approval)
├─ Delete resources (require MFA)
├─ Access data (encryption key access logged)
└─ Modify billing (CFO only)
```

**Cost Controls:**
```
Budget alerts:
├─ Warning: Notify when at 80% of budget
├─ Action: Reduce resources when at 100%
└─ Automation: Auto-stop non-critical resources if exceeded
```

**Compliance Auditing:**
```
CloudTrail logs all API calls:
├─ Who: Which user/role
├─ What: Which action (CreateInstance, DeleteBucket)
├─ When: Timestamp
├─ Where: Resource affected
└─ Used for: Audit trail, compliance verification
```

---

## Summary

**Cloud Cost Management:**
1. **Measure:** Use cost explorer, understand spending patterns
2. **Optimize:** Right-size instances, purchase RIs, use spot
3. **Automate:** IaC, auto-scaling, cost monitoring
4. **Govern:** Set budgets, enforce policies, audit spending

**Key Principles:**
- Right-sizing (most impact, quick wins)
- Reserved Instances (predictable workloads, 30-50% savings)
- Automation (reduces manual overhead)
- Monitoring (detect waste early)

**Cost Optimization Potential:**
- Typical savings: 20-30% with basic optimization
- Advanced optimization: 40-50% possible
- Requires: Discipline, monitoring, continuous improvement

