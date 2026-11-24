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



# News Feed APIs

The News Feed System provides two main APIs.

## 1. Feed Publishing API

Allows users to publish new posts.

**Endpoint:**
```http
POST /v1/me/feed
```

**Parameters:**
- `content`: Text or content of the post.
- `auth_token`: Authentication token.

**Process:**
1. User creates a post.
2. Frontend sends a POST request.
3. Backend verifies the authentication token.
4. Post is saved in the database.
5. Success response is returned.

**Daily-Life Example:**

When a user writes "Hello World" on Facebook and clicks Share, the publishing API saves the post.

## 2. News Feed Retrieval API

Allows users to retrieve their news feed.

**Endpoint:**
```http
GET /v1/me/feed
```

**Parameters:**
- `auth_token`: Authentication token.

**Process:**
1. User opens the application.
2. Frontend sends a GET request.
3. Backend verifies authentication.
4. Friends' posts are retrieved.
5. Posts are returned in reverse chronological order.

**Daily-Life Example:**

When a user opens Facebook, the retrieval API provides the latest posts from their friends.

## 3. Difference Between APIs

| Publishing API | Retrieval API |
|---|---|
| POST request | GET request |
| Creates a new post | Retrieves existing posts |
| content + auth_token | auth_token |
| Write operation | Read operation |

**Key Point:** Both APIs use the same endpoint but different HTTP methods.



# Feed Publishing and News Feed Building

## 1. Feed Publishing
<img width="1400" height="903" alt="image" src="https://github.com/user-attachments/assets/ea2d0314-8fbe-4182-a554-ad14811b52a5" />


Feed Publishing is the process of storing a new post and distributing it to the user's friends.

### Components

**1. User Interaction**
- User publishes a post through the Feed Publishing API.

**Daily-Life Example:** Ali uploads a photo on Facebook and clicks Share.

**2. Load Balancer**
- Distributes incoming traffic across multiple web servers.
- Prevents a single server from becoming overloaded.

**Daily-Life Example:** A hospital distributes patients across multiple reception counters.

**3. Web Servers**
- Receive incoming requests.
- Authenticate users.
- Forward requests to relevant services.

**Daily-Life Example:** A receptionist verifies a patient's identity and directs them to the appropriate doctor.

**4. Post Service**
- Stores posts in the database and cache.

**Daily-Life Example:** Keeping original documents in a cabinet and frequently used copies on a desk.

**5. Fanout Service**
- Distributes newly published posts to friends' news feeds.
- Updates friends' feed entries in the cache.

**Daily-Life Example:** Ali publishes a post, and the system distributes it to his 100 friends' feeds.

**6. Notification Service**
- Sends notifications to friends about new posts.

**Daily-Life Example:** Friends receive a notification saying "Ali shared a new post."

## 2. News Feed Building

News Feed Building retrieves posts from the user's feed and prepares them for display.
<img width="744" height="392" alt="image" src="https://github.com/user-attachments/assets/6e808ce4-53e1-4f80-93aa-9362a43c438b" />

### Components

**1. User Interaction**
- User requests their news feed through the Retrieval API.

**Daily-Life Example:** Ahmed opens Facebook to view recent posts.

**2. Load Balancer**
- Distributes incoming feed requests across multiple web servers.

**Daily-Life Example:** A shopping mall distributes visitors across multiple entry gates.

**3. Web Servers**
- Receive requests.
- Authenticate users.
- Forward requests to the News Feed Service.

**Daily-Life Example:** A receptionist verifies identity and directs visitors to the correct department.

**4. News Feed Service**

Responsible for preparing the user's news feed.

It performs two main operations:

**A. Fetch Post IDs**
- Retrieves post IDs from the news feed cache.

Example:
```text
User: Ahmed
Feed Cache: [105, 102, 98, 95]
```

**B. Fetch Complete Post Details**
- Retrieves complete post information using post IDs.
- Fetches data from the database or cache.
- Returns the prepared feed to the user.

**Daily-Life Example:** A shopping list contains product codes. You use those codes to retrieve complete products from a store.

## 3. Feed Publishing vs News Feed Building

| Feed Publishing | News Feed Building |
|---|---|
| Creates a new post. | Retrieves existing posts. |
| Write operation. | Read operation. |
| Uses Post Service. | Uses News Feed Service. |
| Uses Fanout Service. | Fetches post IDs from feed cache. |
| Updates friends' feeds. | Prepares the user's feed. |
| Sends notifications. | Returns complete post details. |

## Key Takeaway

- **Feed Publishing:** Saves and distributes posts.
- **Fanout Service:** Distributes posts to friends' feeds.
- **News Feed Building:** Retrieves post IDs and complete post details.
- **Notification Service:** Notifies friends about new posts.
