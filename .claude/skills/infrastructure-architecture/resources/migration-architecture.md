# Migration Architecture

Guide to planning and executing infrastructure migrations with minimal risk and downtime.

## Migration Patterns

### 1. Strangler Fig Pattern

**Gradually replace old system with new:**

```
Phase 1: Both systems running, proxy routes to old
┌──────┐     ┌─────────┐
│Client│────►│  Proxy  │────►Old System (100%)
└──────┘     └─────────┘

Phase 2: Route new features to new system
┌──────┐     ┌─────────┐────►Old System (90%)
│Client│────►│  Proxy  │
└──────┘     └─────────┘────►New System (10%)

Phase 3: Gradually shift traffic
┌──────┐     ┌─────────┐────►Old System (50%)
│Client│────►│  Proxy  │
└──────┘     └─────────┘────►New System (50%)

Phase 4: Complete migration
┌──────┐     ┌─────────┐
│Client│────►│  Proxy  │────►New System (100%)
└──────┘     └─────────┘     Old System (decommissioned)
```

**Example:**
```typescript
// API proxy routing old → new gradually
app.use((req, res, next) => {
  const rolloutPercentage = 20;  // Start with 20%

  // Feature flag or random routing
  if (Math.random() * 100 < rolloutPercentage) {
    return proxyToNewAPI(req, res);  // New system
  } else {
    return proxyToOldAPI(req, res);  // Old system
  }
});
```

**Advantages:**
- ✅ Low risk (gradual rollout)
- ✅ Easy rollback
- ✅ No big bang
- ✅ Continuous delivery

**Disadvantages:**
- ❌ Both systems run simultaneously (higher cost)
- ❌ Longer timeline
- ❌ Data synchronization complexity

---

### 2. Big Bang Migration

**Switch all at once:**

```
Friday 11pm: Final backup
Friday 11:30pm: Migrate data
Saturday 12am: Switch DNS
Saturday 12:30am: Verify
Saturday 1am: Go-live

Downtime: 2 hours
```

**When to use:**
- Small applications
- Can tolerate downtime
- Tight deadline
- Data migration simple

**Example:**
```bash
#!/bin/bash
# Big bang migration script

# 1. Stop old application
kubectl scale deployment/old-app --replicas=0

# 2. Final data export
pg_dump old_db > final_backup.sql

# 3. Import to new database
psql new_db < final_backup.sql

# 4. Deploy new application
kubectl apply -f new-app.yaml

# 5. Update DNS
aws route53 change-resource-record-sets ...

# 6. Verify
curl https://api.example.com/health
```

**Advantages:**
- ✅ Fast (single maintenance window)
- ✅ Simple (no dual operation)
- ✅ Lower cost (no parallel systems)

**Disadvantages:**
- ❌ High risk (all-or-nothing)
- ❌ Downtime required
- ❌ Difficult rollback
- ❌ Stressful

---

### 3. Phased Migration

**Migrate in stages:**

```
Phase 1: Migrate authentication service (1 week)
Phase 2: Migrate user profile service (1 week)
Phase 3: Migrate order service (1 week)
Phase 4: Migrate product catalog (1 week)
Phase 5: Decommission old system

Total: 5 weeks
```

**Advantages:**
- ✅ Lower risk per phase
- ✅ Learn and adjust
- ✅ Easier rollback (per service)

**Disadvantages:**
- ❌ Longer timeline
- ❌ Complex coordination
- ❌ Data consistency challenges

---

### 4. Blue-Green Deployment

**Two identical environments:**

```
Blue (Current):
┌──────────────┐
│ v1.0 (100%)  │◄─── Load Balancer (active)
└──────────────┘

Green (New):
┌──────────────┐
│ v2.0 (0%)    │     (idle, testing)
└──────────────┘

After validation:
Blue (Old):
┌──────────────┐
│ v1.0 (0%)    │     (idle, rollback ready)
└──────────────┘

Green (New):
┌──────────────┐
│ v2.0 (100%)  │◄─── Load Balancer (active)
└──────────────┘
```

**Implementation:**
```yaml
# Blue deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-blue
  labels:
    version: blue
spec:
  replicas: 10

---
# Green deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-green
  labels:
    version: green
spec:
  replicas: 10

---
# Service switches between blue/green
apiVersion: v1
kind: Service
metadata:
  name: app
spec:
  selector:
    version: blue  # Switch to 'green' to cutover
```

**Advantages:**
- ✅ Zero downtime
- ✅ Instant rollback (switch back)
- ✅ Full validation before cutover

**Disadvantages:**
- ❌ 2x infrastructure cost
- ❌ Database migration complexity
- ❌ Data synchronization during transition

---

## Data Migration Strategies

### 1. Dual Writes

**Write to both old and new databases:**

```typescript
async function createUser(data) {
  // Write to old database
  await oldDB.users.create({ data });

  // Also write to new database
  try {
    await newDB.users.create({ data });
  } catch (error) {
    logger.error('New DB write failed', error);
    // Continue (old DB is source of truth)
  }
}

// Read from old database (still source of truth)
async function getUser(id) {
  return await oldDB.users.findUnique({ where: { id } });
}
```

**Phases:**
1. Dual writes (both DBs)
2. Backfill new DB with historical data
3. Verify data consistency
4. Switch reads to new DB
5. Stop writes to old DB

---

### 2. Change Data Capture (CDC)

**Stream changes from old to new:**

```
Old Database → Debezium → Kafka → Consumer → New Database

Real-time replication without application changes
```

```yaml
# Debezium connector
{
  "name": "mysql-connector",
  "config": {
    "connector.class": "io.debezium.connector.mysql.MySqlConnector",
    "database.hostname": "old-mysql",
    "database.port": "3306",
    "database.user": "debezium",
    "database.dbname": "prod",
    "database.server.id": "1",
    "table.include.list": "prod.users,prod.orders",
    "database.history.kafka.bootstrap.servers": "kafka:9092",
    "database.history.kafka.topic": "schema-changes"
  }
}
```

**Advantages:**
- ✅ No application code changes
- ✅ Real-time replication
- ✅ Can replay events

**Disadvantages:**
- ❌ Requires CDC-capable database
- ❌ Additional infrastructure (Kafka)

---

### 3. Database Replication

**Built-in database replication:**

```
MySQL → MySQL (read replica) → Promote to standalone

or

PostgreSQL → PostgreSQL (streaming replication)
```

```sql
-- Create read replica (AWS RDS)
aws rds create-db-instance-read-replica \
  --db-instance-identifier prod-db-replica \
  --source-db-instance-identifier prod-db \
  --db-instance-class db.r5.xlarge

-- Promote replica to standalone
aws rds promote-read-replica \
  --db-instance-identifier prod-db-replica
```

---

## Migration Checklist

### Pre-Migration

**Planning:**
- [ ] Document current architecture
- [ ] Define success criteria
- [ ] Identify dependencies
- [ ] Estimate timeline
- [ ] Calculate costs (old + new during migration)
- [ ] Plan rollback procedures

**Risk Assessment:**
- [ ] Identify high-risk components
- [ ] Plan for data loss scenarios
- [ ] Define acceptable downtime
- [ ] Set up monitoring/alerting
- [ ] Practice in staging

**Communication:**
- [ ] Notify stakeholders
- [ ] Create maintenance window (if needed)
- [ ] Prepare status page updates
- [ ] Assign on-call engineers

---

### During Migration

**Execution:**
- [ ] Execute migration plan step-by-step
- [ ] Monitor metrics continuously
- [ ] Document issues encountered
- [ ] Be ready to rollback

**Validation:**
- [ ] Smoke tests (critical paths)
- [ ] Data integrity checks
- [ ] Performance benchmarks
- [ ] User acceptance testing

---

### Post-Migration

**Verification:**
- [ ] Monitor for 24-48 hours
- [ ] Compare metrics (old vs new)
- [ ] Validate business KPIs
- [ ] Address any issues

**Cleanup:**
- [ ] Decommission old infrastructure (after 2-4 weeks)
- [ ] Update documentation
- [ ] Delete old backups (after retention period)
- [ ] Post-mortem meeting

---

## Zero-Downtime Migration Techniques

### Database Schema Changes

**Expand-Contract Pattern:**

```sql
-- Phase 1: EXPAND (add new column, keep old)
ALTER TABLE users ADD COLUMN full_name VARCHAR(255);

-- Dual-write both columns
UPDATE users SET full_name = CONCAT(first_name, ' ', last_name);

-- Phase 2: Migrate code to use new column
-- (deploy application changes)

-- Phase 3: CONTRACT (remove old columns)
ALTER TABLE users DROP COLUMN first_name;
ALTER TABLE users DROP COLUMN last_name;
```

**Migration steps must be backward-compatible!**

---

### DNS Cutover

```
1. Lower DNS TTL to 60 seconds (24 hours before)
2. Update DNS to point to new system
3. Wait for TTL expiration (1-5 minutes)
4. Monitor both systems for stragglers
5. After 24 hours, decommission old
```

---

## Rollback Strategies

### Feature Flags

```typescript
const useNewAPI = featureFlags.isEnabled('new-api');

if (useNewAPI) {
  return await newAPI.getUsers();
} else {
  return await oldAPI.getUsers();
}

// Instant rollback: Disable feature flag
```

### Database Rollback

```bash
# Backup before migration
pg_dump prod_db > pre_migration_backup.sql

# If migration fails:
psql prod_db < pre_migration_backup.sql

# Rollback application
kubectl rollout undo deployment/api
```

---

## Migration Timeline Template

```markdown
# Migration: Monolith → Microservices

## Week 1-2: Preparation
- Document current architecture
- Set up new infrastructure (Kubernetes)
- Create migration plan
- Practice in staging

## Week 3-4: Phase 1 - Authentication Service
- Extract auth service
- Deploy to production (0% traffic)
- Shadow traffic testing
- Ramp to 10% → 50% → 100%
- Decommission old auth code

## Week 5-6: Phase 2 - User Profile Service
- Extract profile service
- Deploy and test
- Ramp traffic gradually
- Monitor and optimize

## Week 7-8: Phase 3 - Order Service
- (repeat process)

## Week 9: Final Cutover
- Decommission monolith
- Update documentation
- Post-mortem

## Week 10+: Monitoring & Optimization
- Monitor for 2-4 weeks
- Performance tuning
- Cost optimization
```

---

## Common Migration Pitfalls

❌ **No rollback plan** - Always have a way back
❌ **Insufficient testing** - Practice in staging first
❌ **Big bang approach** - Prefer gradual migration
❌ **Ignoring data consistency** - Dual writes, CDC, or replication
❌ **No monitoring** - Can't detect issues quickly
❌ **Poor communication** - Stakeholders surprised
❌ **Rushing** - Give adequate time for each phase

---

## Best Practices

✅ **Migrate gradually** - Strangler fig preferred
✅ **Test thoroughly** - Staging should mirror production
✅ **Monitor everything** - Metrics before/during/after
✅ **Have rollback plan** - Test rollback procedure
✅ **Communicate clearly** - Keep stakeholders informed
✅ **Document everything** - Architecture, decisions, issues
✅ **Validate data** - Check integrity after migration
✅ **Keep old system** - Don't decommission for 2-4 weeks

---

**Related Resources:**
- architecture-patterns.md - Microservices, strangler fig
- disaster-recovery.md - Backup and recovery procedures
- multi-region-design.md - Multi-region migration
