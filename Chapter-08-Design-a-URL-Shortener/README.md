# Chapter 8: Design a URL Shortener

## Introduction

A URL Shortener is a service that converts long URLs into short, easy-to-share links, like TinyURL and Bitly.

**Example:**

- Long URL: `https://www.amazon.com/products/electronics/samsung-galaxy-s25`
- Short URL: `https://tinyurl.com/abc123`

When someone clicks the short URL, they are automatically redirected to the original URL.

### Popular URL Shortening Websites

- [TinyURL](https://tinyurl.com/)
- [Bitly](https://bitly.com/)
- [is.gd](https://is.gd/)

## Requirements

### 1. Unique and Short URLs

- Every generated short URL must be unique.
- Short URLs should be as short as possible.
- Different original URLs must not receive the same short URL.

**Example:**

```text
google.com    → short.ly/abc123
facebook.com  → short.ly/xyz789
youtube.com   → short.ly/pqr456
```

### 2. URL Generation Capacity

- Handle 100 million URL generations per day.
- Support a 10-year operational period.

**Calculation:**

- Daily: 100 million URLs
- Yearly: 36.5 billion URLs
- 10 Years: 365 billion URLs

### 3. Read-to-Write Ratio (10:1)

The system must support 10 times more read requests than write requests.

- **Write:** Creating a new short URL.
- **Read:** Accessing a short URL and redirecting to the original URL.

**Example:**

For every 100 URLs created, approximately 1,000 requests may be made to access them.

Therefore, the system should be optimized for read operations.

### 4. Storage Requirements

The system must store approximately 365 billion URL records over 10 years.

Assuming each record requires approximately 1 KB:

```text
365 billion × 1 KB ≈ 365 TB
```

Therefore, approximately **365 TB of storage** will be required.

**Example of stored URL mappings:**

| Short URL | Original URL |
|---|---|
| abc123 | google.com |
| xyz789 | facebook.com |
| pqr456 | youtube.com |

*Note: Actual storage requirements depend on record size and database overhead.*



## Step 1: High-Level Design

### 1. API Endpoints

The URL Shortener system uses two main API endpoints.

**A. URL Shortening**

- **Method:** POST
- **Endpoint:** `/api/v1/data/shorten`
- **Parameter:** `longUrl`
- **Purpose:** Converts a long URL into a short URL.

Example Request:

```json
{
  "longUrl": "https://www.amazon.com/products/samsung-galaxy-s25"
}
```

Example Response:

```json
{
  "shortURL": "https://tinyurl.com/abc123"
}
```

**B. URL Redirecting**

- **Method:** GET
- **Endpoint:** `/{shortUrl}`
- **Purpose:** Retrieves the original URL and redirects the user to it.

Example:

```text
tinyurl.com/abc123
        |
        v
Find original URL in database
        |
        v
Redirect to original website
```

### 2. URL Redirection

HTTP provides two common redirect status codes:

**A. 301 Redirect (Permanent)**

- Indicates that the URL has permanently moved.
- Browsers can cache the redirect response.
- Subsequent requests may bypass the URL Shortener server.
- Makes click tracking less reliable.

**B. 302 Redirect (Temporary)**

- Indicates a temporary redirect.
- Browsers generally do not cache it like a permanent redirect.
- Requests can reach the URL Shortener server again.
- Useful for click tracking and analytics.

### 3. URL Shortening

A **Hash Function** is used to generate a short code from a long URL.

Example:

```text
Long URL
   |
   v
Hash Function
   |
   v
Hash Value: abc123
   |
   v
Short URL: tinyurl.com/abc123
```

**Hash Function Requirements:**

1. **Consistent Mapping:** Each long URL should generate a consistent hash value when using a deterministic approach.

2. **Unique Mapping:** Different long URLs must not generate the same hash value. Hash collisions must be handled.

3. **Reverse Lookup:** The original URL must be retrievable using the generated hash value through a database mapping.

**Example Database Mapping:**

| Short Code | Original URL |
|---|---|
| abc123 | google.com |
| xyz789 | facebook.com |

**Important:** Hash functions are not reversible. The original URL is retrieved through database mapping, not by reversing the hash.



## Step 2: Deep Dive into Design

### 1. Data Model

Store `<shortURL, longURL>` mappings in a relational database to efficiently manage URL records.

**Database Table: `urls`**

| Column | Description |
|---|---|
| `id` | Primary key for unique record identification. |
| `shortURL` | Generated short URL or short code. |
| `longURL` | Original long URL. |

**Example:**

| id | shortURL | longURL |
|---|---|---|
| 1 | abc123 | google.com |
| 2 | xyz789 | facebook.com |
| 3 | pqr456 | youtube.com |

### Table Schema

```sql
CREATE TABLE urls (
    id BIGINT PRIMARY KEY,
    shortURL VARCHAR(20) UNIQUE,
    longURL TEXT
);
```

**Key Points:**

- **Primary Key:** Uniquely identifies each database record.
- **Unique Constraint:** Ensures every short URL is unique.
- **Indexing:** Helps retrieve URL mappings efficiently.
- **Relational Database:** Stores structured URL mappings and supports efficient lookups.

**MERN Stack Example:**

In MongoDB, the same mapping can be stored as:

```js
{
  shortURL: "abc123",
  longURL: "https://www.google.com"
}
```

A unique index can be created on `shortURL` to prevent duplicate short codes.



### 2. Hash Function

A hash function is used to generate short codes for long URLs. There are two common approaches:

#### 1. Base 62 Conversion

- Encodes numbers using 62 characters: `[0-9, a-z, A-Z]`.
- Assign a unique ID to each URL and convert it into Base 62.
- The converted value becomes the short URL.
- A 7-character code supports approximately 3.5 trillion unique combinations.

**Example:**

```text
Unique ID: 2009215674938
Base 62:   zn9edcu

Short URL: tinyurl.com/zn9edcu
```

**Capacity Calculation:**

\[
62^7 \approx 3.52 \text{ trillion}
\]

#### 2. Hash + Collision Resolution

- Apply hash functions such as CRC32, MD5, or SHA-1 to the original URL.
- Use the first 7 characters of the generated hash as the short code.
- Different URLs can generate the same short code, causing a **hash collision**.

**Collision Resolution:**

- Check whether the generated short code already exists in the database.
- If a collision occurs, append a predefined string (salt) and generate another hash.
- Repeat until a unique short code is generated.
- Bloom Filters can improve lookup efficiency by checking whether a short code might already exist.

**Example:**

```text
First Attempt:
google.com → abc123x (Collision)

Second Attempt:
google.com + salt → xyz789p (Unique)
```

### 3. Comparison: Base 62 vs Hash + Collision Resolution

| Feature | Hash + Collision | Base 62 |
|---|---|---|
| URL Length | Fixed | Variable |
| Unique ID Generator | Not required | Required |
| Collision | Possible | Not possible with unique IDs |
| Next Available URL | Difficult | Easy with sequential IDs |
| Security | Depends on hash design | Sequential IDs can be guessed |

**Key Points:**

- **Base 62:** Converts unique numerical IDs into short alphanumeric codes.
- **Hashing:** Generates short codes from original URLs.
- **Collision:** Occurs when different URLs generate the same short code.
- **Bloom Filter:** Helps identify possible existing codes efficiently.
- **Security Concern:** Sequential IDs can make short URLs predictable.



### 4. URL Shortening Flow

1. Check whether the `longURL` already exists in the database.
2. If found, return the existing `shortURL`.
3. Otherwise:
   - Generate a unique ID using a **Distributed ID Generator**.
   - Convert the ID into a short code using **Base 62**.
   - Store the `<id, shortURL, longURL>` mapping in the database.
4. Return the generated short URL to the user.

**Flow:**

```text
Long URL
   |
   v
Check Database
   |
   +---- Found ----> Return Existing Short URL
   |
   +---- Not Found
          |
          v
   Generate Unique ID
          |
          v
    Base 62 Conversion
          |
          v
    Generate Short URL
          |
          v
    Store in Database
          |
          v
    Return Short URL
```

### 5. URL Redirecting Flow

1. User clicks a `shortURL`.
2. Search for the corresponding original URL:
   - Check the **Cache** first for faster access.
   - If not found, query the database.
3. Retrieve the original `longURL`.
4. Redirect the user to the original URL.

**Flow:**

```text
User Clicks Short URL
         |
         v
     Check Cache
         |
    +----+----+
    |         |
  Found    Not Found
    |         |
    |         v
    |    Query Database
    |         |
    +----+----+
         |
         v
  Retrieve Original URL
         |
         v
   Redirect to Website
```

**Key Points:**

- **Cache Hit:** URL is found in cache, resulting in a faster response.
- **Cache Miss:** URL is not found in cache, so the database is queried.
- **Distributed ID Generator:** Generates unique IDs across multiple servers.
- **Base 62:** Converts numerical IDs into short alphanumeric codes.
- **Redis:** Can be used as a cache for frequently accessed URLs.
