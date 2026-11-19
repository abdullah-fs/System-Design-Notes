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
