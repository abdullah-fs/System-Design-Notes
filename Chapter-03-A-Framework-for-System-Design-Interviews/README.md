# Chapter 3: A Framework for System Design Interviews

## Introduction

System Design Interviews evaluate a candidate's ability to design scalable systems and solve real-world problems.

### Key Skills Evaluated

- Technical Skills
- Problem-Solving
- Communication
- Collaboration
- Handling Ambiguous Requirements

## 4-Step Framework

### 1. Understand Requirements
Identify functional and non-functional requirements and clarify the problem.

### 2. High-Level Design
Create the overall system architecture and identify major components.

### 3. Detailed Design
Explain components, APIs, databases, data flow, and technologies.

### 4. Identify Bottlenecks
Find performance issues and suggest solutions to improve scalability and reliability.

## Key Takeaways

- Understand the problem before designing.
- Communicate ideas clearly.
- Make reasonable assumptions.
- Design for scalability and reliability.
- Identify potential bottlenecks.

## Goal

Develop a structured approach to solving real-world system design problems during interviews.



## Step 1: Understand the Problem and Establish Design Scope

### 1. Key Objectives

- Clarify functional and non-functional requirements.
- Understand the problem before starting the design.
- Avoid jumping into solutions prematurely.
- Demonstrate critical thinking by asking relevant questions.

### 2. Ask Clarifying Questions

Before designing a system, ask questions to understand its requirements:

- What are the most important features?
- How many users should the system support?
- Are we building for web, mobile, or both?
- What technologies or constraints should we consider?
- What type of data will the system handle?

### 3. Document Assumptions

Write down important assumptions to guide design decisions.

**Example:**
- The system supports millions of users.
- Users can create and view posts.
- Posts can contain images and videos.
- The feed displays the latest posts first.

### 4. Practical Example: Design a News Feed System

Before designing a news feed, ask:

1. Is it a mobile app, web app, or both?
2. How many friends can a user have?
3. Should the feed support images and videos?
4. Should posts appear in reverse chronological order?

**Reverse Chronological Order:** Displaying the latest posts first, followed by older posts.

### 5. Key Takeaways

- Ask questions before proposing solutions.
- Understand system requirements and scale.
- Identify important features and constraints.
- Document assumptions.
- Make design decisions based on clarified requirements.

**Golden Rule:** Never start designing a system before understanding the problem. Asking the right questions is half the solution.



## Step 2: Propose High-Level Design and Get Buy-In

### 1. Key Objectives

- Develop a high-level system architecture.
- Identify major system components.
- Collaborate with the interviewer to refine the design.
- Validate the design against requirements and scalability constraints.

### 2. Draft a Blueprint

Create a basic architecture diagram using the following components:

- **Clients:** Web and mobile applications.
- **APIs:** Handle communication between clients and backend.
- **Application Servers:** Process business logic.
- **Database:** Store permanent data.
- **Cache:** Store frequently accessed data.
- **CDN:** Deliver images and videos efficiently.

### 3. Back-of-the-Envelope Calculations

Perform approximate calculations to check whether the proposed design can handle expected traffic.

Important metrics:
- Daily Active Users (DAU)
- Requests Per Second (QPS)
- Peak QPS
- Storage Requirements
- Bandwidth Requirements

### 4. Walk Through Use Cases

Identify how different operations work and check possible edge cases.

**Example:**
- User publishes a post.
- Post is stored in the database.
- Followers' feeds are updated.
- Users retrieve and view their feeds.

### 5. Example: News Feed System

A news feed system has two main flows:

#### A. Feed Publishing Flow

1. User creates a post.
2. The post is sent to the backend through an API.
3. The post is stored in the database.
4. Followers' feeds are updated with the new post.

**Fanout on Write:** Distributing a new post to followers' feeds when the post is published.

#### B. Feed Retrieval Flow

1. User requests their news feed.
2. Backend retrieves relevant posts.
3. Posts are arranged in reverse chronological order.
4. The feed is returned to the user.

**Fanout on Read:** Retrieving and combining posts from followed users when a user requests their feed.

### 6. Fanout on Write vs Fanout on Read

| Fanout on Write | Fanout on Read |
|---|---|
| Feeds are updated when a post is published. | Posts are retrieved when the feed is requested. |
| Faster feed retrieval. | More work during feed retrieval. |
| Expensive for users with millions of followers. | Useful for users with very large follower counts. |

**Hybrid Approach:** Combining Fanout on Write and Fanout on Read to balance publishing and retrieval performance.

### 7. Key Takeaways

- Create a high-level architecture diagram.
- Identify major system components.
- Perform capacity and traffic estimations.
- Explain publishing and retrieval flows.
- Consider edge cases and scalability.
- Get feedback and refine the design.

**Golden Rule:** Build a high-level blueprint, validate it against expected traffic, and refine it through discussion before moving to detailed design.



## Step 3: Design Deep Dive

### 1. Key Objectives

- Explore critical system components in detail.
- Demonstrate technical understanding.
- Adapt the design according to changing requirements.
- Identify potential performance issues.

### 2. Approach

#### A. Prioritize Key Components

Focus on the components most relevant to the system.

**Example: News Feed System**
- Feed Publishing
- Feed Retrieval
- Database
- Redis Cache

#### B. Discuss Bottlenecks

Identify components that may cause performance problems and propose suitable solutions.

**Example:**

If a celebrity has 50 million followers, updating every follower's feed using Fanout on Write can create huge processing overhead.

**Possible Solutions:**
- Fanout on Read
- Hybrid Approach
- Message Queues
- Background Workers

#### C. Balance Detail

- Focus on important components.
- Avoid unnecessary complexity.
- Do not over-engineer the system.
- Explain technical decisions clearly.

### 3. Example Topics

#### A. URL Shortener

Important areas:
- Hash Function Design
- Unique Short URLs
- URL Mapping
- Redirect Mechanism

#### B. Chat System

Important areas:
- Real-Time Messaging
- Latency Reduction
- WebSockets
- Online/Offline Status
- Message Delivery

#### C. News Feed System

Important areas:
- Feed Publishing
- Feed Retrieval
- Fanout on Write
- Fanout on Read
- Database and Cache Performance

### 4. Key Takeaways

- Focus on critical components.
- Understand the internal working of components.
- Identify performance bottlenecks.
- Propose appropriate solutions.
- Balance technical detail and complexity.
- Adapt the design according to requirements.

### 5. Fanout Strategies

Fanout is a technique used to distribute posts to users' feeds.

#### A. Fanout on Write
Distributes a post to followers' feeds immediately when the post is published.

- Faster feed retrieval.
- More write operations.
- Expensive for users with millions of followers.

#### B. Fanout on Read
Retrieves and combines posts when a user opens their feed.

- Less processing during publishing.
- More processing during feed retrieval.
- Useful for users with millions of followers.

#### C. Hybrid Approach
Combines Fanout on Write and Fanout on Read.

- Fanout on Write for normal users.
- Fanout on Read for celebrities or users with millions of followers.
- Balances read and write performance.

**Example:**
- Normal User → 500 Followers → Fanout on Write.
- Celebrity → 50 Million Followers → Fanout on Read.

**Golden Rule:** Focus on the most important components, identify potential bottlenecks, and design solutions that improve system performance without unnecessary complexity.
