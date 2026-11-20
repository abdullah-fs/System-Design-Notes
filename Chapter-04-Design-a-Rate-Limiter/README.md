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



## Step 2: High-Level Design

### 1. Placement Options
<img width="1274" height="494" alt="image" src="https://github.com/user-attachments/assets/89147718-a9c7-465c-a72e-1f9a42391560" />


Rate limiting can be implemented at different layers of a system. The placement depends on the application architecture, security requirements, and available resources.

#### 1.1 Client-Side Implementation
- Rate limiting logic is implemented on the frontend.
- It can improve user experience by preventing unnecessary requests.
- However, users can modify or bypass client-side restrictions.
- It is not reliable for security or actual request control.

#### 1.2 Server-Side Implementation
- Rate limiting is implemented on the backend server.
- Every incoming request is checked against defined limits.
- Excessive requests are rejected by the server.
- It provides better control, security, and reliability.

#### 1.3 Middleware (API Gateway)
- Rate limiting is implemented in middleware or an API Gateway.
- The gateway checks incoming requests before forwarding them to backend services.
- It provides centralized rate limiting across multiple services.
- It is especially useful in microservices architectures.

### 2. Guidelines for Placement

#### 2.1 Evaluate Current Tech Stack
- Consider the existing application architecture and technology stack.
- Choose an implementation that integrates efficiently with the system.
- For example, Express middleware can work well for a simple application.

#### 2.2 Select Appropriate Algorithms
- Choose a rate-limiting algorithm according to business requirements.
- Consider traffic patterns, request limits, and system behavior.
- Common algorithms include Token Bucket, Leaky Bucket, Fixed Window, and Sliding Window.

#### 2.3 Use API Gateway for Microservices
- An API Gateway is useful when an application uses multiple microservices.
- It provides centralized rate limiting.
- Common rules can be applied before requests reach individual services.

#### 2.4 Consider Commercial Solutions
- Commercial rate-limiting solutions can reduce development and maintenance effort.
- They are useful when development resources are limited.
- Consider cost, scalability, and customization before choosing one.

### 3. Key Takeaways
- Client-side rate limiting is easy to bypass.
- Server-side implementation provides reliable request control.
- API Gateway offers centralized rate limiting for microservices.
- Algorithm selection depends on business requirements.
- Commercial solutions can save development time and resources.



## Step 3: Rate Limiting Algorithms

### 1. Token Bucket Algorithm
<img width="1344" height="1112" alt="image" src="https://github.com/user-attachments/assets/1396be43-ce8c-456d-85ba-ef140ac685b7" />


#### Description
The Token Bucket algorithm controls incoming requests using a virtual bucket containing tokens.

- Tokens are added to the bucket at a fixed rate.
- Each incoming request consumes one token.
- If a token is available, the request is allowed.
- If no token is available, the request is rejected or delayed.
- The bucket has a maximum capacity and cannot store extra tokens.

#### Working Example
Suppose the bucket capacity is 10 tokens and the refill rate is 2 tokens per second.

- A user can send up to 10 requests immediately if the bucket is full.
- Each request consumes one token.
- Once all tokens are consumed, the user must wait for new tokens.

#### Important Parameters

**1. Bucket Size**
- Defines the maximum number of tokens the bucket can hold.
- Determines how many burst requests can be allowed.

**2. Refill Rate**
- Defines how many tokens are added per second or minute.
- Controls the average request rate over time.

#### Advantages (Pros)
- Easy to implement.
- Memory-efficient because it stores minimal information.
- Supports temporary traffic bursts.
- Provides flexible request handling.

#### Disadvantages (Cons)
- Bucket size and refill rate require careful configuration.
- Incorrect settings may allow excessive traffic or restrict legitimate users.

#### Key Takeaway
The Token Bucket algorithm allows temporary traffic bursts while maintaining an average request rate through a fixed token refill rate.



### 2. Leaky Bucket Algorithm
<img width="1458" height="458" alt="image" src="https://github.com/user-attachments/assets/957e9f08-8290-41b7-87b9-dacc3c32f35d" />


#### Description
The Leaky Bucket algorithm controls incoming traffic by processing requests at a fixed rate using a FIFO (First In, First Out) queue.

- Incoming requests are stored in a queue.
- Requests are processed in the order they arrive.
- The system processes requests at a constant rate.
- If the queue becomes full, new incoming requests are rejected.

#### Working Example
Suppose the queue capacity is 10 requests and the system processes 2 requests per second.

- If 10 requests arrive simultaneously, they are stored in the queue.
- The system processes 2 requests every second.
- Additional requests are rejected if the queue is full.

#### Advantages (Pros)
- Memory-efficient because the queue has a fixed capacity.
- Maintains a stable and predictable processing rate.
- Prevents sudden traffic bursts from overwhelming the server.

#### Disadvantages (Cons)
- Traffic bursts can cause requests to wait in the queue.
- New requests may be rejected when the queue is full.
- Recent requests must wait for earlier requests to be processed.

#### Real-World Example
Uber's [uber-go/ratelimit](https://github.com/uber-go/ratelimit) is a Go rate-limiting library that provides leaky-bucket-style rate limiting.

#### Key Takeaway
The Leaky Bucket algorithm smooths incoming traffic by processing requests at a fixed rate, regardless of how quickly they arrive.



### 3. Fixed Window Counter Algorithm
<img width="1436" height="780" alt="image" src="https://github.com/user-attachments/assets/c753655d-59bf-41a3-9260-12ffc087366e" />

#### Description
The Fixed Window Counter algorithm divides time into fixed intervals and maintains a separate request counter for each interval.

- A fixed time window is defined, such as one minute.
- Each incoming request increments the counter.
- Requests are allowed while the counter is within the defined limit.
- Once the limit is reached, additional requests are rejected.
- The counter resets when a new time window begins.

#### Working Example
Suppose the rate limit is 100 requests per minute.

- From 10:00 to 10:01, a maximum of 100 requests are allowed.
- At 10:01, the counter resets.
- The next window allows another 100 requests.

#### Advantages (Pros)
- Simple and easy to implement.
- Memory-efficient because it requires only a counter and window information.
- Efficient request counting and limit checking.

#### Disadvantages (Cons)

**1. Boundary Burst Problem**
- Traffic spikes can occur at the edges of consecutive time windows.
- A user can send requests near the end of one window and immediately send more requests at the beginning of the next window.

**Example:**
- 100 requests are sent at 10:00:59.
- Another 100 requests are sent at 10:01:00.
- This allows 200 requests within a very short period, even though the limit is 100 requests per minute.

#### Key Takeaway
The Fixed Window Counter algorithm is simple and memory-efficient, but it can allow sudden traffic bursts at window boundaries.
<img width="1372" height="622" alt="image" src="https://github.com/user-attachments/assets/38c25e61-4cf5-4cdb-b385-18b6b5e3a797" />



### 4. Sliding Window Log Algorithm
<img width="1276" height="898" alt="image" src="https://github.com/user-attachments/assets/11732e9e-272b-42cb-a6c7-d9e4035936c3" />


#### Description
The Sliding Window Log algorithm tracks the exact timestamp of each request to enforce rate limits over a rolling time window.

- Each incoming request's timestamp is stored.
- The system checks requests within the current time window.
- Expired timestamps are removed.
- Requests are allowed if the count is below the defined limit.
- Otherwise, the request is rejected.

#### Working Example
Suppose the rate limit is 5 requests per minute.

- Each request timestamp is recorded.
- When a new request arrives, the system checks the previous 60 seconds.
- If 5 requests already exist within that period, the new request is rejected.
- Once an old request moves outside the window, a new request can be allowed.

#### Advantages (Pros)
- Provides accurate rate limiting.
- Prevents the boundary burst problem of Fixed Window Counter.
- Uses a rolling time window based on exact request timestamps.

#### Disadvantages (Cons)
- High memory consumption because every request timestamp must be stored.
- Requires additional processing to check timestamps and remove expired entries.
- Can become expensive in high-traffic systems.

#### MERN Stack Example
Redis Sorted Sets can store request timestamps.

- Store each request timestamp.
- Remove timestamps outside the current window.
- Count remaining requests.
- Allow or reject the incoming request based on the limit.

#### Key Takeaway
Sliding Window Log provides accurate rate limiting by tracking individual request timestamps, but it requires more memory and processing.



### 5. Sliding Window Counter Algorithm
<img width="1436" height="730" alt="image" src="https://github.com/user-attachments/assets/b8d31c75-debd-47f5-84e2-fd1655815772" />

#### Description
The Sliding Window Counter algorithm combines Fixed Window Counter and Sliding Window concepts to estimate the number of requests in a rolling time window.

- It maintains request counters for the previous and current windows.
- It uses a weighted portion of the previous window's requests.
- It combines this value with the current window's request count.
- The estimated count is compared against the rate limit.

#### Working Example
Suppose the rate limit is 100 requests per minute.

- Previous window count: 80 requests.
- Current window count: 30 requests.
- 50% of the previous window overlaps with the current rolling window.

Estimated Count:

`(80 × 50%) + 30 = 70 requests`

Since the estimated count is below 100, the new request can be allowed.

#### Important Formula

`Estimated Count = (Previous Count × Overlap Percentage) + Current Count`

- **Previous Count:** Total requests in the previous window.
- **Overlap Percentage:** The portion of the previous window overlapping with the current rolling window.
- **Current Count:** Total requests in the current window.

#### Advantages (Pros)
- Memory-efficient because it stores counters instead of individual timestamps.
- Reduces the boundary burst problem of Fixed Window Counter.
- Provides smoother rate limiting.
- Offers a good balance between performance and accuracy.

#### Disadvantages (Cons)
- Calculates an estimated request count rather than an exact count.
- May not enforce perfectly strict limits in every situation.

#### MERN Stack Example
Redis can maintain previous and current window counters. Express.js middleware can use these counters to calculate the estimated request count and decide whether to allow or reject a request.

#### Key Takeaway
Sliding Window Counter combines the efficiency of Fixed Window Counter with the smoother behavior of a rolling window, while using less memory than Sliding Window Log.
