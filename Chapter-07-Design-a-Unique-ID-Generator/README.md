## 1. Introduction

A Unique ID Generator creates unique identifiers for records in distributed systems. Traditional auto-increment IDs are unsuitable due to scalability and synchronization issues.

**Key Requirements:**

- **Uniqueness:** Every ID must be different.
- **Sortable:** IDs should be ordered by date/time.
- **64-bit:** IDs must fit within 64 bits.
- **High Throughput:** Generate over 10,000 IDs per second.

**Example:**

Imagine Facebook has multiple servers creating posts simultaneously.

- Server 1 → Post ID: `100001`
- Server 2 → Post ID: `100002`
- Server 3 → Post ID: `100003`

Even if posts are created simultaneously, each must receive a unique ID, and IDs should be sortable by creation time.



## 2. Understanding the Problem

### Basic Requirements

1. **Uniqueness:** Every ID must be unique across all servers.

   **Example:**
   ```text
   Server 1 → ID: 1001
   Server 2 → ID: 1002
   Server 3 → ID: 1003
   ```

2. **64-bit Numerical IDs:** IDs must be numerical and fit within 64 bits.

   **Example:**
   ```text
   User ID: 987654321012345
   ```
   This numerical ID fits within the 64-bit limit.

3. **Time-Based Increment:** IDs should increase with time, but not necessarily by `+1`.

   **Example:**
   ```text
   10:00:01 → 1001
   10:00:02 → 1050
   10:00:03 → 1200
   ```

4. **Sortable by Date:** IDs should support sorting based on their creation time.

   **Example:**
   ```text
   10:00:01 → ID: 1001
   10:00:02 → ID: 1050
   10:00:03 → ID: 1200
   ```
   Sorting IDs helps maintain creation-time order.

5. **High Throughput:** The system must generate at least 10,000 IDs per second.

   **Example:**
   ```text
   Second 1 → 10,000 IDs
   Second 2 → 10,000 IDs
   Second 3 → 10,000 IDs
   ```

### Real-Life Example

Imagine Facebook has millions of users creating posts simultaneously across multiple servers.

- Every post needs a unique ID.
- IDs must fit within 64 bits.
- IDs should help identify when posts were created.
- The system must generate thousands of IDs per second.

A Unique ID Generator handles these requirements efficiently without relying on a single database's auto-increment mechanism.



## 3. High-Level Design Options

### 1. Multi-Master Replication

**Approach:**
- Uses multiple database servers with `auto_increment`.
- Each server generates IDs using a different increment value (`+k`), where `k` is the number of servers.
- This prevents ID conflicts between servers.
<img width="952" height="472" alt="image" src="https://github.com/user-attachments/assets/384f8c5c-65da-48b1-8e14-5038d47c946f" />


**Example:**

For 3 database servers:

```text
Server 1 → 1, 4, 7, 10
Server 2 → 2, 5, 8, 11
Server 3 → 3, 6, 9, 12
```

Each server generates IDs with an increment of `+3`.

**Drawbacks:**
- **Scalability:** Difficult to scale across multiple data centers.
- **Time Ordering:** IDs do not always increase according to creation time.
- **Server Changes:** Adding or removing servers requires configuration changes and may cause ID conflicts.

**Real-Life Example:**

Facebook uses multiple database servers to handle posts. Managing different auto-increment sequences becomes difficult when servers are added or removed.



### 2. UUID (Universally Unique Identifier)

**Approach:**
- Generates 128-bit unique identifiers independently on each server.
- No coordination between servers is required.
<img width="1192" height="324" alt="image" src="https://github.com/user-attachments/assets/e8b0736c-43fe-4007-b898-240b887c8773" />


**Example:**
```text
Server 1 → 550e8400-e29b-41d4-a716-446655440000
Server 2 → 6ba7b810-9dad-11d1-80b4-00c04fd430c8
Server 3 → 3f2504e0-4f89-11d3-9a0c-0305e82c3301
```

**Advantages:**
- **No Coordination:** Servers can generate UUIDs independently.
- **Easy Scalability:** New servers can be added without changing ID generation logic.

**Drawbacks:**
- **128-bit Size:** Exceeds the required 64-bit limit.
- **Not Time-Sortable:** Traditional UUIDs (e.g., UUID v4) are not ordered by creation time.
- **Non-Numeric Format:** UUIDs use hexadecimal characters and hyphens.

**Real-Life Example:**

An e-commerce application uses multiple servers to process orders. Each server independently generates a UUID for every order without coordinating with other servers.



### 3. Ticket Server

**Approach:**
- Uses a centralized database server to generate unique IDs.
- Application servers request IDs from the Ticket Server.
- The Ticket Server increments a counter and returns a new ID.
<img width="1202" height="450" alt="image" src="https://github.com/user-attachments/assets/83cd6b20-61d5-466f-8c99-d9aef5c1b0a4" />

**Example:**
```text
Server 1 → Ticket Server → ID: 1001
Server 2 → Ticket Server → ID: 1002
Server 3 → Ticket Server → ID: 1003
```

**Advantages:**
- **Simple Implementation:** Easy to implement for small-scale systems.
- **Numeric IDs:** Generates simple numerical IDs.

**Drawbacks:**
- **Single Point of Failure:** If the Ticket Server goes down, ID generation stops.
- **Synchronization Challenges:** Multiple servers must coordinate with the central server, creating a potential bottleneck.

**Real-Life Example:**

An e-commerce website has multiple servers processing orders. Each server requests a unique order ID from the Ticket Server before saving a new order.



### 4. Twitter Snowflake Approach

**Approach:**
- Generates unique 64-bit numerical IDs.
- Divides each ID into sections to ensure uniqueness and scalability.
- Each machine can generate IDs independently.
<img width="1252" height="970" alt="image" src="https://github.com/user-attachments/assets/d095af7f-9c6e-4128-b383-4ec9fda9e27b" />

**64-bit ID Structure:**

| Component | Bits | Purpose |
|---|---:|---|
| Sign Bit | 1 | Always `0` (positive ID) |
| Timestamp | 41 | Milliseconds since a custom Epoch |
| Datacenter ID | 5 | Supports 32 datacenters |
| Machine ID | 5 | Supports 32 machines per datacenter |
| Sequence Number | 12 | Supports 4096 IDs per millisecond |
| **Total** | **64** | |

**Components Explained:**

1. **Sign Bit (1 bit):** Always `0` to keep IDs positive.

2. **Timestamp (41 bits):** Stores milliseconds since a custom Epoch (Twitter's default: November 4, 2010). Makes IDs time-sortable.

3. **Datacenter ID (5 bits):** Identifies up to \(2^5 = 32\) datacenters.

4. **Machine ID (5 bits):** Identifies up to \(2^5 = 32\) machines per datacenter.

5. **Sequence Number (12 bits):** Supports up to \(2^{12} = 4096\) IDs per millisecond on each machine. Resets to `0` every millisecond.

**Example:**

```text
Datacenter 1, Machine 1 → Unique ID A
Datacenter 1, Machine 2 → Unique ID B
Datacenter 2, Machine 1 → Unique ID C
```

Different Datacenter IDs, Machine IDs, and Sequence Numbers help prevent duplicate IDs.

**Advantages:**
- **Scalability:** Handles 10,000+ IDs per second across multiple machines.
- **Time Ordering:** IDs can be sorted by generation time.
- **Decentralization:** No central ID server is required.

**Real-Life Example:**

Twitter has multiple datacenters and machines generating tweet IDs simultaneously. Snowflake combines timestamps, datacenter IDs, machine IDs, and sequence numbers to generate unique IDs independently.

**Note:** Snowflake relies on accurate system clocks. Clock differences must be handled to prevent duplicate IDs and ordering issues.



## 4. Additional Considerations

### 1. Clock Synchronization

- Distributed servers must maintain synchronized clocks to generate time-ordered IDs.
- Use **NTP (Network Time Protocol)** to minimize clock differences.

**Example:** If Server A's clock shows 10:00:01 and Server B's shows 10:00:03, IDs may not follow the correct time order.

### 2. Section Length Tuning

- Adjust the number of bits assigned to each section according to system requirements.
- More timestamp bits provide a longer time range.
- More sequence bits allow more IDs per millisecond.
- More machine bits support more machines.

**Example:** A system generating many IDs per millisecond may allocate more bits to the sequence number.

### 3. High Availability

- ID generators should remain operational even if a server fails.
- Use redundancy and failover mechanisms.
- A backup server can take over when the primary server fails.

**Example:** If the primary ID generator goes down, a backup generator starts providing IDs.

### Real-Life Example

A large e-commerce platform uses synchronized server clocks, optimized bit allocation, and backup ID generators to generate unique order IDs reliably, even during high traffic or server failures.

