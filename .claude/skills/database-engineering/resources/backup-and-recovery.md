# Backup and Recovery

Guide to PostgreSQL backup strategies and disaster recovery procedures.

## pg_dump (Logical Backup)

```bash
# Full database backup
pg_dump -h localhost -U postgres myapp > backup.sql

# Compressed backup
pg_dump -h localhost -U postgres myapp | gzip > backup.sql.gz

# Restore
psql -h localhost -U postgres myapp < backup.sql

# Backup specific tables
pg_dump -h localhost -U postgres -t users -t orders myapp > tables_backup.sql
```

## Automated Backups

```bash
#!/bin/bash
# daily-backup.sh

DATE=$(date +%Y-%m-%d)
BACKUP_DIR="/backups"
DATABASE="myapp"

# Create backup
pg_dump -h localhost -U postgres $DATABASE | gzip > "$BACKUP_DIR/backup-$DATE.sql.gz"

# Delete backups older than 30 days
find $BACKUP_DIR -name "backup-*.sql.gz" -mtime +30 -delete

# Upload to S3
aws s3 cp "$BACKUP_DIR/backup-$DATE.sql.gz" s3://my-backups/
```

## Point-in-Time Recovery

```ini
# postgresql.conf
wal_level = replica
archive_mode = on
archive_command = 'cp %p /archive/%f'
```

```bash
# Restore to specific point in time
pg_basebackup -D /var/lib/postgresql/data

# recovery.conf
restore_command = 'cp /archive/%f %p'
recovery_target_time = '2024-01-15 14:30:00'
```

## Best Practices

✅ Automated daily backups
✅ Test restore procedure regularly
✅ Store backups off-site (S3, etc.)
✅ Retain backups for 30+ days
✅ Monitor backup success
✅ Document recovery procedures

---

**Related Resources:**
- postgresql-fundamentals.md - PostgreSQL basics
- database-replication.md - Replication for HA
