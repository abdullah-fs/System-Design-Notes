# Chapter 12: Design a Chat System

## 1. Introduction

A Chat System enables users to send and receive messages in real time without refreshing the application.

**Example:** WhatsApp allows users to exchange messages instantly.

## 2. Main Features

### 1. One-on-One Chat
- Supports private conversations between two users.
- Example: Sending a personal message to a friend.

### 2. Group Chat
- Supports group conversations with a maximum of 100 users.
- Example: A university WhatsApp group with 50 students.

### 3. Online Presence Indicators
- Shows whether a user is online or offline.
- Example: WhatsApp displays an "Online" status.

### 4. Multiple Device Support
- Allows users to access their accounts from multiple devices.
- Example: Using WhatsApp on both a mobile phone and laptop.

### 5. Push Notifications
- Notifies users about new messages even when the app is not active.
- Example: Receiving a new message notification on a locked phone.

## 3. System Requirements

### 1. 50 Million Daily Active Users (DAU)
- The system must support 50 million active users per day.
- Example: 50 million different users accessing the chat application daily.

### 2. Permanent Chat History
- All messages must be stored permanently.
- Users should be able to access previous conversations.
- Example: Reading a message sent six months ago.

### 3. Real-Time Communication
- Messages should be delivered with minimal delay.
- Example: A friend receives your message almost immediately after sending it.



# Step 1: Understanding the Problem

## 1. Functional Requirements (Features)

### 1. One-on-One and Group Chat
- Support private conversations between two users.
- Support group chats with a maximum of 100 members.
- Example: Personal WhatsApp chat and a university group.

### 2. Text-Based Messages
- Support text messages up to 100,000 characters.
- Example: Sending both short and long messages.

### 3. Online/Offline Indicators
- Display whether a user is currently online or offline.
- Example: WhatsApp's online status indicator.

### 4. Multiple Device Support
- Allow users to access their accounts from multiple devices.
- Synchronize messages across all connected devices.
- Example: Using WhatsApp on a mobile phone and laptop.

### 5. Push Notifications
- Notify users about new messages even when the application is inactive.
- Example: Receiving a notification while playing a mobile game.

## 2. Scale

- The system must support 50 million Daily Active Users (DAU).
- It must handle a large number of connections, messages, requests, and notifications.
- The architecture must be scalable.
- Example: A university management system designed to handle 50,000 students instead of just 500.

## 3. Storage

- Store chat history permanently.
- Preserve message content, sender information, and timestamps.
- Allow users to access previous conversations.
- Example: Reading a message sent one year ago.
