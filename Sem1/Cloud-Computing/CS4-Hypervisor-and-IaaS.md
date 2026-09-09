# CS4: Hypervisors and Introduction to IaaS

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**
>
> **References:** T1: Ch2, Ch9 | T2: Ch3, Ch5 | R3 | R8
>
> **Contact Hours:** 7-8, 13-14 (Handout Topics 2.4 + 3.1-3.4)
> **Class Slide:** CS4 - HYPERVISOR_IAAS

---

## Table of Contents

- [2.4 x86 Hardware Virtualization (Detailed)](#24-x86-hardware-virtualization-detailed)
- [3.1 Introduction to IaaS](#31-introduction-to-iaas)
- [3.2 IaaS Architecture and Reference Model](#32-iaas-architecture-and-reference-model)
- [3.3 AWS as an IaaS Reference Platform](#33-aws-as-an-iaas-reference-platform)
- [3.4 Regions, Availability Zones, and Edge Locations](#34-regions-availability-zones-and-edge-locations)
- [VM Provisioning and Migration (from Lecture 7)](#vm-provisioning-and-migration)

---

## 2.4 x86 Hardware Virtualization (Detailed)

### The Ring Problem Revisited

The x86 architecture uses four privilege levels (Ring 0-3). The OS kernel needs Ring 0 for hardware access. In virtualization, the hypervisor also needs Ring 0. This creates a conflict since only one entity can have Ring 0 at a time.

### Solutions Implemented in Production

| Solution | How it works | Used by |
|---|---|---|
| **Binary Translation** | Hypervisor at Ring 0, guest OS at Ring 1. Hypervisor scans guest instructions and replaces privileged ones at runtime. | Early VMware (before VT-x) |
| **Para-virtualization** | Guest OS kernel modified to make hypercalls directly to hypervisor. No trapping needed. | Xen (PV mode) |
| **Hardware-Assisted (VT-x/AMD-V)** | CPU adds VMX root mode (hypervisor) and non-root mode (guest). Hardware traps privileged instructions automatically. | KVM, ESXi, Hyper-V — **dominant today** |
| **Extended Page Tables (EPT/RVI)** | Hardware handles two-level address translation (guest virtual → guest physical → host physical) without software shadow page tables. | All modern hypervisors |

### BOCHS — Pure Emulation (Contrast with Virtualization)

**BOCHS** is an open-source **x86 PC emulator** that takes a fundamentally different approach from hypervisors. Instead of using hardware-assisted virtualization (VT-x/AMD-V), BOCHS uses **pure interpretation** — it emulates the entire x86 hardware stack (CPU, memory, I/O devices, BIOS) in software, translating every single guest instruction at runtime.

This makes BOCHS **extremely slow** compared to hypervisor-based virtualization (orders of magnitude slower), but it is invaluable for **OS development, debugging, and education**. Because BOCHS emulates hardware entirely in software, it can run on any host architecture and provides deep introspection into guest behaviour — you can step through individual CPU instructions, inspect register state, and debug boot sequences that would be opaque on a real hypervisor.

**Why it matters for this course:** BOCHS illustrates the spectrum from pure emulation (interpret every instruction → portable but slow) to hardware-assisted virtualization (trap-and-emulate with VT-x → near-native speed). Modern cloud hypervisors (KVM, ESXi) sit at the fast end of this spectrum because they let the CPU execute guest instructions directly in hardware, only trapping on privileged operations.

### Hypervisor Deep-Dive

#### Type 1 Hypervisors in Production

| Hypervisor | Developer | Key characteristics | Used by |
|---|---|---|---|
| **KVM** (Kernel-based Virtual Machine) | Open-source (Linux) | Built into Linux kernel. Uses hardware virtualization (VT-x). Lightweight — the Linux kernel IS the hypervisor. | AWS (Nitro/KVM), Google Cloud, DigitalOcean, Red Hat |
| **VMware ESXi** | VMware (Broadcom) | Commercial bare-metal hypervisor. Mature ecosystem (vSphere, vCenter, vMotion). Industry standard for enterprise. | Enterprise private clouds, VMware Cloud on AWS |
| **Microsoft Hyper-V** | Microsoft | Built into Windows Server. Strong Windows VM support. Also runs Linux guests. | Azure, enterprise Windows environments |
| **Xen** | Open-source (Linux Foundation) | Pioneered para-virtualization. Supports both PV and HVM modes. | Citrix (XenServer), early AWS (before Nitro) |

#### Type 2 Hypervisors (Development/Testing Only)

| Hypervisor | Platform | Use case |
|---|---|---|
| **Oracle VirtualBox** | Cross-platform (free) | Development, testing, learning |
| **VMware Workstation/Fusion** | Windows/macOS (commercial) | Professional development |
| **Parallels Desktop** | macOS (commercial) | Running Windows on Mac |

#### AWS Nitro System — Modern Cloud Hypervisor

AWS evolved from Xen to the **Nitro System**, a custom hypervisor architecture:

- **Nitro Hypervisor:** Lightweight KVM-based hypervisor. Offloads networking, storage, and security to dedicated Nitro hardware cards.
- **Nitro Cards:** Custom ASIC chips that handle VPC networking, EBS storage I/O, and instance monitoring — removing this work from the host CPU.
- **Nitro Security Chip:** Hardware-based security that prevents unauthorized access to instance storage and memory.
- **Result:** Near-bare-metal performance for VMs because the hypervisor overhead is offloaded to hardware.

---

## 3.1 Introduction to IaaS

### What is IaaS?

**Infrastructure as a Service (IaaS)** is the delivery of computing infrastructure — servers, storage, networking, and data centre resources — as on-demand, pay-per-use services over the internet.

From the lecture notes: *"The delivery of services such as hardware, software, storage, networking, data center space, and various utility software elements on request. Both public and private versions of IaaS exist."*

### Key Characteristics of IaaS

| Characteristic | Description |
|---|---|
| **On-demand provisioning** | Users can spin up VMs, storage, and networks in minutes via a self-service portal or API. No procurement cycle. |
| **Pay-per-use** | Billed based on resource consumption (per hour of compute, per GB of storage, per GB of data transfer). No upfront CapEx. |
| **Elastic scaling** | Scale resources up during peaks, down during lulls. Automatically or manually. |
| **Multi-tenancy** | Multiple customers share the same physical infrastructure, isolated by hypervisor and virtual networking. |
| **Self-service** | Users provision resources through web consoles, CLIs, or APIs without contacting the provider. |
| **Dynamic** | Infrastructure can be reconfigured, resized, or destroyed at any time. |

### Public vs Private IaaS

| Aspect | Public IaaS | Private IaaS |
|---|---|---|
| **Provider** | Third-party cloud provider (AWS, Azure, GCP) | Organisation's own IT team or dedicated third party |
| **Users** | Anyone with a credit card | Internal users and sometimes partners |
| **Infrastructure** | Shared across thousands of customers | Dedicated to one organisation |
| **Sign-up** | Simple online registration | Internal approval process |
| **Examples** | AWS EC2, Azure VMs, Google Compute Engine | OpenStack, VMware vSphere, AWS Outposts |

### Why IaaS Matters

IaaS is the **foundation** on which PaaS and SaaS are built. The IaaS provider manages:
- Physical servers, racks, data centres
- Power, cooling, physical security
- Networking hardware (switches, routers, firewalls)
- Hypervisor layer
- Storage hardware (SAN, NAS, SSDs)

The customer manages:
- Operating system (install, patch, configure)
- Middleware and runtime
- Applications and data
- Security configuration (firewalls, IAM)

---

## 3.2 IaaS Architecture and Reference Model

### The IaaS Stack

```
┌─────────────────────────────────────────────────┐
│           Customer-Managed Layer                  │
│  Applications, Data, Runtime, Middleware, OS      │
├─────────────────────────────────────────────────┤
│           Virtualisation Layer                    │
│  VMs, Containers, Virtual Networks, Virtual Disks │
├─────────────────────────────────────────────────┤
│           Physical Infrastructure                 │
│  Compute (servers), Storage (SAN/SSD),            │
│  Network (switches/routers), Data Centres         │
├─────────────────────────────────────────────────┤
│           Management & Orchestration              │
│  Provisioning, Monitoring, Billing, IAM,          │
│  Auto-scaling, Load Balancing                     │
└─────────────────────────────────────────────────┘
```

### IaaS Components

| Component | What it provides | AWS Example |
|---|---|---|
| **Compute** | Virtual machines (instances) with configurable CPU, memory, GPU | EC2 (Elastic Compute Cloud) |
| **Storage** | Block storage (virtual disks), Object storage (files), File storage (shared) | EBS (block), S3 (object), EFS (file) |
| **Networking** | Virtual private clouds, subnets, load balancers, DNS, CDN | VPC, ELB, Route 53, CloudFront |
| **Security** | Firewalls, identity management, encryption, DDoS protection | Security Groups, IAM, KMS, Shield |
| **Management** | Monitoring, auto-scaling, provisioning APIs, billing | CloudWatch, Auto Scaling, CloudFormation |

---

## 3.3 AWS as an IaaS Reference Platform

### Why AWS as Reference?

AWS is the largest and most mature cloud provider (33% market share as of 2024). It launched in 2006 and has the broadest service catalogue. The lecture notes use AWS as the primary reference for IaaS concepts.

From lecture notes: *"Amazon launched AWS so that other organizations could benefit from Amazon's experience and investment in running a large-scale distributed, transactional IT infrastructure. AWS has been operating since 2006, and today serves hundreds of thousands of customers worldwide."*

### AWS Distinguishing Characteristics (from Lecture 5-6)

| Characteristic | Description |
|---|---|
| **Flexible** | Supports any programming model, OS, database, and architecture. Mix and match as needed. |
| **Cost-effective** | Pay only for what you use. No upfront commitments. |
| **Scalable and elastic** | Add/remove resources to meet demand and manage costs. |
| **Secure** | Built with security best practices. End-to-end encryption. Compliance certifications. |
| **Experienced** | Leverages Amazon's 15+ years of large-scale infrastructure experience. |

### Key AWS IaaS Services Overview

| Category | Service | What it does |
|---|---|---|
| **Compute** | EC2 | Resizable VMs in the cloud. Full OS control. |
| **Compute** | Auto Scaling | Automatically scale EC2 capacity based on demand. |
| **Compute** | Elastic Load Balancing | Distribute traffic across multiple EC2 instances. |
| **Networking** | VPC | Isolated virtual network you define. |
| **Networking** | Route 53 | DNS service (domain name → IP address). |
| **Storage** | S3 | Object storage — any amount of data, any time, from anywhere. |
| **Storage** | EBS | Block storage volumes attached to EC2 instances. |
| **Storage** | Glacier | Extremely low-cost archival storage. |
| **CDN** | CloudFront | Content delivery via global edge locations. |
| **Management** | CloudWatch | Monitoring for cloud resources and applications. |
| **Deployment** | Elastic Beanstalk | PaaS-like deployment for web apps (deploys on EC2 behind the scenes). |

### AWS IAM (Identity and Access Management)

**IAM** is the AWS service that controls **who** (authentication) can do **what** (authorization) in your AWS account. Every API call to AWS is checked against IAM policies. It is a global service — not tied to any single region.

#### Authentication — "Who are you?"

| Method | Used for | How it works |
|---|---|---|
| **Username + Password** | AWS Management Console (web UI) | Human users sign in via browser. |
| **Access Key ID + Secret Access Key** | AWS CLI and SDKs (programmatic access) | Long-lived credentials used in scripts and applications. |
| **MFA (Multi-Factor Authentication)** | Added security layer on top of password or keys | Requires a second factor (virtual MFA app, hardware token) in addition to credentials. |

#### Authorization — "What are you allowed to do?"

Permissions are defined by **IAM Policies** and evaluated every time an API call is made. By default, all actions are **denied** — you must explicitly grant access.

#### IAM Identities

| Identity | Description | Use case |
|---|---|---|
| **User** | A person or application with permanent credentials (password, access keys). | Individual developers, CI/CD service accounts. |
| **Group** | A collection of users. Policies attached to the group apply to all its members. | `Developers` group, `Admins` group. |
| **Role** | An identity **without permanent credentials**. Provides temporary security credentials via AWS STS. No username/password — assumed by whoever or whatever needs it. | EC2 instances accessing S3, Lambda functions calling DynamoDB, cross-account access. |

**Key insight:** Roles are preferred over access keys for services. An EC2 instance assumes a role and receives short-lived credentials that rotate automatically — no risk of leaked long-term keys.

#### IAM Policies

Policies are **JSON documents** that define permissions. They specify:
- **Effect** — `Allow` or `Deny`
- **Action** — the AWS API action (e.g. `s3:GetObject`, `ec2:StartInstances`)
- **Resource** — the specific AWS resource (identified by ARN)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-bucket/*"
    },
    {
      "Effect": "Deny",
      "Action": "s3:DeleteObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

This policy allows reading and writing objects in `my-bucket` but explicitly denies deletion. Policies are attached to users, groups, or roles.

#### IAM Best Practices

| Practice | Why |
|---|---|
| **Use root account only for initial setup** | Root has unrestricted access — too dangerous for daily use. |
| **Enable MFA on root account** | Protects against compromised root credentials. |
| **Use roles, not access keys, for services** | Roles provide temporary credentials that rotate automatically. Access keys are long-lived and can be leaked. |
| **Principle of least privilege** | Grant only the minimum permissions needed. Start with zero access and add permissions as required. |
| **Use groups for permission assignment** | Attach policies to groups, then add users to groups. Easier to manage than per-user policies. |
| **Rotate credentials regularly** | Minimises exposure if keys are compromised. |

---

## 3.4 Regions, Availability Zones, and Edge Locations

### The AWS Global Infrastructure Hierarchy

```
Region (e.g. ap-south-1 = Mumbai)
├── Availability Zone 1 (ap-south-1a) — one or more data centres
├── Availability Zone 2 (ap-south-1b) — separate power, network, cooling
└── Availability Zone 3 (ap-south-1c) — physically separated (km apart)

Edge Locations — 600+ worldwide (CDN caching points)
```

| Concept | What it is | Why it matters |
|---|---|---|
| **Region** | A geographic area with multiple AZs. Completely independent from other regions. Data stays within a region unless you move it. | **Data sovereignty** — comply with regulations requiring data to stay in specific countries. **Latency** — deploy close to users. |
| **Availability Zone (AZ)** | One or more data centres within a region with independent power, cooling, networking. Connected to other AZs by low-latency links. | **High availability** — deploy across multiple AZs so if one fails, others keep running. AWS SLAs require multi-AZ deployment. |
| **Edge Location** | Small data centres at the network edge worldwide. Used by CloudFront (CDN) and Route 53. | **Low latency** — cache content close to end users. A user in Delhi gets content from a Delhi edge location, not from Mumbai region. |

### Designing for High Availability

**Single-AZ deployment:** If that AZ fails, your application is down.

**Multi-AZ deployment:** Application runs in 2+ AZs. If one fails, the load balancer routes traffic to healthy AZs. This is the **minimum recommended architecture** for production workloads.

**Multi-Region deployment:** Application runs in 2+ regions. If an entire region has an outage (rare but happens), the other region takes over. Used for mission-critical, globally distributed applications.

---

## VM Provisioning and Migration

*(From Lecture 7 — VM Management)*

### VM Lifecycle

A virtual machine goes through distinct phases during its life:

```
1. IT Service Request
   → Analyse server resource pool
   → Match resources with requirements
       ↓
2. VM Provisioning
   → Load OS + applications on VM instance
   → Customise and configure (IP, network, storage)
   → Start the server
       ↓
3. VM in Operation
   → Serves requests
   → Supports migration
   → Scale on-demand compute resources
       ↓
4. Release VM
   → End of service
   → Compute resources reallocated to another VM
```

### VM Provisioning Process

1. **Select server** from pool of available servers with appropriate OS template.
2. **Load software** — device drivers, middleware, application packages.
3. **Customise and configure** — IP address, network connectivity, storage attachment.
4. **Virtual server is ready** to serve requests.

In cloud: This entire process takes **minutes** (e.g. AWS EC2 provisioning). In traditional IT: This takes **weeks** (procurement, shipping, racking, cabling).

### VM Migration Techniques

| Technique | What moves | VM state during migration | Shared storage required? | Downtime |
|---|---|---|---|---|
| **Hot/Live Migration** | Running VM from one physical host to another. VM stays powered on. | Running | Yes | Milliseconds (almost none) |
| **Cold/Regular Migration** | Powered-off VM from one host to another. Disks and config files moved. | Powered off | No | Minutes (VM is off during move) |
| **Live Storage Migration** | VM's virtual disks and config from one data store to another. VM stays running. | Running | No (moving between stores) | None |

### Live Migration Algorithm (Xen Hypervisor — from Lecture 7)

| Stage | What happens |
|---|---|
| **Stage 0: Pre-Migration** | Active VM exists on Host A. |
| **Stage 1: Reservation** | Request sent to migrate VM from Host A to Host B. Resources reserved on B. |
| **Stage 2: Iterative Pre-Copy** | First iteration: ALL memory pages copied from A to B. Subsequent iterations: only **dirtied pages** (pages modified since last copy) are re-copied. Each iteration copies fewer pages. |
| **Stage 3: Stop-and-Copy** | VM on A is suspended. Remaining dirty pages + CPU state copied to B. Network traffic routed to B. Consistent copy now exists on both A and B. |
| **Stage 4: Commitment** | B confirms successful receipt of complete VM image. A acknowledges. Original VM on A is discarded. B becomes primary host. |
| **Stage 5: Activation** | Migrated VM on B is activated. Device drivers reattached. IP addresses advertised on new network. |

**Key insight:** Most of the data is copied WHILE the VM is still running (pre-copy). The actual downtime (stop-and-copy) is only milliseconds because only the last few dirty pages need to be transferred.

### Cloud Provisioning Platforms (from Lecture 7)

| Platform | Type | Key feature |
|---|---|---|
| **Amazon EC2** | Public cloud | Provision VMs in minutes via API. Pay-as-you-use. Auto Scaling + CloudWatch + ELB. |
| **Eucalyptus** | Private/Hybrid cloud | Open-source. API-compatible with AWS (EC2, S3, EBS). Runs on-premises. |
| **OpenNebula** | Private/Hybrid/Public cloud | Open-source VM orchestrator. Supports KVM, VMware, Xen. Manages storage, network, and compute. |
| **Aneka** | Distributed cloud | .NET-based platform for building distributed applications on cloud. APIs for resource management. |

---

*End of CS4*
