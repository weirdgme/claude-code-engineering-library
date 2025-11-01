# Rate Limiting

Guide to implementing rate limiting and throttling for APIs.

## Algorithms

### Token Bucket
```typescript
class TokenBucket {
  constructor(
    private capacity: number,  // Max tokens
    private refillRate: number  // Tokens per second
  ) {}

  async consume(tokens: number = 1): Promise<boolean> {
    const key = `rate-limit:${userId}`;
    const current = await redis.get(key) || this.capacity;

    if (current >= tokens) {
      await redis.decrby(key, tokens);
      return true;  // Allowed
    }
    return false;  // Rate limited
  }
}

// Usage: 100 requests per minute
const limiter = new TokenBucket(100, 100/60);
```

### Sliding Window
```typescript
async function checkRateLimit(userId: string, limit: number, windowMs: number) {
  const key = `rate-limit:${userId}`;
  const now = Date.now();
  const windowStart = now - windowMs;

  // Remove old entries
  await redis.zremrangebyscore(key, 0, windowStart);

  // Count requests in window
  const count = await redis.zcard(key);

  if (count < limit) {
    await redis.zadd(key, now, `${now}-${Math.random()}`);
    await redis.expire(key, Math.ceil(windowMs / 1000));
    return true;  // Allowed
  }

  return false;  // Rate limited
}

// Usage: 1000 requests per hour
await checkRateLimit(userId, 1000, 3600000);
```

## Express Middleware

```typescript
import rateLimit from 'express-rate-limit';
import RedisStore from 'rate-limit-redis';
import Redis from 'ioredis';

const redis = new Redis(process.env.REDIS_URL);

const limiter = rateLimit({
  store: new RedisStore({ client: redis }),
  windowMs: 15 * 60 * 1000,  // 15 minutes
  max: 100,  // 100 requests per window
  message: 'Too many requests, please try again later',
  standardHeaders: true,
  legacyHeaders: false,
});

app.use('/api/', limiter);
```

## Response Headers

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 85
X-RateLimit-Reset: 1642348800

HTTP 429 Too Many Requests
Retry-After: 60
```

## Per-User vs Per-IP

```typescript
// Per-user (authenticated)
const userLimiter = rateLimit({
  keyGenerator: (req) => req.user?.id || req.ip,
  max: 1000,  // Higher limit for authenticated users
});

// Per-IP (unauthenticated)
const ipLimiter = rateLimit({
  keyGenerator: (req) => req.ip,
  max: 100,  // Lower limit for anonymous users
});
```

## Best Practices

✅ Use distributed rate limiting (Redis)
✅ Return clear error messages
✅ Include rate limit headers
✅ Different limits for authenticated vs anonymous
✅ Higher limits for premium users
✅ Monitor rate limit hits

---

**Related Resources:**
- rest-api-design.md - API design
- api-versioning.md - API versions
