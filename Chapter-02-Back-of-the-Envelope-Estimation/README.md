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
