# Cloud Computing - Post-Semester Questions & Answers
## CSI ZG527 - Chapters 7-9 (PaaS, SaaS, VM Provisioning & Migration)

---

## Q1. PaaS - Platform as a Service [3 Marks]

### Question:
Define PaaS and explain how it differs from IaaS and SaaS. What are the key characteristics of PaaS? Provide 3 practical examples and discuss: (i) when to use PaaS, (ii) advantages, (iii) limitations.

### Answer:

**PaaS Definition and Positioning [1 mark]:**

PaaS (Platform as a Service) provides a platform (middleware, runtime, databases) where developers can build, deploy, and run applications without managing underlying infrastructure. Provider manages: infrastructure, OS, databases, middleware. Customer manages: application code, data, users.

**How PaaS differs from IaaS and SaaS:**
- **vs. IaaS:** PaaS abstracts infrastructure; developers focus on code, not servers/networking. IaaS requires customer to manage OS and middleware.
- **vs. SaaS:** PaaS provides development platform; customer writes custom applications. SaaS provides ready-to-use applications.

**Key Characteristics [1 mark]:**

1. **Rapid Development:** Pre-built components, frameworks, libraries accelerate development
2. **Built-in Services:** Databases, message queues, caching, analytics provided
3. **Scalability:** Automatic scaling of applications based on demand
4. **Multi-tenancy:** Typically multi-tenant architecture reduces costs
5. **Development Workflow:** Integrated version control, testing, deployment pipelines
6. **Pay-as-you-go:** Pricing based on resource usage, deployment time

**Practical Examples [0.5 marks]:**
1. **Heroku:** Deploy web applications by pushing code to git repository; automatic scaling; integrated databases
2. **AWS Elastic Beanstalk:** Upload code; platform automatically handles infrastructure, load balancing, auto-scaling
3. **Google App Engine:** Write Python/Java/Node.js applications; platform runs and scales them automatically

**When to Use PaaS [0.25 marks]:**
- Rapid application development needed
- Don't need fine-grained infrastructure control
- Want to leverage built-in services (databases, caching, APIs)
- Team wants to focus on application code, not infrastructure

**Advantages [0.25 marks]:**
- Faster development and deployment
- Integrated development tools and services
- Automatic scaling and management
- Lower operational overhead

---

## Q2. SaaS - Software as a Service [3 Marks]

### Question:
Define SaaS and explain the SaaS business model. Discuss: (i) provider's responsibilities, (ii) customer's responsibilities, (iii) 5 characteristics of SaaS, (iv) advantages and disadvantages, (v) provide 4 practical examples.

### Answer:

**SaaS Definition [0.75 marks]:**

SaaS (Software as a Service) delivers fully functional applications over the internet via web browser or APIs. Users subscribe to applications without installing or maintaining software locally. Provider manages everything: infrastructure, platform, application, security, availability.

**SaaS Business Model [0.5 marks]:**
- **Subscription-based pricing:** Monthly or annual subscriptions (sometimes freemium with paid tiers)
- **Multi-tenant architecture:** Single application instance serves multiple customers
- **Continuous updates:** Provider updates application automatically; all users get latest features
- **No installation required:** Access via web browser; no client-side installation

**Provider's Responsibilities [0.5 marks]:**
- All infrastructure (servers, storage, networking, security, backup, disaster recovery)
- Application development, maintenance, new features
- Security patches, compliance certifications
- Service availability, uptime SLA
- Data backup, encryption

**Customer's Responsibilities [0.5 marks]:**
- User account management, access control
- Configuring application for their business needs
- Ensuring data accuracy, compliance of data use
- Compliance with SaaS acceptable use policy

**Five Key Characteristics of SaaS [0.75 marks]:**

1. **Accessibility:** Web-based access from any device with internet connection
2. **Multi-tenancy:** Single application serves multiple customers; data logically separated
3. **Continuous Updates:** Features, security patches deployed automatically; no version management
4. **Scalability:** Application automatically scales for all users
5. **Integration:** APIs enable integration with other applications and systems

**Advantages [0.5 marks]:**
- **Simplicity:** No installation, minimal training
- **Accessibility:** Available anywhere on any device
- **Cost-effective:** No upfront hardware investment; predictable monthly costs
- **Automatic updates:** Always have latest features and security patches
- **Reliability:** Provider ensures uptime and backup

**Disadvantages [0.5 marks]:**
- **Less control:** Cannot customize heavily; limited flexibility
- **Data privacy concerns:** Data on third-party servers; vendor lock-in
- **Internet dependency:** Requires reliable internet connection
- **Limited offline capabilities:** Cannot use without internet
- **Recurring costs:** Continuous subscription costs

**Practical Examples:**
1. **Salesforce:** CRM platform used globally for customer relationship management
2. **Microsoft Office 365:** Cloud-based productivity suite (Word, Excel, Teams, etc.)
3. **Slack:** Communication and collaboration platform
4. **Dropbox:** File storage and synchronization service

---

## Q3. Cloud Deployment Patterns and Architectures [3.5 Marks]

### Question:
Explain 4 common deployment patterns in cloud: (i) lift-and-shift, (ii) refactor/re-architect, (iii) repurchase, (iv) retire. For each: describe the approach, discuss use cases, advantages, disadvantages.

### Answer:

**Lift-and-Shift (Rehost) [0.9 marks]:**
- **Approach:** Move on-premises applications directly to cloud without modifications. Application runs in VMs on cloud similar to on-premises.
- **Use Cases:** Legacy applications where re-architecting is too costly/complex; applications with tight dependencies on existing infrastructure
- **Advantages:** Minimal changes; fast migration; quick cost reduction; no re-development needed
- **Disadvantages:** Doesn't leverage cloud-native features; may not be cost-optimal; performance may not be ideal; scalability still limited by architecture

**Refactor/Re-architect [0.9 marks]:**
- **Approach:** Modify application to leverage cloud-native capabilities (microservices, containers, serverless, auto-scaling, managed services).
- **Use Cases:** Performance-critical applications; applications that benefit from auto-scaling; applications where cloud investment is strategic
- **Advantages:** Optimized for cloud; leverages elasticity; better performance; cost optimization; modern architecture
- **Disadvantages:** High cost/effort; requires re-development; time-consuming; risk of introducing bugs during refactoring

**Repurchase (Re-platform) [0.85 marks]:**
- **Approach:** Replace on-premises application with SaaS equivalent. Example: Replace on-premises CRM with Salesforce; replace email server with Office 365.
- **Use Cases:** Standard business applications (CRM, ERP, HR, Finance); where vendor solution meets requirements
- **Advantages:** Modern application; reduced maintenance burden; immediate benefit of continuous updates and features; proven solution
- **Disadvantages:** Vendor lock-in; customization limited; data migration complexity; change management with users; switching costs

**Retire [0.75 marks]:**
- **Approach:** Decommission on-premises applications that are no longer needed or are replaced by cloud alternatives.
- **Use Cases:** Legacy applications with minimal business value; applications with declining user base; redundant systems
- **Advantages:** Reduce complexity; eliminate maintenance costs; free up staff; clear IT landscape; reduce technical debt
- **Disadvantages:** Loss of functionality if not properly replaced; need to migrate data/workflows to replacement systems; organizational change resistance

---

## Q4. Virtual Machine Provisioning [3 Marks]

### Question:
Explain the VM provisioning process in cloud. Discuss: (i) steps involved, (ii) provisioning templates/images, (iii) automated vs. manual provisioning, (iv) infrastructure-as-code role in provisioning, (v) challenges in provisioning.

### Answer:

**VM Provisioning Process [0.75 marks]:**

1. **Request:** User submits VM request (compute size, OS, storage, networking)
2. **Validation:** Cloud provider validates request against quotas, permissions
3. **Resource Allocation:** Scheduler selects physical host with available resources
4. **VM Creation:** Hypervisor creates VM with requested specifications
5. **Image Deployment:** OS and software image deployed to VM
6. **Configuration:** Network configuration, security groups, storage attachment
7. **Initialization:** VM boot; applications configured and started
8. **Delivery:** VM ready for use; credentials provided to user

**Provisioning Templates/Images [0.5 marks]:**
- **Golden Images:** Pre-configured VM images with OS, patches, standard software
- **Cloud-Init:** Standard mechanism for customizing VMs at boot time (user data scripts)
- **VM Templates:** Reusable templates specifying VM configuration, resources, software
- **Base Images:** Minimal OS images (Ubuntu 20.04, CentOS 8) customized by user

**Automated vs. Manual Provisioning [0.5 marks]:**
- **Manual:** Admin manually creates VMs via console; slow; error-prone; not scalable
- **Automated:** APIs, CLI, or scripts automatically provision VMs; fast; consistent; scalable; enables self-service

**Infrastructure-as-Code Role [0.5 marks]:**
IaC enables codified provisioning: define infrastructure in files (Terraform, CloudFormation); version-controlled; repeatable; enables automated CI/CD pipelines for infrastructure; disaster recovery becomes straightforward (reproduce infrastructure from code).

**Challenges in Provisioning [0.25 marks]:**
- **Time:** Provisioning can take minutes; slow feedback loop for testing
- **Complexity:** Managing many VMs, dependencies, configurations
- **Consistency:** Manual provisioning leads to configuration drift
- **Cost:** Tracking, billing, preventing over-provisioning
- **Security:** Secure initial configuration, credential management

---

## Q5. VM Migration Strategies [3.5 Marks]

### Question:
Explain VM migration from on-premises to cloud. Discuss: (i) what is VM migration, (ii) migration strategies (cold migration, warm migration, hot migration), (iii) challenges, (iv) best practices, (v) post-migration optimization.

### Answer:

**What is VM Migration? [0.5 marks]:**
VM migration is moving running virtual machine from one location to another (on-premises to cloud, cloud provider to another, physical to virtual). Includes transferring OS, application, data, and state.

**Migration Strategies [1.5 marks]:**

1. **Cold Migration (Offline Migration):**
   - Shutdown VM completely; copy disk contents to destination; restart on destination
   - *Advantage:* Simplest; no active memory to copy; stable source
   - *Disadvantage:* Downtime; service unavailable during migration; users affected
   - *When to use:* Non-critical applications; maintenance windows available

2. **Warm Migration (Hybrid):**
   - Pause VM; copy memory state and disk to destination; resume on destination
   - *Advantage:* Minimal downtime (seconds to minutes)
   - *Disadvantage:* Some downtime; state must be consistent
   - *When to use:* Acceptable brief downtime; stateful applications

3. **Hot Migration (Live Migration):**
   - VM continues running; memory and state transferred while running; pause only at final moment
   - *Advantage:* Minimal or zero downtime; continuous availability
   - *Disadvantage:* Complex; requires compatible source/destination; potential performance impact
   - *When to use:* Critical applications; SLA requires high availability

**Challenges [0.75 marks]:**
- **Downtime:** Users cannot access applications during migration
- **Data transfer:** Large VMs (100+ GB) take time to transfer
- **Compatibility:** Source and destination architectures may differ
- **Network connectivity:** Requires reliable connectivity during transfer
- **Complexity:** Multiple dependencies, databases, configurations to migrate
- **Cost:** Migration tools, downtime, validation costs

**Best Practices [0.5 marks]:**
- Plan thoroughly; understand dependencies, data requirements
- Backup before migration; ensure rollback capability
- Test migration in non-production first
- Use migration tools (AWS DMS, Azure Migrate, CloudEndure)
- Validate post-migration; test applications thoroughly
- Optimize after migration; cloud-specific tuning, cost optimization

**Post-Migration Optimization [0.25 marks]:**
- **Right-sizing:** Adjust VM sizes; initial estimates often conservative
- **Auto-scaling:** Implement auto-scaling for variable workloads
- **Cloud-native services:** Replace self-managed services with managed services (RDS instead of self-managed DB)
- **Cost optimization:** Remove unused resources; leverage reserved instances, spot instances
- **Monitoring:** Implement cloud-native monitoring and logging

---

## Q6. Docker and Kubernetes in Cloud [3 Marks]

### Question:
Explain Docker and Kubernetes in context of cloud computing. Discuss: (i) Docker containerization, (ii) Kubernetes orchestration, (iii) how they work together, (iv) when to use each, (v) advantages in cloud deployment.

### Answer:

**Docker Containerization [1 mark]:**
- **Definition:** Docker packages applications and dependencies into lightweight containers
- **Workflow:** Write Dockerfile → build image → run containers
- **Advantages in cloud:** Portability (runs same on laptop, test, production); resource efficiency; rapid deployment
- **Cloud usage:** Base layer for modern cloud applications; foundation for microservices

**Kubernetes Orchestration [1 mark]:**
- **Definition:** Kubernetes automates deployment, scaling, networking of containers across clusters
- **Key features:** Auto-scaling; self-healing; rolling updates; service discovery; persistent storage
- **Orchestration:** Manages 100s/1000s of containers; handles placement, networking, storage
- **Cloud usage:** Industry standard for container orchestration (AWS EKS, Azure AKS, GCP GKE)

**How They Work Together [0.5 marks]:**
- Developers containerize applications using Docker
- Kubernetes orchestrates Docker containers across cluster
- Developer pushes Docker image to registry
- Kubernetes deployment pulls image, schedules containers, manages lifecycle

**When to Use [0.25 marks]:**
- **Docker alone:** Simple applications; single or few containers; limited scaling needs
- **Docker + Kubernetes:** Microservices architecture; need auto-scaling; production deployments; complex deployments

**Advantages in Cloud [0.25 marks]:**
- Portability across clouds (Docker image runs on AWS, Azure, GCP)
- Cost optimization (high density; efficient resource utilization)
- Rapid scaling (containers start in milliseconds)
- Developer productivity (dev environment mirrors production)

---

## Q7. Serverless Computing (FaaS) [3.5 Marks]

### Question:
Define serverless computing and Function-as-a-Service (FaaS). Explain: (i) how serverless works, (ii) difference from traditional server-based applications, (iii) advantages and disadvantages, (iv) use cases, (v) examples of serverless platforms, (vi) cost model.

### Answer:

**Serverless Computing / FaaS Definition [0.75 marks]:**

Serverless computing (Function-as-a-Service) is cloud execution model where applications run in stateless, short-lived functions triggered by events. Developers write functions; cloud platform handles provisioning, scaling, execution. No servers to manage (from developer perspective).

**How Serverless Works [0.75 marks]:**
1. Developer writes function (e.g., process image, send email)
2. Upload function to serverless platform (AWS Lambda, Azure Functions)
3. Configure trigger (HTTP request, S3 file upload, database change, schedule)
4. Function invoked when trigger occurs
5. Platform allocates resources, executes function, returns result
6. Resources released after execution
7. Developer charged only for execution time

**Difference from Traditional Server-based [0.5 marks]:**

| Aspect | Traditional | Serverless |
|--------|-----------|-----------|
| **Infrastructure** | Manage servers, OS, middleware | No infrastructure management |
| **Scaling** | Manual or auto-scaling VMs | Automatic per-function |
| **Billing** | Charged for running servers (even idle) | Charged for execution time |
| **Startup** | Servers always running | Instantaneous (cold start latency) |
| **Code** | Full applications | Stateless functions |
| **Complexity** | Infrastructure management overhead | Just function code |

**Advantages [0.75 marks]:**
- **Cost-efficient:** Pay only for actual execution; no idle server costs
- **Auto-scaling:** Automatic scale from zero to thousands of concurrent functions
- **Reduced complexity:** No infrastructure management; focus on business logic
- **Rapid development:** Write function, deploy in seconds
- **High availability:** Built-in redundancy, fault tolerance

**Disadvantages [0.75 marks]:**
- **Cold start latency:** First invocation slow (500ms-2s) as platform allocates resources
- **Limited execution time:** Functions typically limited to 5-15 minutes
- **Statelessness:** Cannot maintain state between invocations; use external storage
- **Monitoring complexity:** Distributed tracing, debugging multiple functions harder
- **Vendor lock-in:** Serverless platforms proprietary; difficult to migrate

**Use Cases [0.5 marks]:**
- **Event processing:** Process files uploaded to storage, database changes, IoT sensor data
- **Scheduled tasks:** Cron jobs, regular reports, cleanup tasks
- **APIs:** Lightweight HTTP APIs, webhooks
- **Data processing:** ETL pipelines, log processing, image resizing
- **Notifications:** Send emails, SMS, push notifications

**Serverless Platforms:**
- **AWS Lambda:** Industry standard; integrates with all AWS services
- **Azure Functions:** Microsoft's serverless; integrates with Office 365, Teams
- **Google Cloud Functions:** GCP's serverless platform
- **IBM Cloud Functions:** OpenWhisk-based serverless

**Cost Model [0.25 marks]:**
- Charged per invocation (typically $0.20 per million)
- Charged for execution time (e.g., $0.0000166667 per GB-second)
- Free tier includes 1 million invocations/month
- Example: 1 million function invocations, 1 second each = ~$1-2/month

---

## Q8. Managed Services in Cloud [2.5 Marks]

### Question:
What are managed services? Explain: (i) the concept, (ii) benefits vs. self-managed, (iii) examples of managed services, (iv) when to use managed services, (v) trade-offs.

### Answer:

**What are Managed Services? [0.75 marks]:**
Managed services are cloud services where the provider manages the complete stack: deployment, updates, patches, backups, monitoring, scaling, availability. Customer focuses only on business logic and data.

**Examples of Managed Services [1 mark]:**
- **Databases:** AWS RDS (managed SQL databases), DynamoDB (managed NoSQL), Elasticsearch (managed search)
- **Message Queues:** AWS SQS, RabbitMQ as service
- **Cache:** AWS ElastiCache (managed Redis/Memcached)
- **Analytics:** AWS Athena (SQL queries on S3), BigQuery (Google's data warehouse)
- **Machine Learning:** AWS SageMaker, Azure ML
- **Content Delivery:** AWS CloudFront (CDN)

**Benefits vs. Self-Managed [0.5 marks]:**

| Aspect | Self-Managed | Managed Service |
|--------|-------------|-----------------|
| **Management Overhead** | High (patches, updates, backups, scaling) | Minimal (provider handles) |
| **Availability** | Your responsibility | Provider guarantees SLA |
| **Scaling** | Manual or custom auto-scaling | Automatic; handled by provider |
| **Cost** | Lower direct; high operational | Higher direct; lower operational |
| **Control** | Full control; customization | Limited customization |

**Benefits [0.5 marks]:**
- Reduced operational overhead; focus on application not infrastructure
- Automatic scaling, backups, patches
- High availability guaranteed by SLA
- Professional management by provider experts
- Faster deployment; no setup time

**Trade-offs [0.25 marks]:**
- Higher cost than self-managed infrastructure
- Less control; less customization possible
- Potential vendor lock-in

---

## Q9. Cloud Cost Optimization [2.5 Marks]

### Question:
Discuss cloud cost optimization strategies. Explain: (i) common sources of cloud costs, (ii) strategies to reduce costs, (iii) cost monitoring and control, (iv) reserved vs. on-demand instances, (v) spot instances.

### Answer:

**Common Sources of Cloud Costs [0.5 marks]:**
- **Compute:** VMs, containers, functions; running time
- **Storage:** Object storage (S3), database storage, backup storage
- **Data transfer:** Egress from region to internet, between regions
- **Services:** Managed services (databases, APIs, monitoring)
- **Licenses:** Software licenses if running on cloud VMs

**Cost Reduction Strategies [1.25 marks]:**

1. **Right-sizing:** Adjust instance sizes; initial provisioning often over-sized; monitor utilization
   
2. **Reserved Instances:** Commit to 1 or 3-year usage; receive 30-60% discount vs. on-demand
   
3. **Spot Instances:** Purchase unused capacity at discounts (70% off); interruptible; suitable for batch jobs
   
4. **Shut down unused resources:** Stop/terminate non-production environments; clean up unused EBS volumes
   
5. **Use managed services:** Database-as-service cheaper than self-managed; no operational overhead
   
6. **Auto-scaling:** Scale down during off-peak hours; scale up only when needed
   
7. **Data transfer optimization:** Keep data in-region; compress data; use CDN for edge caching

**Cost Monitoring and Control [0.5 marks]:**
- Set budgets and alerts (AWS Budgets, Azure Cost Management)
- Tag resources for cost allocation; understand cost per application/team
- Use cost analysis tools to identify expensive resources
- Regular cost reviews; rightsizing based on usage patterns

**Reserved vs. On-Demand [0.25 marks]:**
- **On-demand:** Pay per hour; flexible; 100% cost if not used; suitable for variable workloads
- **Reserved instances:** Commit upfront; 30-60% discount; suitable for baseline/predictable workloads

**Spot Instances [0.25 marks]:**
- Purchase unused cloud capacity at discounts (often 70%)
- Can be interrupted with short notice
- Cost-effective for fault-tolerant, batch jobs (data processing, testing, CI/CD)
- Example: Batch image processing using 100 spot instances at $0.03/hour instead of $0.10/hour on-demand

---

## Q10. Cloud Security and Compliance [3 Marks]

### Question:
Discuss cloud security and compliance considerations. Explain: (i) cloud security challenges, (ii) provider's security responsibility, (iii) customer's security responsibility, (iv) common compliance requirements, (v) security best practices.

### Answer:

**Cloud Security Challenges [0.75 marks]:**
- **Multi-tenancy risks:** Data isolation between tenants; potential cross-tenant data breach
- **Shared responsibility confusion:** Unclear who is responsible for what
- **Network visibility:** Less visibility into cloud infrastructure; harder to troubleshoot
- **Data residency:** Data stored in cloud provider's data centers; regulatory requirements
- **Configuration mistakes:** Default open security groups; misconfigured access controls
- **Vendor lock-in:** Difficult to switch providers if security breach occurs

**Provider's Security Responsibility [0.5 marks]:**
- Physical infrastructure security; access control to data centers
- Network infrastructure; DDoS protection at network level
- Hypervisor security; VM isolation
- Encryption keys (if provider-managed); security patches to infrastructure
- Compliance certifications (ISO 27001, SOC 2, FedRAMP)

**Customer's Security Responsibility [0.5 marks]:**
- Encryption keys (if customer-managed); key rotation
- Access control; IAM policies; principle of least privilege
- Data classification; encryption of sensitive data
- Application security; secure code practices
- Monitoring and logging; incident response
- Compliance validation; audit readiness

**Common Compliance Requirements [0.75 marks]:**
- **GDPR (EU):** Personal data protection; data residency in EU
- **HIPAA (Healthcare):** Protected health information security; audit trails
- **PCI-DSS (Finance):** Credit card data security; network segmentation
- **SOC 2:** Service organization controls; availability, security, integrity
- **FedRAMP:** US government cloud security; compliance for government agencies

**Security Best Practices [0.5 marks]:**
- Enable MFA for all accounts
- Use principle of least privilege; minimize permissions
- Encrypt sensitive data at-rest and in-transit
- Enable audit logging; monitor access patterns
- Regular security assessments; penetration testing
- Keep systems patched; enable automatic patching where possible
- Separate production and non-production environments

---

## Marks Summary
- Q1: 3 marks
- Q2: 3 marks
- Q3: 3.5 marks
- Q4: 3 marks
- Q5: 3.5 marks
- Q6: 3 marks
- Q7: 3.5 marks
- Q8: 2.5 marks
- Q9: 2.5 marks
- Q10: 3 marks

**Total: 31.5 marks**

