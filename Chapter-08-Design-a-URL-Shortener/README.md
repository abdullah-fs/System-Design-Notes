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
