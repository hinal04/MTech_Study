# Session 7: Backup, Recovery, and Database Administration

> BITS Pilani — **SSZG507: Modern Database Systems**

---

## Table of Contents

- [7.1 Database Administration Overview](#71-database-administration-overview)
- [7.2 Backup Strategies](#72-backup-strategies)
- [7.3 Recovery Mechanisms](#73-recovery-mechanisms)
- [7.4 High Availability](#74-high-availability)
- [7.5 Security and Access Control](#75-security-and-access-control)
- [7.6 Monitoring and Maintenance](#76-monitoring-and-maintenance)

---

## 7.1 Database Administration Overview

### DBA Responsibilities

1. **Planning:** Capacity planning, growth forecasting
2. **Setup:** Install, configure, optimize
3. **Backup/Recovery:** Ensure data protection, test restores
4. **Monitoring:** Watch performance, alerts
5. **Tuning:** Optimize queries, indexes, resources
6. **Security:** Access control, encryption, compliance
7. **Maintenance:** Updates, patches, maintenance windows
8. **Troubleshooting:** Diagnose and fix issues

### DBA Skills Required

- Database technology (SQL, architecture)
- System administration (OS, networking)
- Performance tuning and optimization
- Backup/recovery procedures
- Security and compliance
- Problem-solving and communication

---

## 7.2 Backup Strategies

### Backup Types

**Full Backup:**
- Complete database copy
- Time-consuming, storage-intensive
- Restore time: Fast (one step)
- Frequency: Weekly or monthly

**Incremental Backup:**
- Only data changed since last backup
- Fast, storage-efficient
- Restore time: Slow (apply all increments)
- Frequency: Daily

**Differential Backup:**
- Data changed since last full backup
- Compromise between full and incremental
- Restore time: Moderate
- Frequency: Daily

**Transaction Log Backup:**
- Copy transaction log
- Enables point-in-time recovery
- Frequency: Minutes or seconds

### Backup Strategy Example

```
Monday: Full backup (8 hours, 100 GB)
Tuesday-Friday: Incremental (30 min each)
Every hour: Transaction log backup (1 GB each)

Recovery from Thursday 2 PM failure:
1. Restore Monday full backup
2. Apply Tuesday-Thursday incremental backups
3. Apply transaction logs up to 2 PM
→ Full recovery with minimal data loss
```

### Backup Tools

**PostgreSQL:**
```bash
# Full backup
pg_dump -U postgres mydb > backup.sql

# Physical backup (binary)
pg_basebackup -D /backup -F tar -z
```

**MySQL:**
```bash
# Logical backup
mysqldump -u root -p mydb > backup.sql

# Physical backup
tar -czf backup.tar.gz /var/lib/mysql
```

**RDBMS-Agnostic:**
- Percona XtraBackup (MySQL)
- pg_basebackup (PostgreSQL)
- RMAN (Oracle)

---

## 7.3 Recovery Mechanisms

### Recovery Types

**Crash Recovery:**
- Database crashed; restart recovery
- Uses transaction log
- Automatic on restart

**Media Recovery:**
- Disk failure; restore from backup
- Restore backup, apply logs
- Manual process

**Point-in-Time Recovery:**
- Recover to specific timestamp
- Replay logs up to point
- Used for logical errors (accidental delete)

### ACID Recovery

**Atomicity:** Incomplete transactions rolled back
**Consistency:** Constraints enforced
**Isolation:** Concurrent transactions not interfered
**Durability:** Committed data survives crashes

### Recovery Procedure

```
1. Restore database from latest full backup
2. Apply differential/incremental backups
3. Apply transaction logs in order
4. Database now at point-in-time state
5. Verify data integrity
6. Restart application connections
```

### Testing Restores

**Critical:** Test backup restoration regularly
- Restore from each backup type
- Verify data integrity
- Test time to restore
- Document restoration procedure

---

## 7.4 High Availability

### Replication

**Master-Slave (Primary-Replica):**
- Master: Accepts writes
- Slave: Read-only copy of master
- Asynchronous replication
- Slave lags behind master

```
Application → [Master DB] → Binary Log →  [Slave DB]
              (Write)                       (Read-Only)
```

**Multi-Master (Active-Active):**
- Both nodes accept writes
- Changes replicated to both
- Risk: Conflict resolution needed

### Failover

If master fails:
1. Detect master is down
2. Promote slave to master (election)
3. Redirect writes to new master
4. Old master recovers as slave

**Downtime:** Seconds to minutes

### High Availability Solutions

**MySQL Group Replication:**
- Multi-master automatic failover
- Consistency guaranteed
- Automatic recovery

**PostgreSQL Streaming Replication:**
- Primary-replica setup
- Synchronous or asynchronous
- Failover tools: patroni, stolon

**Commercial Solutions:**
- Oracle Data Guard
- SQL Server AlwaysOn

---

## 7.5 Security and Access Control

### User Authentication

```sql
-- Create user
CREATE USER alice@'localhost' IDENTIFIED BY 'password123';

-- Create role (PostgreSQL)
CREATE ROLE analyst LOGIN PASSWORD 'password123';
```

### Authorization (Role-Based Access Control)

```sql
-- Grant privileges
GRANT SELECT, INSERT ON employees TO alice;
GRANT ALL PRIVILEGES ON database db TO dba_role;

-- Grant role to user
GRANT analyst_role TO alice;

-- Revoke privileges
REVOKE INSERT ON employees FROM alice;
```

### Row-Level Security

Restrict which rows users see:
```sql
-- PostgreSQL
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;

CREATE POLICY user_orders ON orders
  USING (user_id = current_user_id());

-- User sees only their orders
SELECT * FROM orders;
-- Returns only rows where user_id = current user
```

### Encryption

**At Rest:**
- Transparent Data Encryption (TDE)
- Encrypt database files on disk

**In Transit:**
- SSL/TLS for client-server communication

**Keys:**
- Secure key management
- Encryption key rotation

---

## 7.6 Monitoring and Maintenance

### Key Metrics

**Performance:**
- Query response time (latency)
- Throughput (queries/sec)
- CPU and memory usage
- Disk I/O (IOPS, throughput)

**Health:**
- Connection count
- Active transactions
- Lock wait times
- Replication lag

**Capacity:**
- Disk space free
- Table and index sizes
- Growth rate

### Monitoring Tools

**Built-in:**
- PostgreSQL: `pg_stat_statements`, `pg_stat_tables`
- MySQL: `performance_schema`

**Third-party:**
- Prometheus + Grafana
- DataDog
- New Relic
- Splunk

### Maintenance Tasks

**Regular:**
- Monitor disk space
- Review slow query logs
- Analyze table statistics
- Vacuum/garbage collection (PostgreSQL)

**Periodic:**
- Update statistics
- Rebuild fragmented indexes
- Archive old data
- Test backup restores

**Upgrades:**
- Test on staging first
- Plan maintenance window
- Have rollback plan

---

## Summary

Database administration is critical for reliable, secure, performant systems. Key responsibilities: backup/recovery, high availability, security, and monitoring. Effective DBA practices minimize downtime, protect data, and optimize performance.

