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
