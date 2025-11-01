# Query Optimization

Guide to optimizing database queries with EXPLAIN, indexing, and query tuning.

## EXPLAIN ANALYZE

```sql
-- See query plan
EXPLAIN ANALYZE
SELECT * FROM users
WHERE email = 'user@example.com';

-- Output shows:
-- Seq Scan on users (cost=0.00..1.25 rows=1) (actual time=0.025..0.026 rows=1)
--   Filter: (email = 'user@example.com')

-- After adding index:
-- Index Scan using idx_users_email (cost=0.15..8.17 rows=1) (actual time=0.010..0.011 rows=1)
```

## Index Strategies

```sql
-- Single column index
CREATE INDEX idx_users_email ON users(email);

-- Composite index
CREATE INDEX idx_orders_user_date ON orders(user_id, created_at);

-- Partial index
CREATE INDEX idx_active_users ON users(email)
WHERE is_active = true;

-- GIN index for JSON
CREATE INDEX idx_metadata ON users USING GIN (metadata);
```

## Query Tuning

```sql
-- ❌ Bad: SELECT *
SELECT * FROM orders WHERE user_id = 123;

-- ✅ Good: Select only needed columns
SELECT id, total, created_at FROM orders WHERE user_id = 123;

-- ❌ Bad: N+1 queries
SELECT * FROM users;
-- Then for each user: SELECT * FROM orders WHERE user_id = ?

-- ✅ Good: JOIN
SELECT u.*, o.* FROM users u
LEFT JOIN orders o ON o.user_id = u.id;
```

## Best Practices

✅ Use EXPLAIN ANALYZE for slow queries
✅ Index foreign keys
✅ Avoid SELECT *
✅ Use LIMIT for large result sets
✅ Consider query caching
✅ Monitor slow query logs

---

**Related Resources:**
- postgresql-fundamentals.md - Database basics
