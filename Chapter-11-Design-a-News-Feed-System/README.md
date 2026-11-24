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
