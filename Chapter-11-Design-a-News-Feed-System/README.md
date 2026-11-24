# Chapter 11: Design a News Feed System

## 1. Introduction

A **News Feed System** displays a continuously updating list of posts from a user's friends, followers, or connections.

### Types of Content
- Text / Status Updates
- Photos
- Videos
- Links

**Daily-Life Example:**
When a user opens Facebook, they see recent posts, photos, and videos shared by their friends in one place.

## 2. Purpose of a News Feed System

The main purposes are:

- Display relevant content.
- Show the latest posts.
- Provide updates from connections.
- Increase user engagement.

**Daily-Life Example:**
Instead of visiting every friend's profile, Facebook displays their posts in one feed.

## 3. MERN Stack Implementation

- **React:** Displays posts in the user interface.
- **Node.js / Express:** Handles feed API requests.
- **MongoDB:** Stores users, posts, and connections.

**Example:**
React requests `/api/feed`, Express retrieves relevant posts from MongoDB, and the frontend displays them.

## 4. Real-World Examples

- **Facebook:** Friends' posts, photos, and videos.
- **Instagram:** Photos, reels, and videos from followed accounts.
- **X (Twitter):** Tweets and updates from followed accounts.



# Step 1: Understanding the Problem

## 1. Requirements

### Platform
- Support Web applications.
- Support Mobile applications (Android and iOS).

**Daily-Life Example:** Users can access Facebook from both laptops and mobile phones.

### Features

**A. Publish Posts**
- Users can publish text updates, images, and videos.

**Daily-Life Example:** A user uploads a photo with a caption on Facebook.

**B. View Friends' Posts**
- Users can see posts published by their friends in their news feed.

**Daily-Life Example:** Facebook displays posts from all your friends in one feed.

## 2. Sorting

Feeds are sorted in **Reverse Chronological Order**.

- Newest posts appear first.
- Oldest posts appear last.

**Daily-Life Example:** The latest messages appear at the bottom of a WhatsApp chat.

**MongoDB Example:**
```javascript
Post.find({ userId: { $in: friendIds } })
  .sort({ createdAt: -1 });
```

## 3. Scale Requirements

### A. Maximum 5,000 Friends Per User
- Each user can have up to 5,000 friends.

**Daily-Life Example:** A user with 5,000 friends may receive posts from all those connections.

### B. 10 Million Daily Active Users (DAU)
- The system must support 10 million unique active users daily.
- Requires scalable infrastructure and optimized database queries.

**Daily-Life Example:** 10 million different people use the platform within one day.

### C. Different Media Types
The feed supports:
- Text
- Images
- Videos

**Daily-Life Example:** Instagram displays captions, photos, and reels.

**MERN Example:**
- MongoDB: Stores post information and media URLs.
- Cloud Storage: Stores images and videos.
- Express: Handles APIs and uploads.
- React: Displays feed content.



# Step 2: High-Level Design

## Overview

The News Feed System consists of two main flows:

1. Feed Publishing
2. News Feed Building

## 1. Feed Publishing

Feed Publishing is the process of saving a new post and distributing it to the feeds of the user's friends.

### Process
1. User publishes a post.
2. The post is saved in the database.
3. The system identifies the user's friends.
4. The post is propagated to their feeds.

**Daily-Life Example:**

Ali uploads a photo on Facebook. The system saves it and distributes it to his friends' feeds.

**Key Point:** Distribute new posts to friends.
<img width="1513" height="949" alt="image" src="https://github.com/user-attachments/assets/76b7e2bd-b0e7-4d20-af04-57683b0e366c" />


## 2. News Feed Building

News Feed Building is the process of collecting posts from a user's friends and preparing a news feed.

### Process
1. User requests their news feed.
2. System retrieves posts from friends.
3. All posts are aggregated.
4. Posts are sorted in reverse chronological order.
5. The final feed is returned to the user.

**Daily-Life Example:**

Ahmed opens Facebook. The system collects posts from his friends and displays the newest posts first.

**Key Point:** Aggregate and sort friends' posts.
<img width="1040" height="460" alt="image" src="https://github.com/user-attachments/assets/dc68fc70-1a56-4254-8721-8731d10e66f2" />


## 3. Difference Between Both Flows

| Feed Publishing | News Feed Building |
|---|---|
| Publishes a post. | Retrieves the feed. |
| Distributes posts to friends. | Collects posts from friends. |
| Write operation. | Read operation. |
| Happens when a post is published. | Happens when a feed is requested. |
