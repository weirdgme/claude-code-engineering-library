# Database Replication

Guide to setting up PostgreSQL replication for high availability and read scaling.

## Streaming Replication

```bash
# Primary server
# postgresql.conf
wal_level = replica
max_wal_senders = 3

# Replica server
# Create replication slot on primary
SELECT * FROM pg_create_physical_replication_slot('replica_1');

# Start replica
pg_basebackup -h primary -D /var/lib/postgresql/data -U replicator -v -P

# standby.signal file indicates replica mode
touch /var/lib/postgresql/data/standby.signal
```

## Read Replicas

```typescript
// Application code
import { PrismaClient } from '@prisma/client';

const primary = new PrismaClient({
  datasources: { db: { url: process.env.DATABASE_PRIMARY_URL } }
});

const replica = new PrismaClient({
  datasources: { db: { url: process.env.DATABASE_REPLICA_URL } }
});

// Writes go to primary
await primary.users.create({ data: { email: 'user@example.com' } });

// Reads can use replica
const users = await replica.users.findMany();
```

## Monitoring Replication

```sql
-- Check replication lag
SELECT
  client_addr,
  state,
  sent_lsn,
  write_lsn,
  replay_lsn,
  sync_state
FROM pg_stat_replication;
```

---

**Related Resources:**
- postgresql-fundamentals.md - PostgreSQL basics
- backup-and-recovery.md - Backup strategies
