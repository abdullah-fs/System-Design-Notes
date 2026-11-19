# Chapter 2: Back-of-the-Envelope Estimation

## 1. Introduction
Back-of-the-Envelope Estimation is a technique used in system design to make quick calculations and estimate a system's capacity, performance, and resource requirements before implementation.

## 2. Why Do We Need It?
- **Scalability:** Estimate how much the system can grow.
- **Performance:** Understand system load and request handling.
- **Storage:** Estimate required database storage.
- **Cost:** Estimate infrastructure and server costs.

## 3. Traffic Estimation
Traffic estimation helps determine how many requests a system receives.

**Example:**
- Daily Active Users (DAU): 100,000
- Requests per user: 10/day
- Total Daily Requests: 1,000,000

## 4. Requests Per Second (QPS)

QPS (Queries Per Second) measures how many requests a system handles per second.

**Formula:**

QPS = Total Daily Requests / 86,400

**Example:**

1,000,000 / 86,400 ≈ 11.57 QPS

## 5. Types of Estimation

- **Traffic Estimation:** Number of requests received by the system.
- **Storage Estimation:** Amount of data required to store information.
- **Bandwidth Estimation:** Amount of data transferred over the network.

## Key Takeaway
Back-of-the-Envelope Estimation helps system designers make informed decisions about scalability, performance, storage, and infrastructure before building a system.



## Section 1: Key Concepts

### 1. Power of Two

Power of Two refers to values obtained by raising 2 to different powers. It is important in System Design for understanding data sizes and performing storage, memory, and bandwidth calculations.
![Uploading image.png…]()


#### Important Powers of Two

| Power | Value |
|---|---:|
| 2⁰ | 1 |
| 2¹⁰ | 1,024 |
| 2²⁰ | 1,048,576 |
| 2³⁰ | 1,073,741,824 |
| 2⁴⁰ | 1,099,511,627,776 |

#### Storage Units

- 1 KB = 1,024 Bytes (binary approximation)
- 1 MB = 1,024 KB
- 1 GB = 1,024 MB
- 1 TB = 1,024 GB
- 1 PB = 1,024 TB

#### Importance

- Helps estimate storage requirements.
- Simplifies memory calculations.
- Helps calculate bandwidth requirements.
- Makes large data volume calculations easier.

**Key Takeaway:** Understanding powers of two makes it easier to estimate storage, memory, and bandwidth requirements in system design.



### 2. Latency Numbers Every Programmer Should Know

Latency is the time taken to complete an operation in a computer system. Understanding latency helps developers identify performance bottlenecks and design faster systems.

#### Important Latency Numbers (2020)

| Operation | Latency |
|---|---:|
| L1 Cache Access | 0.5 ns |
| L2 Cache Access | 7 ns |
| Main Memory Access | 100 ns |
| SSD Random Read | 150 µs |
| HDD Random Seek | 10 ms |
| Round-Trip in Data Center | 500 µs |
| Inter-Region Data Center | 150 ms |

#### Key Insights

1. **Memory is Fast, Disk is Slow:** Accessing data from RAM is generally much faster than accessing it from storage devices.
2. **Avoid Disk Seeks:** Reduce unnecessary disk operations to improve system performance.
3. **Compress Data:** Use compression techniques such as Gzip and Brotli to reduce data transfer size and save bandwidth.

#### Practical Applications

- Use caching (Redis) to reduce repeated database queries.
- Use database indexes to improve data retrieval.
- Compress API responses to reduce network bandwidth.
- Minimize unnecessary network communication.

**Key Takeaway:** Understanding latency helps developers identify slow operations and improve overall system performance.



### 3. Availability Numbers

Availability refers to the percentage of time a system remains operational and accessible to users. High Availability (HA) aims to minimize downtime.

#### Uptime and Downtime

- **Uptime:** The time during which a system is operational.
- **Downtime:** The time during which a system is unavailable.

#### Availability in Nines

Availability is commonly expressed in nines.

| Availability | Downtime Per Year |
|---|---:|
| 99% (Two Nines) | 3.65 days |
| 99.9% (Three Nines) | 8.8 hours |
| 99.99% (Four Nines) | 52 minutes |
| 99.999% (Five Nines) | 5.3 minutes |
| 99.9999% (Six Nines) | 31.56 seconds |

#### SLA (Service Level Agreement)

An SLA is an agreement between a service provider and a customer that defines expected service availability and performance.

Cloud providers such as AWS, Google Cloud, and Microsoft Azure offer specific availability SLAs for their services.

#### How to Improve Availability?

- **Load Balancing:** Distributes incoming traffic across multiple servers.
- **Redundancy:** Maintains backup servers and resources.
- **Monitoring:** Detects system failures and performance issues.
- **Failover:** Automatically switches to a backup system when the primary system fails.

**Key Takeaway:** Higher availability means less downtime and better system reliability. The number of nines helps measure how reliable a system is.



## Section 2: Example Estimation – Twitter QPS and Storage Requirements

### 1. Assumptions

- Monthly Active Users (MAU): 300 Million
- Daily Active Users (DAU): 50% of MAU
- Average Tweets per User per Day: 2
- Tweets Containing Media: 10%
- Data Retention Period: 5 Years

### 2. Query Per Second (QPS) Estimation

**Step 1: Calculate Daily Active Users**

DAU = 300M × 50% = 150M

**Step 2: Calculate Daily Tweets**

Daily Tweets = 150M × 2 = 300M

**Step 3: Calculate Average QPS**

QPS = Total Daily Tweets / 86,400

QPS = 300M / 86,400 ≈ 3,500

**Step 4: Calculate Peak QPS**

Assuming peak traffic is 2 times the average traffic:

Peak QPS = 3,500 × 2 ≈ 7,000

### 3. Media Storage Estimation

#### Tweet Size Components

| Component | Size |
|---|---:|
| tweet_id | 64 Bytes |
| text | 140 Bytes |
| media | 1 MB |

**Step 1: Calculate Daily Media Tweets**

Daily Media Tweets = 150M × 2 × 10%

Daily Media Tweets = 30M

**Step 2: Calculate Daily Media Storage**

Daily Media Storage = 30M × 1 MB

Daily Media Storage = 30 TB

**Step 3: Calculate Five-Year Storage**

Five-Year Storage = 30 TB × 365 × 5

Five-Year Storage = 54,750 TB ≈ 55 PB

### 4. Final Estimation Summary

| Metric | Estimated Value |
|---|---:|
| Daily Active Users | 150M |
| Daily Tweets | 300M |
| Average QPS | 3,500 |
| Peak QPS | 7,000 |
| Daily Media Storage | 30 TB |
| Five-Year Media Storage | 55 PB |

### Key Takeaways

- QPS measures the number of requests processed per second.
- Peak QPS estimates the maximum expected traffic.
- Storage estimation helps determine future storage requirements.
- Large-scale systems require careful planning for traffic and data growth.

**Note:** These are simplified estimates based on the given assumptions. They exclude tweet metadata, database indexes, replication, and backups.


## Section 3: Practice Example – Instagram QPS and Storage Estimation

### 1. Assumptions

- Monthly Active Users (MAU): 200 Million
- Daily Active Users (DAU): 40% of MAU
- Average Posts per User per Day: 3
- Posts Containing Images: 20%
- Average Image Size: 2 MB
- Data Retention Period: 3 Years
- Peak Traffic: 2 times Average Traffic

### 2. QPS Estimation

**Step 1: Calculate Daily Active Users**

DAU = 200M × 40%

DAU = 80M

**Step 2: Calculate Daily Posts**

Daily Posts = 80M × 3

Daily Posts = 240M

**Step 3: Calculate Average QPS**

QPS = Total Daily Posts / 86,400

QPS = 240M / 86,400

QPS ≈ 2,778

**Step 4: Calculate Peak QPS**

Peak QPS = Average QPS × 2

Peak QPS = 2,778 × 2

Peak QPS ≈ 5,556

### 3. Image Storage Estimation

**Step 1: Calculate Daily Image Posts**

Daily Image Posts = 240M × 20%

Daily Image Posts = 48M

**Step 2: Calculate Daily Image Storage**

Daily Storage = 48M × 2 MB

Daily Storage = 96 TB

**Step 3: Calculate Three-Year Storage**

Three-Year Storage = 96 TB × 365 × 3

Three-Year Storage = 105,120 TB

Three-Year Storage ≈ 105.12 PB

### 4. Final Estimation Summary

| Metric | Estimated Value |
|---|---:|
| Daily Active Users | 80M |
| Daily Posts | 240M |
| Average QPS | 2,778 |
| Peak QPS | 5,556 |
| Daily Image Posts | 48M |
| Daily Image Storage | 96 TB |
| Three-Year Image Storage | 105.12 PB |

### Key Takeaways

- DAU represents daily active users.
- QPS measures average requests or operations per second.
- Peak QPS estimates maximum expected traffic.
- Image storage estimation helps plan long-term infrastructure requirements.

**Note:** This is a simplified estimation. It excludes post text, metadata, database indexes, replicas, backups, and storage overhead.
