# CS9: Virtual Machine Provisioning and Migration

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**
>
> **References:**
> - T1: Buyya, Vecchiola & Selvi, *Mastering Cloud Computing*, Ch.13
> - R1: VMware vSphere Documentation
> - R2: AWS EC2 Migration Documentation

---

## Table of Contents

- [9.1 VM Provisioning Overview](#91-vm-provisioning-overview)
- [9.2 Provisioning Process](#92-provisioning-process)
- [9.3 Templates and Images](#93-templates-and-images)
- [9.4 Automated Provisioning](#94-automated-provisioning)
- [9.5 VM Migration Strategies](#95-vm-migration-strategies)
- [9.6 Live Migration](#96-live-migration)
- [9.7 Storage Migration](#97-storage-migration)
- [9.8 Challenges and Best Practices](#98-challenges-and-best-practices)

---

## 9.1 VM Provisioning Overview

### What is VM Provisioning?

Creation and configuration of virtual machines to be ready for running applications.

**Steps:**
1. Define VM specifications (CPU, memory, storage, network)
2. Allocate resources from physical infrastructure
3. Create VM from template/image
4. Configure network (IP, DNS, firewall)
5. Install software/applications
6. Deliver to user/application owner

### Provisioning Methods

**Manual Provisioning:**
- Administrator manually creates each VM
- Uses web console or management interface
- Slow; error-prone; not scalable

**Automated Provisioning:**
- Templates or Infrastructure-as-Code
- Scripts create VMs from definitions
- Fast; consistent; scalable

**Self-Service Provisioning:**
- Users request VMs via portal
- Policies enforce constraints
- Reduces IT dependency

---

## 9.2 Provisioning Process

### Typical Provisioning Workflow

```
1. Capacity Planning
   ↓
2. Request/Approval
   ↓
3. Resource Allocation
   ↓
4. Create VM from Template
   ↓
5. Network Configuration
   ↓
6. Software Installation
   ↓
7. Configuration Management
   ↓
8. Health Check & Validation
   ↓
9. Handoff to Owner
```

### Provisioning Timeline

Without automation: 2-6 weeks
- Procurement, setup, testing, deployment

With automation: Minutes to hours
- Network provisioning: 1-2 minutes
- VM creation: 5-10 minutes
- Software installation: 5-30 minutes
- Total: 15-50 minutes

### Provisioning Options

**On-Demand:**
- User requests; VM created immediately
- Auto-scaling provisions more VMs on demand
- Pay-per-VM pricing

**Scheduled:**
- VMs created on schedule (nightly batch jobs)
- Useful for predictable workloads

**Elastic:**
- Auto-scale based on metrics (CPU, memory, load)
- VMs provisioned/deprovisioned automatically

---

## 9.3 Templates and Images

### VM Templates

Pre-configured VM blueprint. Includes:
- Operating system
- Patches and updates
- Standard software (Java, Python, etc.)
- Configuration (hostname, domain)
- Security settings

**Template Format:**
- VMware: `.vmtx` (template file)
- AWS: AMI (Amazon Machine Image)
- KVM: QCOW2 or RAW disk image

### Golden Images

Optimized templates with all production requirements:
- Base OS + patches
- Standard software
- Monitoring agents
- Backup agents
- Security hardening
- Company-specific tools

**Creating Golden Image:**
```
1. Start with clean OS
2. Install and test all software
3. Apply security hardening
4. Install monitoring/backup agents
5. Optimize and clean
6. Capture as template
7. Version and document
```

### Image Management

**Image Repository:**
- Store multiple image versions
- Track image contents (inventory)
- Version control

**Image Lifecycle:**
```
Create → Test → Certify → Use → Deprecate → Archive
```

### Cloud-Init

Standard mechanism for VM customization at boot:

```yaml
#cloud-config
hostname: web-server
fqdn: web-server.example.com
users:
  - name: ubuntu
    sudo: ALL=(ALL) NOPASSWD:ALL
packages:
  - nginx
  - curl
  - git
runcmd:
  - systemctl start nginx
  - systemctl enable nginx
write_files:
  - path: /var/www/html/index.html
    content: |
      <h1>Welcome</h1>
```

---

## 9.4 Automated Provisioning

### Infrastructure-as-Code (IaC)

Define infrastructure in code files:

**Terraform Example:**
```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"  # Ubuntu 20.04
  instance_type = "t2.micro"
  
  tags = {
    Name = "web-server"
  }
  
  user_data = file("${path.module}/init.sh")
}

resource "aws_security_group" "web" {
  name = "web-sg"
  
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

Deploy:
```bash
terraform init
terraform plan
terraform apply
```

### Configuration Management

Ensure desired state after provisioning:

**Ansible Playbook:**
```yaml
---
- hosts: all
  tasks:
    - name: Update packages
      apt:
        update_cache: yes
    
    - name: Install nginx
      apt:
        name: nginx
        state: present
    
    - name: Start nginx
      service:
        name: nginx
        state: started
        enabled: yes
    
    - name: Deploy web app
      copy:
        src: ./app/
        dest: /var/www/html/
```

---

## 9.5 VM Migration Strategies

### Migration Types

**1. Physical to Virtual (P2V):**
- Convert physical server to VM
- Replicate disk, OS, applications
- Common when consolidating data centers

**2. Virtual to Virtual (V2V):**
- Move VM between hypervisors (ESXi to Xen)
- Between cloud providers
- Between versions of same hypervisor

**3. On-Premises to Cloud:**
- Lift-and-shift: Move VM to IaaS
- Re-architecture: Modify for cloud-native

### Migration Approaches

**Cold Migration (Offline):**
- Shut down VM
- Copy disk and state to destination
- Start VM on destination
- Downtime: Full duration of copy + startup

```
Source VM: [SHUTDOWN] → Copy Disk/Memory → Startup on Destination
```

**Warm Migration (Suspended):**
- Pause VM
- Copy memory and disk state
- Resume on destination
- Downtime: Seconds to minutes

```
Source VM: [PAUSE] → Copy State → Resume on Destination
```

**Hot/Live Migration (Online):**
- VM continues running during migration
- Memory copied iteratively
- Final pause only to switch network I/O
- Downtime: Milliseconds (imperceptible)

```
Source VM: [RUNNING] → Copy Memory Iteratively → Pause & Switch I/O
Destination: Receive Memory → Resume → [RUNNING]
```

### Live Migration Process

```
Step 1: Preparation
- Reserve resources on destination host
- Verify resources sufficient
- Check network connectivity

Step 2: Memory Copy (Iterative)
- Copy all memory pages to destination
- Mark copied pages dirty on source
- Repeat copy of dirty pages
- When dirty rate < copy rate, go to Step 3

Step 3: Final Switch
- Pause VM (few milliseconds)
- Copy remaining dirty pages
- Switch VM to destination
- Resume VM

Step 4: Cleanup
- Verify VM running on destination
- Release resources on source
- Update network/management systems
```

**Prerequisites for Live Migration:**
- Source and destination same CPU architecture
- Shared storage (NFS, SAN) or local migration
- Same hypervisor version (usually)
- Connected via fast network (1 Gbps+)

---

## 9.6 Live Migration

### Live Migration Benefits

✅ **Zero Downtime:** Application continues running
✅ **Load Balancing:** Move VMs between hosts based on load
✅ **Maintenance:** Evacuate host for patching/maintenance
✅ **Disaster Recovery:** Migrate VMs from failing host

### Live Migration Challenges

❌ **Complexity:** Requires hypervisor support; careful state tracking
❌ **Network:** Must be connected; migrating across WANs difficult
❌ **Storage:** Shared storage typically required (expensive)
❌ **Consistency:** Memory pages may change during copy

### Live Migration Optimization

**Pre-Copy (Standard):**
- Copy memory while VM running
- Minimizes downtime

**Post-Copy (Linux KVM):**
- Pause VM briefly
- Copy memory pages on-demand after resume
- May cause page faults initially

**Hybrid Approach:**
- Pre-copy initial memory
- Post-copy for remaining pages
- Balance between total time and downtime

---

## 9.7 Storage Migration

### Storage Migration Scenarios

**1. Changing Storage Backend:**
- SAN to SAN (different controllers)
- Local storage to SAN
- Storage aging (replace old drives)

**2. Data Center Migration:**
- Move storage arrays between data centers
- Geographically distributed deployments

**3. Cloud Migration:**
- On-prem storage to AWS S3, Azure Blob
- Requires replication and cutover

### Storage Migration Methods

**Snapshot-Based:**
```
Create Snapshot → Replicate Snapshot → Mount on Destination
Capture state at point-in-time
Good for: Testing, temporary copies
```

**Continuous Replication:**
```
Initial Copy → Continuous Sync → Failover
Keep copy in sync
Good for: Disaster recovery, planned migrations
```

**Block-Level Copy:**
```
Read blocks from source → Write to destination
Only copy changed blocks (incremental)
Good for: Efficiency, speed
```

### Storage Migration Challenges

- **Data Consistency:** Ensure no data loss during cutover
- **Downtime:** Copying large volumes takes time
- **Verification:** Validate data integrity after migration
- **Network Bandwidth:** May be bottleneck for large volumes

---

## 9.8 Challenges and Best Practices

### Challenges

**Planning:**
- Predicting resource needs
- Determining migration strategy
- Managing complexity at scale

**Execution:**
- Ensuring minimal downtime
- Maintaining data consistency
- Testing and validation

**Post-Migration:**
- Performance tuning on new host
- Network reconfiguration
- Backup restoration

**Scalability:**
- Migrating thousands of VMs
- Coordinating migrations to avoid load spikes
- Managing dependencies

### Best Practices

**Pre-Migration Planning:**
1. Audit current VMs (CPU, memory, storage, network)
2. Identify dependencies between VMs
3. Define rollback procedures
4. Test in non-production first
5. Schedule during maintenance window
6. Communicate with stakeholders

**During Migration:**
1. Monitor source and destination
2. Verify network connectivity
3. Validate application health on destination
4. Keep logs of migration events

**Post-Migration Validation:**
1. Verify all services running
2. Test application functionality
3. Check performance (CPU, memory, disk I/O, network)
4. Validate network connectivity
5. Confirm backups working
6. Monitor for 24-48 hours

**Automation:**
1. Use Infrastructure-as-Code for consistency
2. Automate validation checks
3. Create runbooks for manual steps
4. Test automation in labs before production

**Documentation:**
1. Document VM inventory
2. Record provisioning procedures
3. Maintain runbooks
4. Track migration history

---

## Summary

VM provisioning and migration are critical operational tasks in cloud environments. **Provisioning** involves creating and configuring VMs efficiently, often using templates and Infrastructure-as-Code. **Migration** moves VMs between hosts or data centers, with options ranging from simple offline migrations to sophisticated live migrations with zero downtime. Success requires careful planning, automation, testing, and validation to ensure consistency and minimize disruption.

