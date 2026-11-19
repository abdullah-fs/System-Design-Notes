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
