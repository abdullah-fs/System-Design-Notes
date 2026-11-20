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



# Chapter 4: Design a Rate Limiter

## 1. Understanding the Problem

A Rate Limiter controls how many requests a client or user can send to a server within a specific time period.

The goal is to protect APIs from excessive traffic, maintain performance, and ensure fair usage.

## 2. Key Features

### 2.1 Server-Side API Rate Limiter
- Rate limiting should be implemented on the server side.
- It controls incoming API requests before they reach the main application logic.
- This prevents clients from bypassing request limits.

### 2.2 Support Multiple Throttle Rules
- The system should support different rate limits for different users and API endpoints.
- For example:
  - Normal users: 100 requests per minute.
  - Premium users: 500 requests per minute.
  - Login API: 5 requests per minute.

### 2.3 Distributed Environment
- The rate limiter should work across multiple servers.
- All servers must share request-count information.
- Redis can be used as shared storage to maintain consistent request counts.

### 2.4 Standalone Service or Application-Level Code
A rate limiter can be implemented in two ways:

- **Standalone Service:** A separate service handles rate-limiting decisions.
- **Application-Level Code:** Rate limiting is implemented directly inside the application, such as Express middleware.

### 2.5 Inform Users When Throttled
- The system should notify users when they exceed their request limit.
- It should return HTTP status code `429 Too Many Requests`.
- A `Retry-After` header can tell users when they can try again.

## 3. Non-Functional Requirements

### 3.1 Accurate Throttling
- The system must correctly count incoming requests.
- It should block requests when the defined limit is exceeded.
- For example, if the limit is 100 requests per minute, the 101st request should be blocked.

### 3.2 Minimal Latency
- Rate limiting should add very little delay to API requests.
- Fast storage solutions like Redis help reduce processing time.

### 3.3 Low Memory Usage
- The system should use memory efficiently.
- It should store only necessary information, such as request counts and timestamps.
- Expired counters can be automatically removed using TTL.

### 3.4 Distributed Capability
- The system should maintain consistent request counts across multiple application servers.
- Shared storage, such as Redis, helps all servers access the same rate-limit data.

### 3.5 Clear Exception Handling
- The system should handle errors properly.
- It should return clear responses when users exceed limits or when internal services fail.

### 3.6 High Fault Tolerance
- The rate limiter should remain reliable even if some components fail.
- It should define what happens when the rate-limiting service or Redis becomes unavailable.
- **Fail-Open:** Allow requests if the rate limiter fails.
- **Fail-Closed:** Block requests if the rate limiter fails.

## 4. Key Takeaways

- Rate limiting controls incoming API traffic.
- It should be implemented on the server side.
- Different users and APIs can have different limits.
- Redis can manage shared counters in distributed systems.
- HTTP `429` indicates that the request limit has been exceeded.
- A good rate limiter should be accurate, fast, memory-efficient, and fault-tolerant.
