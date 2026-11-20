# Chapter 5: Design Consistent Hashing

## Introduction

Consistent Hashing is a technique used to distribute data and requests across multiple servers. It minimizes data redistribution when servers are added or removed, making horizontal scaling more efficient.

## Key Objectives

- **Horizontal Scaling:** Distribute data across multiple servers.
- **Minimal Redistribution:** Move only a small portion of data when servers are added or removed.
- **Load Distribution:** Distribute data and requests across servers.
- **Reduce Hotspots:** Prevent excessive load on a single server.

## Example

Consider an application with three servers:

- Server A
- Server B
- Server C

If Server B fails or a new Server D is added, traditional hashing may require redistributing a large amount of data.

Consistent Hashing minimizes this redistribution by moving only the affected data to maintain efficient distribution.

## Real-World Applications

- Distributed Databases
- Redis Clusters
- Distributed Caching
- Load Balancing
- Amazon DynamoDB

## Key Benefits

- Supports horizontal scalability.
- Minimizes data redistribution.
- Improves resource utilization.
- Helps maintain system availability.
- Supports distributed systems efficiently.

## Summary

Consistent Hashing is an effective technique for distributing data across multiple servers while minimizing data movement when servers are added or removed.



## The Rehashing Problem

### Explanation

Traditional hashing methods distribute data across servers using the following formula:

```javascript
serverIndex = hash(key) % N
```

Where:
- `hash(key)`: Hash value of the data key.
- `N`: Total number of servers.
- `%`: Modulo operator.

When the number of servers changes, the modulo result changes, causing many keys to be reassigned.

### Problems

**1. Adding a Server**
- Adding a new server changes the total server count.
- Many keys get assigned to different servers.
- Existing cache entries may become inaccessible through their previous mappings.
<img width="1284" height="708" alt="image" src="https://github.com/user-attachments/assets/ac950065-640d-4257-a503-7cfd250af05d" />
<img width="1246" height="814" alt="image" src="https://github.com/user-attachments/assets/ecff0c13-fd11-42ee-a770-0dd700d19380" />


**2. Removing a Server**
- Removing a server changes the server count.
- Most keys may be reassigned to other servers.
- This leads to increased cache misses.

### Key Issues

- **Cache Misses:** Previously cached data cannot be found at the newly calculated server.
- **Data Redistribution:** A large number of keys need reassignment.
- **Performance Degradation:** Additional database requests increase latency.
- **Server Overload:** Increased requests can overload backend servers.

### Limitations

Traditional hashing works well when the number of servers remains fixed. However, it becomes inefficient when servers are frequently added or removed.

### Solution

Consistent Hashing solves this problem by minimizing key redistribution when the server pool changes.



## Consistent Hashing

### Definition

Consistent Hashing is a technique used to distribute data across multiple servers. It ensures that only a fraction of keys are remapped when servers are added or removed, minimizing data redistribution and improving scalability.

### Key Concepts

#### 1. Hash Space and Hash Ring
<img width="1454" height="946" alt="image" src="https://github.com/user-attachments/assets/76fac7c8-df19-47f6-a218-ba63ac134a22" />


- **Hash Space:** A range of possible hash values from `0` to `2^160 - 1`, when using a 160-bit hash function like SHA-1.
- **Hash Ring:** A circular representation of the hash space, created by connecting both ends.
- **Server Mapping:** Servers are mapped onto the ring using a hash function based on their IP addresses or names.
<img width="1402" height="930" alt="image" src="https://github.com/user-attachments/assets/3b1dd74c-fd72-41e8-b4ea-17c2f29c2c45" />


#### 2. Server Lookup
<img width="1176" height="720" alt="image" src="https://github.com/user-attachments/assets/af2ba61a-1472-4485-a89e-e1f4881d50b1" />

- Each data key is hashed to determine its position on the ring.
- Starting from the key's position, the ring is traversed clockwise.
- The first server encountered is responsible for storing that key.

#### 3. Adding a Server

- A new server's hash value is calculated.
- The server is placed at its corresponding position on the ring.
- Only affected keys are reassigned to the new server.
- Other keys remain on their existing servers.

#### 4. Removing a Server

- The server is removed from the hash ring.
- Its assigned keys are transferred to the next clockwise server.
- Other keys generally remain unchanged.
<img width="1452" height="1018" alt="image" src="https://github.com/user-attachments/assets/cc96247e-7c63-43a4-9bea-78f61f233016" />


### Practical Example

Consider three servers:

- Server A = 100
- Server B = 300
- Server C = 500

A new Server D is added with hash value `400`.

Server D is placed between Server B and Server C because its hash value falls within that range.

Only the affected keys are reassigned to Server D.

### Key Benefits

- Minimizes data redistribution.
- Reduces cache misses.
- Supports horizontal scaling.
- Improves scalability in distributed systems.
- Simplifies server addition and removal.

### Summary

Consistent Hashing distributes data across servers using a circular hash ring. It assigns keys to the next clockwise server and minimizes data movement when the server pool changes.



# Virtual Nodes (VNodes)

## Challenges in Basic Consistent Hashing

Basic Consistent Hashing has two main problems:

1. **Uneven Partition Sizes:** Some servers may receive larger portions of the hash ring than others.
2. **Non-uniform Key Distribution:** Some servers may receive significantly more keys or requests, causing load imbalance and hotspots.

## Solution: Virtual Nodes

Virtual Nodes (VNodes) solve these problems by representing each physical server with multiple virtual nodes on the hash ring.

- Each virtual node has its own position on the ring.
- All virtual nodes belonging to one physical server point to that same server.
- Keys are assigned to the nearest virtual node in the clockwise direction.
- Increasing the number of virtual nodes generally improves data distribution and load balancing.

## Example

Suppose we have three physical servers:

| Physical Server | Virtual Nodes |
|---|---|
| Server A | A1, A2, A3 |
| Server B | B1, B2, B3 |
| Server C | C1, C2, C3 |

Each virtual node gets a separate position on the hash ring. This allows every physical server to handle multiple smaller portions of the ring instead of one large portion.

## Benefits

- More balanced data distribution.
- Improved load balancing.
- Reduced chances of server hotspots.
- Better distribution when servers are added or removed.
- More virtual nodes generally result in a more balanced distribution and lower standard deviation.

## Standard Deviation

Standard deviation measures how much the data distribution varies from the average.

- **High standard deviation:** Large differences in server load.
- **Low standard deviation:** More balanced server load.

## Daily-Life Example: Restaurant Delivery

Imagine a restaurant with three delivery riders:

- Rider A
- Rider B
- Rider C

Without multiple delivery zones, Rider A might receive 600 orders, Rider B 200, and Rider C 100.

To balance the workload, the manager assigns multiple delivery zones to each rider:

- Rider A → A1, A2, A3
- Rider B → B1, B2, B3
- Rider C → C1, C2, C3

Now orders can be distributed across different zones, helping balance the workload among riders.

**Connection to Consistent Hashing:**

| Restaurant | Consistent Hashing |
|---|---|
| Delivery rider | Physical server |
| Delivery zone | Virtual node |
| Customer order | Data key |
| Assigning an order | Assigning a key to a server |

**Important:** A1, A2, and A3 are not separate riders. They represent the same physical Rider A, just as multiple virtual nodes represent one physical server.

## Summary

Virtual Nodes allow one physical server to occupy multiple positions on the hash ring. They improve key distribution, reduce load imbalance, and make consistent hashing more efficient.
