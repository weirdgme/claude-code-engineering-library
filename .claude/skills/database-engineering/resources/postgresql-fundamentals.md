# PostgreSQL Fundamentals

Basic PostgreSQL administration, configuration, and common operations.

## Installation

```bash
# Docker
docker run --name postgres \
  -e POSTGRES_PASSWORD=mysecretpassword \
  -p 5432:5432 \
  -d postgres:15

# Connect
psql -h localhost -U postgres
```

## Common Operations

```sql
-- Create database
CREATE DATABASE myapp;

-- Create user
CREATE USER myapp_user WITH PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE myapp TO myapp_user;

-- Create table
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Create index
CREATE INDEX idx_users_email ON users(email);

-- Vacuum (cleanup)
VACUUM ANALYZE users;
```

## Configuration

```ini
# postgresql.conf
max_connections = 100
shared_buffers = 256MB
effective_cache_size = 1GB
maintenance_work_mem = 64MB
work_mem = 4MB
```

## Extensions

```sql
-- Enable UUID
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Full-text search
CREATE EXTENSION IF NOT EXISTS pg_trgm;

-- PostGIS (geospatial)
CREATE EXTENSION IF NOT EXISTS postgis;
```

---

**Related Resources:**
- query-optimization.md - Performance tuning
- backup-and-recovery.md - Data protection
