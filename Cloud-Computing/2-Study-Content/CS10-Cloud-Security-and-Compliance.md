# Cloud Security and Compliance - Session 10

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**

---

## Table of Contents

1. [Cloud Security Fundamentals](#cloud-security-fundamentals)
2. [Identity and Access Management (IAM)](#identity-and-access-management-iam)
3. [Data Protection and Encryption](#data-protection-and-encryption)
4. [Network Security](#network-security)
5. [Cloud Compliance and Governance](#cloud-compliance-and-governance)
6. [Security Best Practices](#security-best-practices)
7. [Incident Response and Disaster Recovery](#incident-response-and-disaster-recovery)

---

## Cloud Security Fundamentals

### The Security Challenge in Cloud

Cloud computing introduces unique security challenges:

**Traditional Model:**
- Physical security: You control the data center
- Network security: Isolated internal networks
- Access control: Limited to authorized personnel
- Data ownership: Physical possession of servers

**Cloud Model:**
- Physical security: Shared with other tenants
- Network security: Internet-connected, multi-tenant
- Access control: Identity-based, granular permissions
- Data ownership: Stored on provider's infrastructure

### Shared Responsibility Model

Cloud security operates on **shared responsibility** principle:

**Provider Responsibilities (IaaS):**
- Physical infrastructure security
- Hypervisor security
- Storage security
- Network infrastructure

**Customer Responsibilities (IaaS):**
- Guest OS patching
- Application security
- Data encryption (client-side)
- Access control and authentication
- Data classification and protection

| Layer | IaaS | PaaS | SaaS |
|-------|------|------|------|
| Applications | Customer | Shared | Provider |
| Data | Customer | Customer | Shared |
| Runtime | Customer | Provider | Provider |
| Middleware | Customer | Provider | Provider |
| OS | Customer | Provider | Provider |
| Virtualization | Provider | Provider | Provider |
| Storage | Provider | Provider | Provider |
| Servers | Provider | Provider | Provider |
| Networking | Provider | Provider | Provider |

### Core Security Concepts

**Confidentiality:** Data is accessible only to authorized parties
- Achieved through: Encryption, access controls, authentication

**Integrity:** Data is accurate, complete, and unmodified
- Achieved through: Digital signatures, checksums, audit logs

**Availability:** Systems and data are accessible when needed
- Achieved through: Redundancy, failover, disaster recovery

**Non-repudiation:** Parties cannot deny their actions
- Achieved through: Digital signatures, audit trails

---

## Identity and Access Management (IAM)

### Core IAM Concepts

**Identity:**
- Who are you?
- Verified through authentication (passwords, MFA, certificates)
- Examples: user@company.com, service account, API key

**Access:**
- What resources can you access?
- Defined through authorization (permissions, roles, policies)
- Examples: "Can read S3 bucket", "Can modify EC2 instances"

**Management:**
- System that controls identity verification and access
- Provides: User provisioning, authentication, authorization, audit

### IAM Architecture

```
┌─────────────────────────────────────────┐
│         User / Application               │
└──────────────────┬──────────────────────┘
                   │ 1. Authentication
┌──────────────────▼──────────────────────┐
│    Authentication Service               │
│  (Verify credentials/tokens)            │
└──────────────────┬──────────────────────┘
                   │ 2. Authorized?
┌──────────────────▼──────────────────────┐
│    Authorization Service                │
│  (Check permissions/roles)              │
└──────────────────┬──────────────────────┘
                   │ 3. Grant/Deny Access
┌──────────────────▼──────────────────────┐
│    Protected Resource                   │
│  (Service/Data/Application)             │
└─────────────────────────────────────────┘
```

### IAM Components

**Users:** Individual identities
```
User: john.doe@company.com
Credentials: password, access keys, MFA device
```

**Groups:** Collections of users with common requirements
```
Group: Developers
Members: john.doe, jane.smith, bob.johnson
Permissions: Deploy to test environment
```

**Roles:** Sets of permissions
```
Role: EC2-Admin
Permissions: 
  - ec2:*
  - elasticloadbalancing:*
  - cloudwatch:*
```

**Policies:** Explicit permissions/denials
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject"],
      "Resource": "arn:aws:s3:::mybucket/*"
    }
  ]
}
```

### Principle of Least Privilege (PoLP)

Users should have **minimum permissions** necessary to perform their job:

**Bad Practice:**
```
Give John "Administrator" role
- Can delete everything
- Can modify security settings
- Can access sensitive data
- Risk: Accidental deletion, insider threat
```

**Good Practice:**
```
Give John specific permissions:
- ec2:DescribeInstances (view only)
- ec2:RebootInstances (restart)
- cloudwatch:GetMetricData (view metrics)
- Risk: Limited, John can only do his job
```

### Authentication Methods

**Single Factor:**
- Password: User memorizes secret
- Limitation: Passwords are weak, often reused

**Multi-Factor Authentication (MFA):**
1. Something you know: Password
2. Something you have: Authenticator app / SMS / Hardware key
3. Something you are: Biometric (fingerprint, face)

**MFA Examples:**
- Google Authenticator + Password
- SMS code + Password (less secure)
- Hardware key (YubiKey) + Password (most secure)

**Passwordless Authentication:**
- Certificates: SSH keys, X.509 certificates
- Tokens: OAuth tokens, JWT tokens
- Biometric: Fingerprint, face recognition

---

## Data Protection and Encryption

### Encryption Fundamentals

**Encryption at Rest:** Data stored on disk
```
Plaintext: "Credit Card: 4532-1234-5678-9012"
              ↓ [Encrypt with key]
Ciphertext: "k7f9j2h4k7f9j2h4k7f9j2h4k7f9j2h4"
              ↓ [Decrypt with key]
Plaintext: "Credit Card: 4532-1234-5678-9012"
```

**Encryption in Transit:** Data moving over network
- TLS/SSL: HTTPS, encrypted email
- VPN: Point-to-point encryption
- Certificates: Verify server authenticity

**Encryption in Use:** Data actively processed
- Homomorphic encryption: Compute on encrypted data
- Trusted execution environments (TEE): Isolated CPU regions
- Memory encryption: RAM is encrypted

### Encryption Algorithms

**Symmetric Encryption:**
- Same key for encryption and decryption
- Fast, suitable for large data
- Challenge: Secure key distribution
- Examples: AES-256, DES (deprecated)

**Asymmetric Encryption:**
- Public key (shared) and private key (secret)
- Slower, suitable for small data or key exchange
- Solves key distribution problem
- Examples: RSA, ECC

**Hash Functions:**
- One-way (irreversible) function
- Input → consistent output of fixed size
- Use cases: Password storage, data integrity verification
- Examples: SHA-256, MD5 (deprecated)

### Key Management

**Key Generation:**
- Secure randomness required
- Longer keys = stronger security
- Recommended: AES-256 (256-bit keys)

**Key Storage:**
- Hardware Security Modules (HSM): Dedicated hardware
- Key Management Services: AWS KMS, Azure Key Vault
- Never hardcode keys in source code

**Key Rotation:**
- Regularly replace encryption keys
- Schedule: Annual or per compliance requirement
- Impact: Performance overhead, complexity

**Key Access Control:**
```
Only authorized users/services can:
✓ Use key to encrypt/decrypt
✗ View key material
✗ Export key
```

### Field-Level Encryption

**Transparent Encryption (Database level):**
- Database automatically encrypts specified columns
- Application sees plaintext; database encrypts/decrypts
- Overhead: 5-15% performance

**Client-Side Encryption:**
- Application encrypts before sending to cloud
- Cloud stores encrypted data, cannot see plaintext
- Advantage: Strongest security (zero-knowledge)
- Disadvantage: Cannot search encrypted data

**Example: Patient Medical Records**
```
Database Encryption:
- Patient Name: encrypted
- SSN: encrypted
- Diagnosis: plaintext (searchable)
- Risk: Database admin can see plaintext with decryption key

Client-Side Encryption:
- Application encrypts Diagnosis before sending to cloud
- Cloud stores: gibberish
- Only patient's app can decrypt
- Risk: None for cloud provider
```

---

## Network Security

### Virtual Private Cloud (VPC) Security

**Network Segmentation:**
```
Internet
   │
   ▼
┌─────────────────────────────────┐
│  VPC (172.31.0.0/16)            │
│                                  │
│  ┌────────────────────────────┐ │
│  │ Public Subnet (Prod)       │ │
│  │ 172.31.1.0/24             │ │
│  │ ┌──────────┐              │ │
│  │ │ Web Tier │ (ALB, Nginx) │ │
│  │ └──────┬───┘              │ │
│  └────────┼──────────────────┘ │
│           │                     │
│  ┌────────▼──────────────────┐ │
│  │ Private Subnet (App)      │ │
│  │ 172.31.2.0/24             │ │
│  │ ┌──────────┐              │ │
│  │ │ App Tier │ (Node.js)    │ │
│  │ └──────┬───┘              │ │
│  └────────┼──────────────────┘ │
│           │                     │
│  ┌────────▼──────────────────┐ │
│  │ Private Subnet (DB)       │ │
│  │ 172.31.3.0/24             │ │
│  │ ┌──────────┐              │ │
│  │ │ Database │ (RDS)        │ │
│  │ └──────────┘              │ │
│  └────────────────────────────┘ │
└─────────────────────────────────┘
```

**Security Groups (Stateful Firewall):**
```
Inbound Rules (Allow):
- Port 443/80: HTTP/HTTPS from 0.0.0.0/0
- Port 22: SSH from 10.0.0.0/8 (internal)

Outbound Rules (Allow):
- All traffic to 0.0.0.0/0 (default)

Result:
✓ Public can access web server on port 80
✗ Public cannot SSH to web server (blocked)
✓ App tier can reach database (security group rule)
```

**Network ACLs (Stateless Firewall):**
- Layer 4 (network level) firewall
- Subnet-level (affects all traffic in/out)
- Stateless: Must define both inbound and outbound

### DDoS Protection

**DDoS Attack Types:**

**Volumetric Attack:**
- Overwhelm bandwidth
- Example: 100 Gbps flood of UDP packets
- Mitigation: CDN, traffic filtering, rate limiting

**Protocol Attack:**
- Exploit weaknesses in network protocols
- Example: SYN flood (exhaust server resources)
- Mitigation: WAF, SYN cookies, connection limits

**Application Attack:**
- Target application logic
- Example: Slowloris (slow HTTP requests)
- Mitigation: WAF, rate limiting, bot detection

**Mitigation Strategies:**

1. **Detection:**
   - Anomaly detection: Sudden traffic spike
   - Signature matching: Known DDoS patterns

2. **Mitigation:**
   - Rate limiting: Limit requests per IP
   - Traffic scrubbing: Filter malicious traffic
   - Anycast network: Distribute traffic across geographies

3. **Tools:**
   - AWS Shield Standard (free, automatic protection)
   - AWS Shield Advanced (additional DDoS protection)
   - AWS WAF (Web Application Firewall)
   - CloudFlare DDoS protection

---

## Cloud Compliance and Governance

### Major Compliance Standards

**HIPAA (Health Insurance Portability and Accountability Act):**
- **Scope:** Healthcare data
- **Requirements:**
  - Encrypt PHI (Protected Health Information)
  - Audit logging of access
  - Data breach notification within 60 days
  - Minimum necessary principle

**PCI-DSS (Payment Card Industry Data Security Standard):**
- **Scope:** Payment card data
- **Requirements:**
  - Encrypt cardholder data
  - Secure networks
  - Regular security testing
  - Access control
  - Incident response plan

**GDPR (General Data Protection Regulation):**
- **Scope:** Personal data of EU residents
- **Requirements:**
  - Right to access/delete personal data
  - Data portability
  - Privacy by design
  - Data breach notification within 72 hours
  - Data protection impact assessment (DPIA)

**SOC 2 (Service Organization Control):**
- **Scope:** Service providers handling customer data
- **Covers:**
  - Security, availability, processing integrity, confidentiality, privacy

### Governance Framework

**Policy:**
- Written rules and procedures
- Example: "All data at rest must be encrypted with AES-256"

**Standards:**
- Technical specifications for compliance
- Example: "Use TLS 1.3 or higher for data in transit"

**Procedures:**
- Step-by-step implementation guides
- Example: "Key rotation procedure: Notify team → Generate new key → Update applications → Archive old key"

**Controls:**
- Mechanisms to enforce compliance
- Detective: Audit logs, alerts
- Preventive: Encryption, access restrictions
- Corrective: Incident response procedures

### Audit and Compliance Assessment

**Internal Audit:**
- Company-conducted review
- Frequency: Annually or per policy
- Tools: Security scanners, penetration testing, manual review

**Third-Party Audit:**
- Independent auditor (Big 4 accounting firms)
- For compliance standards: HIPAA, PCI-DSS, SOC 2
- Result: Compliance certificate (valid 1-3 years)

**Continuous Compliance:**
- Cloud provider undergoes regular audits
- Certifications: ISO 27001, SOC 2 Type II
- Shared responsibility: Provider handles infrastructure compliance

---

## Security Best Practices

### Defense in Depth

**Layer 1 - Physical:**
- Data center access control
- Surveillance, guards
- Locked cabinets

**Layer 2 - Network:**
- Firewalls, VPCs
- Intrusion detection
- DDoS protection

**Layer 3 - Identity:**
- MFA, strong passwords
- Least privilege access
- Audit logging

**Layer 4 - Application:**
- Input validation
- SQL injection prevention
- Secure coding practices

**Layer 5 - Data:**
- Encryption at rest/transit
- Data classification
- Backup and recovery

### Secure Development Lifecycle (SDLC)

```
1. Planning
   └─ Security requirements defined
   
2. Design
   └─ Threat modeling, security architecture
   
3. Development
   └─ Secure coding, code reviews
   
4. Testing
   └─ Security testing, penetration testing
   
5. Deployment
   └─ Security hardening, configuration review
   
6. Operations
   └─ Monitoring, patch management, incident response
```

### Incident Response

**Preparation:**
- Incident response plan documented
- Team trained and assigned roles
- Contact list maintained

**Detection:**
- Security alerts trigger investigation
- Example: Unusual access patterns, failed login attempts

**Analysis:**
- Determine: What happened? Who was affected? How did it happen?
- Scope assessment: How many systems/users impacted?

**Containment:**
- Stop the attack
- Isolate affected systems
- Revoke compromised credentials

**Eradication:**
- Remove malware, patch vulnerabilities
- Change compromised passwords

**Recovery:**
- Restore from backup
- Verify systems are clean
- Bring systems back online

**Post-Incident:**
- Root cause analysis
- Lessons learned
- Improve controls to prevent recurrence

---

## Summary

Cloud security requires layered approach:
- **IAM:** Control who accesses what
- **Encryption:** Protect data confidentiality
- **Network Security:** Segment and protect traffic
- **Compliance:** Adhere to regulations
- **Incident Response:** React to security events

**Key Takeaway:** Security is shared responsibility. Cloud provider secures infrastructure; you secure your applications, identities, and data.

