# Chapter 4: Design a Rate Limiter

## 1. Introduction

A **Rate Limiter** is a mechanism that controls how many requests a client or user can send to a server within a specific time period.

If a user exceeds the allowed request limit, the server blocks additional requests.

### Example

Suppose a website has the following rate limit:

- Maximum Requests: 100
- Time Window: 1 Minute

| Request | Result |
|---|---|
| 1–100 | Allowed |
| 101 | Blocked |

The server returns `429 Too Many Requests` when the user exceeds the limit.

## 2. Benefits of Rate Limiting

### 2.1 Preventing DoS Attacks
Blocks excessive requests to prevent server resources from being exhausted.

### 2.2 Cost Reduction
Reduces unnecessary requests and helps lower server infrastructure costs.

### 2.3 Preventing Overloads
Controls incoming traffic to maintain server stability and performance.

## 3. Important HTTP Status Code

**429 – Too Many Requests**

This status code is returned when a client exceeds the allowed request limit.

## 4. Key Takeaways

- Controls incoming requests.
- Limits requests within a specific time period.
- Helps prevent DoS attacks.
- Reduces unnecessary server costs.
- Protects servers from overload.
- HTTP 429 means Too Many Requests.
