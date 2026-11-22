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
<img width="1114" height="950" alt="image" src="https://github.com/user-attachments/assets/4b7371e0-a73c-4013-a475-8e307c8024da" />


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



### 4. Inconsistency Resolution

Data replication improves availability but can cause inconsistencies among replicas.

**Versioning and Vector Clocks** are used to track data changes and detect conflicts.

#### 1. Versioning

Versioning means treating each data modification as a new version of the data.

- Every modification creates a new version.
- Helps track changes across replicas.
- Helps detect simultaneous updates.

**Example: University Database**

Imagine two servers store a student's name:

- Server 1 changes "Abdullah" to "Ahmad" (Version V1).
- Server 2 simultaneously changes "Abdullah" to "Ali" (Version V2).

Both servers contain different updates, creating a conflict.

#### 2. Vector Clocks

A Vector Clock is a mechanism that tracks data versions using server identifiers and version counters.

It helps determine whether:
- One version is older than another.
- One version is newer than another.
- Two versions have conflicting updates.

**Setup:**

A vector clock is represented as:

`D([S1, v1], [S2, v2], ..., [Sn, vn])`

Where:
- `D` = Data item.
- `Si` = Server identifier.
- `vi` = Version counter for that server.

**Updating Vector Clocks:**

When data is modified:
- If the server already exists in the vector clock, increment its counter.
- Otherwise, add a new entry for that server.

**Example:**

Initial vector clock:

`D([S1,0], [S2,0])`

After S1 updates:

`D([S1,1], [S2,0])`

After S2 independently updates:

`D([S1,0], [S2,1])`

**Conflict Detection:**

1. **No Conflict (Ancestor):**
   - Version X is an ancestor of Y if all counters in X are less than or equal to the corresponding counters in Y.
   - At least one counter must be smaller for a strictly newer version.

   Example:
   - X = `[1,0]`
   - Y = `[2,1]`

2. **Conflict Exists (Siblings):**
   - Neither version is an ancestor of the other.
   - Indicates independent or concurrent updates.

   Example:
   - X = `[1,0]`
   - Y = `[0,1]`

#### 3. Conflict Resolution

Conflict Resolution means resolving different versions of the same data and deciding the final correct value.

**Example: University Database**

Imagine two servers update Abdullah's name simultaneously:
<img width="1090" height="638" alt="image" src="https://github.com/user-attachments/assets/b977bf18-ee95-4d61-a106-24d6ae3a3f58" />
<img width="1128" height="520" alt="image" src="https://github.com/user-attachments/assets/67e53fb4-a000-45a9-8ecc-2b9ef965c33f" />


- Server 1: Abdullah → Ahmad (V1).
- Server 2: Abdullah → Ali (V2).

Now both servers have different values, creating a conflict.

**Methods of Conflict Resolution:**

1. **Application-specific Logic:**
   - The application uses predefined rules to resolve conflicts.
   - Example: The university accepts the name verified through official documents.

2. **Client Intervention:**
   - The system provides both conflicting versions to the client.
   - The client decides which value is correct.
   - Example: The university administrator selects Ahmad or Ali as the correct name.

**Important:** Vector clocks detect conflicts, while conflict resolution decides how to resolve them.

#### 4. Challenges

- **Client Complexity:** Clients must handle multiple versions and resolve conflicts.
- **Vector Clock Size:** Vector clocks can grow with many servers and updates.
- **Trimming:** Old entries may need to be removed to control vector clock size.
