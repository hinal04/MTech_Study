# CS8: Platform-as-a-Service (PaaS) and Software-as-a-Service (SaaS)

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**
>
> **References:**
> - T1: Buyya, Vecchiola & Selvi, *Mastering Cloud Computing*, Ch.11-12
> - T2: Erl, Puttini & Mahmood, *Cloud Computing: Concepts, Technology & Architecture*, Ch.8-9

---

## Table of Contents

- [8.1 Platform-as-a-Service (PaaS)](#81-platform-as-a-service-paas)
- [8.2 PaaS Architecture](#82-paas-architecture)
- [8.3 PaaS Providers and Services](#83-paas-providers-and-services)
- [8.4 Software-as-a-Service (SaaS)](#84-software-as-a-service-saas)
- [8.5 SaaS Architecture](#85-saas-architecture)
- [8.6 SaaS Providers and Examples](#86-saas-providers-and-examples)
- [8.7 PaaS vs. SaaS vs. IaaS](#87-paas-vs-saas-vs-iaas)
- [8.8 Advantages and Limitations](#88-advantages-and-limitations)

---

## 8.1 Platform-as-a-Service (PaaS)

### What is PaaS?

Platform-as-a-Service provides a complete development and deployment environment in the cloud. Developers focus on application logic; platform manages infrastructure.

**Provider Manages:**
- Infrastructure (servers, storage, networking)
- Operating system
- Middleware (app server, database, message queue)
- Development tools and libraries

**Developer Manages:**
- Application code
- Business logic
- User experience
- Data

### PaaS Value Proposition

**For Developers:**
- Rapid development: Pre-built components accelerate development
- No infrastructure management: Focus on code
- Integrated development tools: IDE, version control, testing
- Easy deployment: Push code; platform handles rest
- Collaboration: Built-in team features

**For Organizations:**
- Faster time-to-market
- Reduced development costs
- Consistent environments (dev, test, production)
- Integrated security and compliance

---

## 8.2 PaaS Architecture

### Typical PaaS Stack

```
┌─────────────────────────────────────┐
│   Developer Applications            │
│   (Custom business logic)           │
├─────────────────────────────────────┤
│   Development Tools                 │
│   (IDE, APIs, libraries, templates) │
├─────────────────────────────────────┤
│   Platform Services                 │
│   (Database, Caching, Queue, etc.)  │
├─────────────────────────────────────┤
│   Middleware                        │
│   (App servers, runtime)            │
├─────────────────────────────────────┤
│   Operating System                  │
├─────────────────────────────────────┤
│   Infrastructure (IaaS Layer)       │
│   (Servers, Storage, Networking)    │
└─────────────────────────────────────┘
```

### PaaS Execution Model

1. **Write Code:** Develop application locally or in cloud IDE
2. **Version Control:** Commit to cloud repository
3. **Deploy:** Push to platform (git push, upload, or console)
4. **Build:** Platform compiles/packages code
5. **Run:** Application starts on platform
6. **Scale:** Platform automatically scales based on demand
7. **Monitor:** Built-in monitoring and logging

### Development Workflow

```
Local Development → Commit → Cloud Repository → Trigger Build
         ↓
Test Locally → Push Changes → Automated Testing → Deploy to Staging
         ↓
Manual QA → Promote to Production → Monitoring/Alerting
```

---

## 8.3 PaaS Providers and Services

### Heroku

**Simplicity-focused PaaS**

```bash
# Create app
heroku create myapp

# Deploy (git push)
git push heroku main

# Scale to 3 dynos (processes)
heroku ps:scale web=3

# View logs
heroku logs --tail

# Add PostgreSQL database
heroku addons:create heroku-postgresql:standard
```

**Features:**
- One-click provisioning
- Built-in databases (PostgreSQL, MySQL)
- Add-ons: Redis, Elasticsearch, etc.
- Simple scaling
- Automatic SSL certificates

**Limitations:**
- Expensive per dyno-hour
- Limited customization
- Vendor lock-in

### AWS Elastic Beanstalk

**AWS's PaaS offering**

- Upload code (WAR, JAR, ZIP)
- Platform automatically provisions: EC2, RDS, Load Balancer, Auto Scaling
- Supports: Java, Python, Node.js, PHP, Ruby, Go, .NET
- Deep integration with AWS services

```bash
# Deploy
eb init
eb create production
eb deploy

# Scale
eb scale 3

# Monitor
eb status
```

### Google App Engine

**Google's serverless PaaS**

- Write application in Python, Java, Node.js, Go, Ruby, PHP, C#, PowerShell
- Upload code
- Google handles: scaling, load balancing, monitoring
- Pay per request

```python
# Simple Flask app
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run()
```

Deploy: `gcloud app deploy`

### Azure App Service

**Microsoft's PaaS**

- Web apps, API apps, mobile apps, logic apps
- Integrated with Office 365, Dynamics 365
- Supports: ASP.NET, PHP, Node.js, Python, Java, Ruby

---

## 8.4 Software-as-a-Service (SaaS)

### What is SaaS?

Software delivered over internet via web browser. Users subscribe; don't install locally.

**Provider Manages:**
- Everything: Infrastructure, platform, application, security, data backup
- Updates and new features (automatic)
- Compliance and certifications

**User Manages:**
- User accounts and permissions
- Data configuration and content
- Business processes

### SaaS vs. Traditional Software

| Aspect | Traditional Software | SaaS |
|--------|----------------------|------|
| **Installation** | Download; install locally | Access via browser |
| **Maintenance** | User responsible; patches, updates | Provider handles |
| **Customization** | Often possible through scripting/plugins | Limited; configuration-based |
| **Data Location** | Local computer or local server | Provider's servers |
| **Collaboration** | Shared drives, email | Built-in collaboration features |
| **Availability** | Local; limited to device | 24/7; any device, any location |
| **Licensing** | Per-user license; perpetual | Subscription-based |
| **Cost Structure** | High upfront; lower ongoing | Low upfront; predictable monthly |
| **Update Cadence** | Quarterly or annual releases | Continuous updates |

---

## 8.5 SaaS Architecture

### Multi-Tenant Architecture

Single application instance serves multiple customers. Data logically separated.

```
┌─────────────────────────────────────┐
│   Load Balancer                     │
└─────────┬───────────────────────────┘
          │
     ┌────┴────┬────────┬────────┐
     ▼         ▼        ▼        ▼
┌────────┐ ┌────────┐ ┌──────┐ ┌──────┐
│App     │ │App     │ │App   │ │App   │
│Inst. 1 │ │Inst. 2 │ │Inst.3│ │Inst.4│
└────┬───┘ └───┬────┘ └──┬───┘ └──┬───┘
     │         │         │        │
     └─────────┴─────────┴────────┘
             │
      ┌──────▼─────────┐
      │  Shared Database│
      │ (Tenant A Data) │
      │ (Tenant B Data) │
      │ (Tenant C Data) │
      └────────────────┘
```

**Benefits:**
- **Efficiency:** Share infrastructure across customers
- **Cost:** Lower per-customer costs
- **Updates:** Single update applies to all customers
- **Scalability:** Add customers without adding servers

**Security Consideration:**
- Strict data isolation required
- Row-level security (tenants see only their data)
- Encryption with tenant-specific keys recommended

### SaaS Lifecycle

```
1. Signup → 2. Provision → 3. Use → 4. Collaborate → 5. Analyze → 6. Upgrade
```

---

## 8.6 SaaS Providers and Examples

### Productivity & Collaboration

**Microsoft Office 365 / Microsoft 365:**
- Word, Excel, PowerPoint, OneNote (web and desktop)
- Outlook (email, calendar)
- Teams (chat, meetings, collaboration)
- OneDrive (file storage)
- Shared workspaces

**Google Workspace:**
- Docs, Sheets, Slides (web-based)
- Gmail
- Meet (video conferencing)
- Drive (file storage)
- Calendar

### Customer Relationship Management (CRM)

**Salesforce:**
- Sales pipeline management
- Customer data platform
- Service cloud (support ticketing)
- Marketing cloud
- Analytics

### Human Resources

**Workday:**
- Employee data management
- Payroll and compensation
- Recruitment
- Learning management
- Analytics and reporting

### Project Management

**Jira (Atlassian):**
- Issue tracking
- Project planning
- Agile boards
- Time tracking
- Reporting

**Asana / Monday.com:**
- Task management
- Team collaboration
- Timeline and Gantt charts
- Workflow automation

### Communication

**Slack:**
- Team messaging
- Channels (organized conversations)
- Integrations (GitHub, Jira, etc.)
- Search and history

**Zoom:**
- Video conferencing
- Screen sharing
- Recording
- Breakout rooms

### Finance

**QuickBooks Online:**
- Accounting
- Invoicing
- Expense tracking
- Tax preparation

### E-commerce

**Shopify:**
- Store setup and hosting
- Payment processing
- Inventory management
- Shipping integration

---

## 8.7 PaaS vs. SaaS vs. IaaS

### Responsibility Matrix

```
┌────────────────┬──────────┬──────────┬──────────┐
│ Layer          │ IaaS     │ PaaS     │ SaaS     │
├────────────────┼──────────┼──────────┼──────────┤
│ Application    │ Customer │ Customer │ Provider │
│ Data           │ Customer │ Customer │ Varies   │
│ Runtime        │ Customer │ Provider │ Provider │
│ Middleware     │ Customer │ Provider │ Provider │
│ OS             │ Customer │ Provider │ Provider │
│ Virtualization │ Provider │ Provider │ Provider │
│ Servers        │ Provider │ Provider │ Provider │
│ Storage        │ Provider │ Provider │ Provider │
│ Networking     │ Provider │ Provider │ Provider │
└────────────────┴──────────┴──────────┴──────────┘
```

### Comparison

| Aspect | IaaS | PaaS | SaaS |
|--------|------|------|------|
| **Control** | Maximum | Medium | Minimum |
| **Flexibility** | High | Medium | Low |
| **Ease of Use** | Low | Medium | High |
| **Setup Time** | Hours | Minutes | Instant |
| **Learning Curve** | Steep | Medium | Low |
| **Customization** | Unlimited | Limited | Very limited |
| **Cost** | Pay for resources | Pay for deployment | Pay per user |
| **Scalability** | Manual/Auto | Automatic | Automatic |
| **Example** | AWS EC2, Azure VMs | Heroku, App Engine | Salesforce, Office 365 |

---

## 8.8 Advantages and Limitations

### PaaS Advantages

✅ **Rapid Development:** Pre-built components accelerate development
✅ **No Infrastructure Management:** Focus on code, not servers
✅ **Built-in Tools:** IDE, testing, deployment integrated
✅ **Automatic Scaling:** Handle traffic spikes automatically
✅ **Consistent Environment:** Dev/test/prod consistency
✅ **Team Collaboration:** Built-in team features

### PaaS Limitations

❌ **Vendor Lock-in:** Difficult to migrate to another provider
❌ **Limited Customization:** Can't modify underlying platform
❌ **Language/Framework Constraints:** May not support all technologies
❌ **Performance:** Less control over optimization
❌ **Compliance:** May not meet specific requirements
❌ **Cost:** Can be expensive at scale

### SaaS Advantages

✅ **Simplicity:** No installation; access from browser
✅ **Accessibility:** Any device, any location
✅ **Automatic Updates:** Always latest features/security
✅ **Collaboration:** Built-in team features
✅ **Reduced IT Burden:** Provider handles everything
✅ **Low Upfront Cost:** No licenses to purchase
✅ **Scalability:** Provider scales automatically

### SaaS Limitations

❌ **Limited Customization:** Configure but can't modify
❌ **Data Privacy:** Data on provider's servers
❌ **Vendor Lock-in:** Switching providers difficult (data export)
❌ **Internet Dependency:** Requires reliable internet
❌ **Limited Offline:** Often requires online access
❌ **Recurring Costs:** Monthly subscriptions accumulate
❌ **Performance Dependency:** Your experience depends on provider's infrastructure

### Decision Framework

**Choose PaaS if:**
- Building custom applications
- Need faster development
- Want to reduce infrastructure management
- Developing web/mobile applications
- Team experienced with specific platform

**Choose SaaS if:**
- Need off-the-shelf solution
- Don't need heavy customization
- Want to minimize IT overhead
- Solutions exist for your use case
- Budget predictability important

---

## Summary

**PaaS** abstracts infrastructure and platform, allowing developers to focus on application code. **SaaS** abstracts everything, providing ready-to-use applications. Both reduce operational overhead but involve trade-offs in customization and control. The choice depends on organizational needs: PaaS for custom development at scale, SaaS for standardized business applications.

