# Redis

## Overview

Redis (Remote Dictionary Server) is an open-source, in-memory data structure store used as a database, cache, message broker, and streaming engine. It's known for its exceptional performance and versatility, making it essential for modern high-performance applications.

**Type**: In-Memory Data Store / Cache  
**License**: BSD (Open Source)  
**Primary Language**: C  
**Founded**: 2009  
**Website**: [redis.io](https://redis.io)

---

## What is Redis?

Redis is an in-memory data structure store that can be used as a database, cache, message broker, or streaming engine. It supports various data structures like strings, hashes, lists, sets, sorted sets, and more.

### Key Characteristics

- **In-Memory**: Extremely fast (sub-millisecond latency)
- **Data Structures**: Rich data types (strings, hashes, lists, sets, etc.)
- **Persistence**: Optional disk persistence
- **Pub/Sub**: Publish-subscribe messaging
- **Atomic Operations**: All operations are atomic
- **Replication**: Master-replica replication
- **Clustering**: Built-in clustering support

---

## Optimal Use Cases

### ✅ Best For

1. **Caching**
   - Database query caching
   - Session storage
   - API response caching
   - Page caching

2. **Session Management**
   - User sessions
   - Shopping carts
   - Temporary data
   - Authentication tokens

3. **Real-Time Features**
   - Leaderboards
   - Real-time analytics
   - Rate limiting
   - Counting

4. **Message Queuing**
   - Pub/Sub messaging
   - Task queues
   - Event streaming
   - Real-time notifications

5. **Rate Limiting**
   - API rate limiting
   - DDoS protection
   - Request throttling

6. **Leaderboards & Rankings**
   - Gaming leaderboards
   - Popular content
   - Trending items
   - Sorted sets

### ❌ Not Ideal For

1. **Primary Database**
   - Not designed as primary DB
   - Use for caching/sessions

2. **Large Data Sets**
   - Memory-limited
   - Expensive at scale

3. **Complex Queries**
   - No SQL-like queries
   - Simple key-value operations

4. **Permanent Storage**
   - Designed for temporary data
   - Use traditional DB for persistence

---

## Architecture & Core Features

### Data Structures

#### 1. Strings
```redis
SET user:1:name "John Doe"
GET user:1:name
INCR user:1:views
```

#### 2. Hashes
```redis
HSET user:1 name "John" email "john@example.com"
HGET user:1 name
HGETALL user:1
```

#### 3. Lists
```redis
LPUSH tasks "task1"
RPUSH tasks "task2"
LRANGE tasks 0 -1
```

#### 4. Sets
```redis
SADD tags "redis" "database" "cache"
SMEMBERS tags
SINTER set1 set2
```

#### 5. Sorted Sets
```redis
ZADD leaderboard 100 "player1"
ZADD leaderboard 200 "player2"
ZREVRANGE leaderboard 0 9
```

#### 6. Streams
```redis
XADD events * user_id 123 action "login"
XREAD STREAMS events 0
```

### Key Features

#### 1. Caching
```redis
# Cache with expiration
SET key "value" EX 3600  # Expires in 1 hour
TTL key  # Check remaining time
```

#### 2. Pub/Sub
```redis
# Publisher
PUBLISH channel "message"

# Subscriber
SUBSCRIBE channel
```

#### 3. Transactions
```redis
MULTI
SET key1 "value1"
SET key2 "value2"
EXEC
```

#### 4. Lua Scripting
```lua
-- Atomic operations
EVAL "return redis.call('incr', KEYS[1])" 1 counter
```

---

## Comparison with Alternatives

### Redis vs Memcached

| Feature | Redis | Memcached |
|---------|-------|-----------|
| **Data Types** | Rich (strings, hashes, etc.) | Strings only |
| **Persistence** | Optional | No |
| **Pub/Sub** | Yes | No |
| **Complexity** | More features | Simpler |
| **Use Case** | Caching + more | Pure caching |

**Choose Redis when**: You need more than simple caching.  
**Choose Memcached when**: You only need simple key-value caching.

### Redis vs Database Caching

| Feature | Redis | Database |
|---------|-------|----------|
| **Speed** | Sub-millisecond | Milliseconds |
| **Persistence** | Optional | Always |
| **Complexity** | Simple | Complex |
| **Use Case** | Cache layer | Primary storage |

**Choose Redis when**: You need fast caching layer.  
**Choose Database when**: You need persistent storage.

### Redis vs RabbitMQ

| Feature | Redis | RabbitMQ |
|---------|-------|----------|
| **Primary Use** | Cache + messaging | Message broker |
| **Message Guarantees** | Basic | Advanced |
| **Performance** | Very fast | Fast |
| **Complexity** | Simple | More complex |

**Choose Redis when**: You need simple pub/sub or already use Redis.  
**Choose RabbitMQ when**: You need advanced message guarantees.

---

## Integration with SaaS Products

### Session Storage

```javascript
const redis = require('redis');
const client = redis.createClient();

// Store session
await client.setEx(`session:${sessionId}`, 3600, JSON.stringify({
  userId: 123,
  email: 'user@example.com'
}));

// Get session
const session = await client.get(`session:${sessionId}`);
```

### API Response Caching

```javascript
async function getCachedData(key) {
  // Try cache first
  const cached = await client.get(key);
  if (cached) return JSON.parse(cached);

  // Fetch from database
  const data = await fetchFromDatabase();
  
  // Cache for 1 hour
  await client.setEx(key, 3600, JSON.stringify(data));
  return data;
}
```

### Rate Limiting

```javascript
async function rateLimit(userId, limit = 100, window = 3600) {
  const key = `rate_limit:${userId}`;
  const current = await client.incr(key);
  
  if (current === 1) {
    await client.expire(key, window);
  }
  
  return current <= limit;
}
```

### Real-Time Leaderboard

```javascript
// Add score
await client.zAdd('leaderboard', {
  score: 1000,
  value: 'player123'
});

// Get top 10
const topPlayers = await client.zRevRange('leaderboard', 0, 9, {
  WITHSCORES: true
});
```

### Pub/Sub for Real-Time Updates

```javascript
// Publisher
const publisher = redis.createClient();
await publisher.publish('notifications', JSON.stringify({
  userId: 123,
  message: 'New notification'
}));

// Subscriber
const subscriber = redis.createClient();
subscriber.subscribe('notifications');
subscriber.on('message', (channel, message) => {
  const notification = JSON.parse(message);
  // Send to user via WebSocket
});
```

---

## Getting Started

### Installation

**macOS:**
```bash
brew install redis
brew services start redis
```

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install redis-server
sudo systemctl start redis
```

**Docker:**
```bash
docker run -d -p 6379:6379 redis:latest
```

### Basic Usage

**CLI:**
```bash
redis-cli

# Set value
SET mykey "Hello Redis"

# Get value
GET mykey

# Set with expiration
SET mykey "value" EX 60

# Increment
INCR counter
```

**Node.js:**
```javascript
const redis = require('redis');
const client = redis.createClient();

await client.connect();

await client.set('key', 'value');
const value = await client.get('key');

await client.disconnect();
```

**Python:**
```python
import redis

r = redis.Redis(host='localhost', port=6379, db=0)

r.set('key', 'value')
value = r.get('key')
```

### Connection Pooling

```javascript
const { createClient } = require('redis');

const client = createClient({
  socket: {
    host: 'localhost',
    port: 6379
  },
  // Connection pool settings
  maxRetriesPerRequest: 3
});
```

---

## Performance Considerations

### Best Practices

1. **Connection Pooling**: Reuse connections
2. **Pipelining**: Batch operations
3. **Key Naming**: Use consistent naming
4. **Expiration**: Set TTL on temporary data
5. **Memory Management**: Monitor memory usage
6. **Persistence**: Configure based on needs

### Pipelining

```javascript
// Batch operations
const pipeline = client.multi();
pipeline.set('key1', 'value1');
pipeline.set('key2', 'value2');
pipeline.set('key3', 'value3');
await pipeline.exec();
```

### Memory Optimization

```redis
# Use appropriate data structures
# Hashes for objects (memory efficient)
HSET user:1 name "John" email "john@example.com"

# Use expiration
SET key "value" EX 3600

# Monitor memory
INFO memory
```

---

## Security Features

1. **Authentication**: Password protection
2. **ACL**: Access Control Lists
3. **TLS/SSL**: Encrypted connections
4. **Network Security**: Bind to specific interfaces
5. **Command Renaming**: Disable dangerous commands

### Security Configuration

```redis
# redis.conf
requirepass your_secure_password
bind 127.0.0.1
protected-mode yes
rename-command FLUSHDB ""
rename-command FLUSHALL ""
```

---

## Persistence Options

### RDB (Snapshot)
```redis
# Save snapshot
SAVE

# Background save
BGSAVE

# Configure in redis.conf
save 900 1
save 300 10
save 60 10000
```

### AOF (Append-Only File)
```redis
# Enable AOF
appendonly yes
appendfsync everysec
```

---

## When to Choose Redis

### ✅ Choose Redis If:

- Need fast caching layer
- Building real-time features
- Need session storage
- Implementing rate limiting
- Building leaderboards
- Need pub/sub messaging
- Want simple data structures
- Need sub-millisecond latency

### ❌ Consider Alternatives If:

- Need primary database (use PostgreSQL/MongoDB)
- Need complex queries (use SQL database)
- Very large datasets (memory expensive)
- Simple caching only (Memcached might suffice)

---

## Resources

- **Documentation**: [redis.io/docs](https://redis.io/docs)
- **Commands**: [redis.io/commands](https://redis.io/commands)
- **Tutorial**: [redis.io/topics/introduction](https://redis.io/topics/introduction)
- **Client Libraries**: [redis.io/docs/clients](https://redis.io/docs/clients)

---

## Summary

Redis is an essential tool for modern applications requiring high-performance caching, session management, and real-time features. Its versatility and speed make it a cornerstone of scalable SaaS architectures.

**Best For**: Caching, sessions, real-time features, rate limiting, pub/sub, leaderboards  
**Not Ideal For**: Primary database, complex queries, very large persistent datasets

