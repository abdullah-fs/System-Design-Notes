# Chapter 6: Design a Key-Value Store

## 1. Introduction

A **Key-Value Store** is a type of NoSQL database that stores data in unique key-value pairs.

- **Key:** A unique identifier used to access data.
- **Value:** The actual data associated with a key.

### Basic Operations

- `put(key, value)`: Inserts or updates a key-value pair.
- `get(key)`: Retrieves a value using its key.

### Daily Life Example: Mobile Phone Contacts

When you save someone's contact in your mobile phone, their information is stored using an identifier.

```js
{
  "user_101": {
    "name": "Abdullah",
    "phone": "03001234567"
  },
  "user_102": {
    "name": "Abdullah",
    "phone": "03111234567"
  },
  "user_103": {
    "name": "Ali",
    "phone": "03451234567"
  }
}
```

**Understanding:**
- Key: Unique identifier (user_101).
- Value: Contact information (name and phone number).
- Two people can have the same name, but their keys must be unique.

**Operations:**
- `put("user_101", contact)`: Saves a new contact.
- `get("user_101")`: Retrieves Abdullah's contact information.

## 2. Design Characteristics

1. **Small Key-Value Pairs:** Each key-value pair is smaller than 10 KB.
2. **High Availability:** The system remains accessible even when some servers fail.
3. **Scalability:** Handles increasing data and user requests.
4. **Automatic Scaling:** Automatically adjusts resources according to demand.
5. **Tunable Consistency:** Allows choosing different consistency levels based on requirements.
6. **Low Latency:** Provides fast read and write operations.

## 3. Main Goal

Design a distributed key-value store that supports fast data access, high availability, scalability, and tunable consistency.



## 2. Single Server Key-Value Store

A single server key-value store stores all key-value pairs on one server.

### Implementation

- **Hash Table:** Stores key-value pairs in memory (RAM) for fast access.
- **Data Compression:** Reduces data size to save memory.
- **Disk Storage:** Stores less frequently accessed data on disk.

### Example: Mobile Contacts

A mobile phone stores contacts using unique keys. A hash table can help quickly retrieve a contact using its key.

### Limitations

- **Limited Memory:** A single server has limited storage and RAM.
- **Limited Scalability:** Cannot efficiently handle unlimited data and traffic.
- **Single Point of Failure:** If the server fails, the entire system becomes unavailable.

### Solution: Distributed Approach

Use multiple servers to distribute data and requests, improving scalability and availability.



## 3. CAP Theorem During Network Partition

When a network partition occurs, distributed systems must choose between Consistency and Availability.

### CP System

- Prioritizes Consistency and Partition Tolerance.
- Temporarily blocks or rejects some requests to prevent inconsistent data.
- Availability is sacrificed during the partition.
- Normal operations resume when the network connection is restored.

### AP System

- Prioritizes Availability and Partition Tolerance.
- Continues accepting read and write requests during the partition.
- May return stale or inconsistent data temporarily.
- Data can synchronize after the network connection is restored.

### Example: Banking System

Imagine three servers storing a bank balance:

- N1: Rs. 8,000
- N2: Rs. 8,000
- N3: Rs. 10,000

If N3 becomes disconnected:

- **CP:** Blocks some requests to prevent incorrect balance information.
- **AP:** Continues accepting requests, even if some servers return outdated balances.

### Important Point

The sacrifice is often temporary. Once the network partition is resolved, the system can return to normal operation.

**Key Takeaway:** During a network partition, a distributed system must prioritize either Consistency or Availability while maintaining Partition Tolerance.
<img width="1384" height="1106" alt="image" src="https://github.com/user-attachments/assets/720aaec7-e480-4b5e-962f-e60388234ef2" />
<img width="1256" height="1014" alt="image" src="https://github.com/user-attachments/assets/1c399032-5141-46d6-920d-ec1ed80586a4" />



## 4. System Components

### 1. Data Partitioning

Data Partitioning divides large amounts of data across multiple servers to improve scalability and performance.

**Technique: Consistent Hashing**

- Uses consistent hashing to distribute data across multiple servers.
- Each key is mapped to a server using a hash function.
- Helps minimize data redistribution when servers are added or removed.
<img width="1923" height="1353" alt="image" src="https://github.com/user-attachments/assets/e4971118-bbba-4373-96d8-4c26ed95514d" />


### Advantages

1. **Automatic Scaling:** New servers can be added and existing servers removed with minimal data redistribution.
2. **Heterogeneity:** Supports servers with different capacities using virtual nodes.
   - Powerful servers receive more virtual nodes.
   - Less powerful servers receive fewer virtual nodes.
   - The number of virtual nodes is proportional to server capacity.

**Example:** A server with 32 GB RAM can have more virtual nodes than a server with 8 GB RAM, allowing it to handle more data.


### 2. Data Replication

Data Replication means storing multiple copies of the same data across different servers to improve high availability and fault tolerance.

**Replication Across N Servers**

- Data is replicated across `N` servers.
- `N` represents the replication factor.
- If one server fails, data can still be accessed from other replicas.
<img width="1024" height="768" alt="image" src="https://github.com/user-attachments/assets/63976b8c-eeb9-4af0-a4f9-882b080823e0" />


**Server Selection Using Consistent Hashing**

1. Hash the data key to find its position on the hash ring.
2. Move clockwise from that position.
3. Select the first `N` distinct physical servers.
4. Store replicas on those servers.

**Data Center Replication**

- Place replicas across different data centers.
- If one data center fails, other data centers can still provide access to the data.
- Improves reliability and fault tolerance.

**Daily Life Example: University Database**

Imagine a university stores student records across three servers:

- Server N1: Main copy of student records.
- Server N2: Replica copy.
- Server N3: Replica copy.

If N1 fails, the university can retrieve student records from N2 or N3.

If these servers are located in different data centers, the data can remain available even if an entire data center fails.



### 3. Consistency

Since data is replicated across multiple nodes, replicas must be synchronized to maintain data consistency.

#### Quorum Consensus

Quorum Consensus determines how many replicas must respond successfully for read and write operations.

**Three Important Values:**

- **N (Total Replicas):** Total number of data copies.
- **W (Write Quorum):** Minimum number of replicas that must acknowledge a write operation.
- **R (Read Quorum):** Minimum number of replicas that must respond to a read operation.

**Main Rule:**

`W + R > N`

This ensures quorum intersection and supports strong consistency under standard quorum assumptions.

#### Example: University Database

Imagine a university stores student records across three replicas:

- N1
- N2
- N3

Configuration:

`N = 3, W = 2, R = 2`

**Write Operation:**

If Abdullah's semester is updated from 3 to 4:

- N1 receives the update.
- N2 receives the update.
- N3 is temporarily not updated.

Since two replicas acknowledge the write, the operation is successful.

**Read Operation:**

- The system waits for responses from at least two replicas.
- N1 and N2 return the updated semester (4).
- The latest successful update can be retrieved.
<img width="1024" height="1024" alt="image" src="https://github.com/user-attachments/assets/d260d4ba-6ff3-4c07-8286-bcf4d70feac4" />


#### W, R, N Configurations

- **R = 1, W = N:** Optimized for fast reads.
- **W = 1, R = N:** Optimized for fast writes.
- **W + R > N:** Strong consistency supported.
- **W + R ≤ N:** Strong consistency is not guaranteed.

Usually, `N = 3, W = 2, R = 2` is a common configuration.

#### Consistency Models

**1. Strong Consistency**
- Every read returns the value of the latest successful write.
- All clients see the latest committed data.

**2. Weak Consistency**
- Read operations may return outdated values.
- Latest updates are not guaranteed to be immediately visible.

**3. Eventual Consistency**
- Replicas may temporarily contain different values.
- Given enough time without new updates, all replicas eventually become consistent.

**Example:** When a university updates a student's semester on N1, N2 and N3 may temporarily have old data. Eventually, all replicas receive the update.
