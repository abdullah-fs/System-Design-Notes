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
