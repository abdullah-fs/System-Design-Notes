# Chapter 13: Design a Search Autocomplete System

## 1. Introduction

Search Autocomplete, also called Typeahead or Incremental Search, provides suggestions while users type in a search box.

The system returns the top-k relevant and popular search queries based on historical search data.

**Example:**

When a user types `pro`, the system may suggest:
- programming
- programming tutorial
- project ideas
- product design
- productivity tips
<img width="1314" height="920" alt="image" src="https://github.com/user-attachments/assets/9431d73f-da3f-40ca-8b6d-fab6b8effc16" />
<img width="592" height="441" alt="image" src="https://github.com/user-attachments/assets/c5d44c19-27c7-4b9c-a745-b6c1895e3048" />
<img width="1600" height="731" alt="image" src="https://github.com/user-attachments/assets/35a387c3-fe92-415f-8d16-e05eb179549b" />
<img width="610" height="457" alt="image" src="https://github.com/user-attachments/assets/e18b540d-e539-41d9-b857-848445554c12" />
<img width="1080" height="613" alt="image" src="https://github.com/user-attachments/assets/023f1785-096a-4ec2-858a-77449af645a5" />
<img width="1494" height="1170" alt="image" src="https://github.com/user-attachments/assets/e7f7a08b-455a-4a49-89b6-9fd708142d44" />
<img width="1600" height="1022" alt="image" src="https://github.com/user-attachments/assets/ae275735-726a-4094-bdcd-4147653cf4de" />

## 2. Key Requirements

### Functional Requirements

- Return up to 5 autocomplete suggestions.
- Suggestions should match the user's input prefix.
- Rank suggestions based on query popularity.
- Support lowercase English characters (`a-z`).

**Example:**

Input: `app`

Output:
1. apple
2. application
3. app development
4. app store
5. app design

### Non-Functional Requirements

- Response time should be less than 100 milliseconds.
- The system should support millions of users.
- The system should be scalable and highly available.

**Example:**

Even when thousands of users type simultaneously, suggestions should appear instantly.

## 3. Query Popularity

Query popularity represents how frequently a search query has been searched by users.

The system uses historical search frequency to rank suggestions.

**Example:**

| Query | Frequency |
|---|---:|
| apple | 1000 |
| application | 800 |
| app development | 600 |

When the user types `app`, the system ranks matching queries according to their frequency.

## 4. Prefix

A prefix is the starting part of a word or search query.

Autocomplete searches for queries that begin with the user's input.

**Example:**

Word: `programming`

Prefixes:
- p
- pr
- pro
- prog
- program

Input: `pro`

Matching queries: `programming`, `product`, `profile`.

Non-matching query: `improve`.

## 5. Data Gathering Service

The Data Gathering Service collects and processes historical search queries.

It maintains the frequency of each query.

**Example:**

Every time a user searches for `programming`, its frequency increases.

```text
programming: 5000
project ideas: 3000
product design: 2000
```

This data helps the system identify popular search queries.

## 6. Query Service

The Query Service handles autocomplete requests from users.

Its responsibilities include:

1. Receiving the user's input.
2. Finding matching queries.
3. Ranking queries by popularity.
4. Returning the top 5 suggestions.

**Example:**

Input: `pro`

The Query Service finds matching queries and returns the five most popular ones.

## 7. Trie Data Structure
![Uploading image.png…]()


A Trie, also called a Prefix Tree, is a tree-like data structure used to store strings efficiently.

Each node represents a character, and words with common prefixes share the same path.

**Example:**

Words: `app`, `apple`, `apply`

These words share the prefix `app`.

```text
        root
         |
         a
         |
         p
         |
         p  ← app
         |
         l
        / \
       e   y
```

**Advantages:**
- Efficient prefix searching.
- Shared prefixes reduce repeated storage.
- Suitable for autocomplete systems.

## 8. Top-K Suggestions

Top-k means selecting the k best results from a collection.

In autocomplete, k is usually 5.

The system selects the most popular matching queries.

**Example:**

Matching queries:

```text
apple: 1000
application: 800
app store: 600
app design: 400
app development: 200
```

For k = 3, the system returns:

```text
apple
application
app store
```

## 9. Caching

Caching stores frequently requested results in memory to reduce response time.

Popular prefixes can be cached with their top suggestions.

**Example:**

The prefix `pro` is searched frequently.

Instead of searching the Trie every time, the system can return cached suggestions for `pro`.

This reduces processing time and improves performance.

## 10. Scalability

Scalability means the system can handle increasing users, queries, and data without significant performance degradation.

Common techniques:
- Distributed servers.
- Load balancing.
- Caching.
- Data partitioning.

**Example:**

If autocomplete traffic increases from 10,000 to 1 million requests per second, additional servers can be added to handle the load.

## 11. System Architecture

```text
Users
  |
  v
Load Balancer
  |
  v
Query Service
  |
  v
Trie / Cache
  |
  v
Top 5 Suggestions
```

Data Gathering:

```text
User Search Queries
        |
        v
Data Gathering Service
        |
        v
Frequency Database
        |
        v
Trie / Autocomplete Data
```

## 12. Performance Optimization

The system must respond in less than 100 milliseconds.

Performance can be improved through:

- Trie-based prefix searching.
- Caching popular prefixes.
- Precomputing top-k suggestions.
- Keeping frequently accessed data in memory.
- Returning only the required number of suggestions.

**Example:**

Instead of calculating the top 5 results for `pro` every time, the system can precompute and store them.

## Summary

A Search Autocomplete System provides fast and popular search suggestions while users type.

Important concepts:
- Prefix matching
- Query popularity
- Data Gathering Service
- Query Service
- Trie
- Top-k ranking
- Caching
- Scalability
- Low latency



# Step 1: Understanding the Problem

## Requirements

### 1. Real-Time Suggestions
Display relevant autocomplete suggestions as the user types.

**Example:**  
Input: `pro`  
Suggestions: `programming`, `project ideas`, `product design`

### 2. Top-k Results
Return up to 5 suggestions, sorted by popularity.

- k = 5
- Higher search frequency means higher ranking.

**Example:**
```text
programming: 5000
project ideas: 4000
product design: 3000
```

The system returns the top 5 matching queries.

### 3. Scalability
The system must support:
- 10 million DAU (Daily Active Users).
- Peak QPS of 48,000.

**Example:**  
The system must handle 48,000 autocomplete requests per second during peak traffic.

### 4. High Availability
The system must remain available even when servers fail.

- Use multiple servers.
- Use load balancing.
- Redirect traffic when a server fails.

**Example:**  
If Server A fails, the Load Balancer sends requests to Server B.

### 5. Data Growth
New query data grows by 0.4 GB per day.

| Duration | Data Growth |
|---|---:|
| 1 day | 0.4 GB |
| 30 days | 12 GB |
| 1 year | 146 GB |

**Calculation:**
```text
0.4 GB × 365 = 146 GB/year
```

The system must support continuous storage growth.

## Summary

| Requirement | Value |
|---|---|
| Maximum Suggestions | 5 |
| Daily Active Users | 10 Million |
| Peak QPS | 48,000 |
| Availability | High |
| Daily Data Growth | 0.4 GB |
| Yearly Data Growth | 146 GB |

The system should provide fast, popular autocomplete suggestions while handling high traffic, server failures, and growing data.



# Step 2: High-Level Design

The Search Autocomplete System is divided into two main services:

1. Data Gathering Service
2. Query Service

## 1. Data Gathering Service

The Data Gathering Service collects user search queries and calculates their frequency.

### Responsibilities
- Collect search queries from analytics logs.
- Aggregate queries and calculate their frequency.
- Update the frequency table.
- Process historical data to build a Trie.

**Example:**

```text
youtube: 5000
youtube music: 3000
youtube premium: 2000
```

The service records how frequently each query is searched.

### Historical Data Processing

Processing large datasets in real-time can be expensive.

The system can process historical data weekly and build an updated Trie.

**Example:**  
Every week, search logs are analyzed to generate an updated Trie containing popular queries.

## 2. Frequency Table

The Frequency Table stores search queries and their popularity.

| Query | Frequency |
|---|---:|
| twitter | 5000 |
| twitch | 4000 |
| twilio | 3000 |
| twitter login | 2000 |
| twitter download | 1000 |

The Query Service uses this table to rank suggestions.

## 3. Query Service

The Query Service handles autocomplete requests from users.

### Responsibilities
- Receive user input.
- Find matching queries using a Trie.
- Retrieve the top-k suggestions.
- Rank suggestions by popularity.
- Return results quickly.

**Example:**

Input: `tw`

Output:

```text
twitter
twitch
twilio
twitter login
twitter download
```

## 4. Trie (Prefix Tree)

A Trie stores queries based on their common prefixes.

It allows the system to find matching queries efficiently.

**Example:**

Queries:
```text
twitter
twitch
twilio
```

All queries share the prefix `tw`.

When a user types `tw`, the Query Service navigates to the `tw` node and retrieves matching suggestions.

## 5. Caching

Caching stores frequently requested autocomplete results in memory.

It reduces repeated Trie searches and improves response time.

**Example:**

```text
Prefix: tw

Cached Results:
twitter
twitch
twilio
twitter login
twitter download
```

Frequently searched prefixes can return cached suggestions directly.

## 6. High-Level Architecture

```text
                 Users
                   |
                   v
             Query Service
                   |
                   v
              Trie / Cache
                   |
                   v
           Top-k Suggestions
```

Data Gathering:

```text
Search Queries
      |
      v
Analytics Logs
      |
      v
Data Gathering Service
      |
      v
Frequency Table
      |
      v
Build Trie
      |
      v
Query Service
```

## 7. Summary

| Component | Responsibility |
|---|---|
| Data Gathering Service | Collect and aggregate search queries |
| Analytics Logs | Store historical search activity |
| Frequency Table | Store query popularity |
| Trie | Efficient prefix-based searching |
| Query Service | Return top-k suggestions |
| Cache | Improve lookup speed |

The Data Gathering Service prepares search data, while the Query Service uses the prepared data to provide fast and popular autocomplete suggestions.
