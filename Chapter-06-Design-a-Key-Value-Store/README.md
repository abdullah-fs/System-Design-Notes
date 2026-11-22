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
