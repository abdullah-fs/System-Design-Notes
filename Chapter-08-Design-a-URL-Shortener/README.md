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
