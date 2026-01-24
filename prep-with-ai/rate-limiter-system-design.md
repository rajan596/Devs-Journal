# Multi-Region Rate Limiter (Senior Backend – Microsoft)

This document is a **detailed, interview-ready reference** for designing a **production-grade, multi-region rate limiter**. It includes **clear sequencing, formulas, examples, and sample calculations** suitable for Senior Software Engineer interviews at Microsoft.

---

## 1. Problem Statement

Design a **rate limiter** that:
- Supports **per-user / per-API / per-API-key limits**
- Works in a **distributed system**
- Scales to **100K+ RPS**
- Operates across **multiple regions**
- Is **highly available** and low latency
- Returns **HTTP 429 + Retry-After** when limits are exceeded

---

## 2. Where the Rate Limiter Lives

- Implemented at the **API Gateway** layer
- Prevents unnecessary load on backend services

### Request Flow
1. Client → API Gateway
2. Identity resolved (API key → User ID → IP fallback)
3. Rate limiter check
4. If allowed → forward to backend
5. If blocked → return `429 Too Many Requests`

---

## 3. Rate Limiting Algorithms (Overview)

### 3.1 Fixed Window Counter

**How it works**:
- Requests are grouped into fixed time buckets (e.g., per minute)
- Each bucket has a max allowed count

**Pros**:
- Very simple
- Exact `Retry-After`

**Cons**:
- Burst problem at window boundaries

---

### 3.2 Token Bucket (Preferred for High Traffic)

**Why Token Bucket**:
- Allows short bursts
- Smooths sustained traffic
- Better UX for clients

**Senior decision**:
- Fixed Window → low-risk APIs
- Token Bucket → burst-heavy or premium APIs

---

## 4. Fixed Window – Detailed Example

### Configuration
- Limit: **10 requests per minute**
- Window size: **60 seconds**

### Window Timestamp Calculation

```
windowStart = (currentEpochSeconds / windowSize) * windowSize
```

#### Example
```
currentTime = 1706000123
windowStart = (1706000123 / 60) * 60
            = 1706000100
```

This window covers:
```
1706000100 → 1706000159
```

---

### Redis Key Model (Fixed Window)

```
Key: rate:{api}:{identity}:{windowStart}
Value: requestCount
TTL: 60 seconds
```

---

### Retry-After Calculation (Fixed Window)

```
retryAfter = (windowStart + windowSize) - currentTime
```

#### Example
```
currentTime = 1706000135
retryAfter = (1706000100 + 60) - 1706000135
           = 25 seconds
```

---

## 5. Token Bucket – Core Concepts

Each **API + identity** has a bucket with:
- **Capacity**: maximum tokens
- **Refill rate**: tokens added per second

Each request consumes **1 token**.

---

## 6. Token Bucket – Step-by-Step Example

### Configuration
- Capacity = **10 tokens**
- Refill rate = **2 tokens/second**

### Timeline

| Time | Action | Tokens |
|----|----|----|
| t=0 | Bucket initialized | 10 |
| t=1 | 5 requests | 5 |
| t=3 | Refill (2 sec × 2) | 9 |
| t=4 | 9 requests | 0 |
| t=4 | Next request | ❌ 429 |

---

## 7. Token Refill Formula

When a request arrives at time `now`:

```
elapsed = now - lastRefillTime
newTokens = min(capacity, tokens + elapsed * refillRate)
```

If `newTokens >= 1` → allow and decrement.
Else → reject.

---

## 8. Retry-After (Token Bucket)

Unlike fixed window, Retry-After is **approximate**.

```
retryAfter = ceil((1 - currentTokens) / refillRate)
```

### Example
- currentTokens = 0
- refillRate = 2/sec

```
retryAfter = ceil(1 / 2) = 1 second
```

---

## 9. Redis Data Model (Token Bucket)

```
Key: bucket:{api}:{identity}
Type: HASH
Fields:
  - tokens
  - last_refill_timestamp
TTL: ceil(capacity / refillRate)
```

TTL ensures cleanup of inactive buckets.

---

## 10. Atomicity with Redis Lua Script

### Why Lua?
- Prevents race conditions
- Guarantees atomic **check + refill + decrement**

### High-Level Lua Logic
1. Read tokens & last refill time
2. Recalculate available tokens
3. If tokens < 1 → reject
4. Else decrement & allow
5. Update TTL

Redis executes Lua scripts atomically.

---

## 11. Scaling to Multi-Region

### The Challenge
- Users hit nearest region
- Each region has its own Redis
- Global rate limits must still be respected

---

## 12. Recommended Design: Regional + Global Hybrid

### Core Idea
- Enforce limits **regionally** for low latency
- Maintain a **global quota** asynchronously

### Example Allocation

Global limit: **100 req/sec**

| Region | Quota |
|----|----|
| US | 40 |
| EU | 30 |
| APAC | 30 |

Each region enforces locally using Token Bucket.

---

## 13. Multi-Region Request Flow

```
Client → Nearest Region → API Gateway
       → Regional Rate Limiter (Redis)
       → Backend Service
```

Usage metrics are emitted asynchronously.

---

## 14. Global Quota Synchronization

### Preferred: Asynchronous Sync
- Regions publish usage every few seconds
- Central quota service rebalances limits

**Pros**:
- Low latency
- High availability

**Cons**:
- Temporary over-limit possible (acceptable)

---

## 15. Dynamic Quota Rebalancing

- Underutilized regions lend unused quota
- Adjusted based on traffic patterns

Improves fairness and utilization.

---

## 16. Failure Handling

### Regional Redis Failure
- Public APIs → fail-open
- Auth / security APIs → fail-closed

### Global Quota Service Failure
- Regions continue with last known quota
- Sync resumes after recovery

---

## 17. Senior-Level Summary Statement

> "I enforce rate limits regionally for low latency while managing a global quota asynchronously to balance scalability, fairness, and availability."

---

## 18. Interview Signals Demonstrated

- Distributed system design
- Latency awareness
- Strong trade-off analysis
- Failure resilience
- Senior engineering judgment

---

**End of Reference Notes**

