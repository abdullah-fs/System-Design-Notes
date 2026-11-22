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
