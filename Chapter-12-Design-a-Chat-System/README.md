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



# Step 2: High-Level Design

## Communication Protocols

Communication protocols define the rules for exchanging data between clients and servers.

### 1. Sender Side — HTTP

- HTTP is used to send messages from the client to the server.
- Persistent connections allow multiple requests to reuse the same TCP connection.
- This reduces connection overhead and improves efficiency.

### 2. Receiver Side — Polling

- The client periodically sends requests to the server to check for new messages.
- If no messages are available, the server returns an empty response.
- Frequent requests create unnecessary network traffic and server load.
<img width="1179" height="860" alt="image" src="https://github.com/user-attachments/assets/227241d1-6158-44a8-8a2c-4d42398b20cc" />
<img width="1400" height="1004" alt="image" src="https://github.com/user-attachments/assets/10600b3c-54cc-405f-a60c-1fdd9966cd73" />
<img width="1194" height="876" alt="image" src="https://github.com/user-attachments/assets/900d7e9b-92e9-4ec1-8a01-22c3a1d7135a" />
<img width="800" height="486" alt="image" src="https://github.com/user-attachments/assets/0944717b-a5b9-430d-be9e-961f3eba5ba0" />
<img width="954" height="650" alt="image" src="https://github.com/user-attachments/assets/d95d7ac7-7bf4-4a21-bfd5-1f0730d6d390" />
<img width="681" height="435" alt="image" src="https://github.com/user-attachments/assets/0941a279-8ced-4278-ab5c-315a143ba243" />
<img width="618" height="418" alt="image" src="https://github.com/user-attachments/assets/f01caf6e-0c6b-4e24-8bc4-13b75a03945e" />


### 3. Long Polling

- The client sends a request, and the server keeps it open until a new message arrives or a timeout occurs.
- The server responds when a message becomes available.
- The client then sends another long-polling request.
- It reduces redundant requests but can consume resources for inactive users.
<img width="1400" height="1004" alt="image" src="https://github.com/user-attachments/assets/e9ebaf23-cd0e-4779-941b-4828929d743e" />
<img width="514" height="560" alt="image" src="https://github.com/user-attachments/assets/9efc37c1-fd91-4927-8506-6bc061f1340c" />
<img width="1400" height="791" alt="image" src="https://github.com/user-attachments/assets/375b59bb-e5a2-4b0e-8607-a23a74173544" />
<img width="959" height="647" alt="image" src="https://github.com/user-attachments/assets/ca8e3794-5433-4a11-9683-94f48e9cf9df" />
<img width="723" height="724" alt="image" src="https://github.com/user-attachments/assets/ceca564e-3d2b-45b6-b4f9-ad3d121462ad" />
<img width="618" height="418" alt="image" src="https://github.com/user-attachments/assets/3c60fe21-c2dd-4856-aac0-2e77804f4b80" />


### 4. WebSocket

- WebSocket provides a persistent, bi-directional connection between client and server.
- Both client and server can send messages independently.
- Messages can be delivered in real time without repeatedly creating HTTP requests.
- It is suitable for real-time chat applications.
<img width="800" height="480" alt="image" src="https://github.com/user-attachments/assets/d9c5a9c7-140d-4324-b4dc-1629ba97097d" />
<img width="1400" height="933" alt="image" src="https://github.com/user-attachments/assets/dde89ff9-d7fa-4a2e-b157-7dd748fde1a8" />
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/a1aef1ca-a310-498c-859b-ae4dcf723bc3" />
<img width="1200" height="628" alt="image" src="https://github.com/user-attachments/assets/32bbd38c-ee37-4ad9-bdc0-594c1aa4d850" />
<img width="1578" height="1396" alt="image" src="https://github.com/user-attachments/assets/749aa91b-7a59-45ed-abd1-7b02757ddd68" />
<img width="1376" height="768" alt="image" src="https://github.com/user-attachments/assets/5a28ba4e-d1a1-4c2d-8774-4323c093e9cc" />
<img width="814" height="354" alt="image" src="https://github.com/user-attachments/assets/0b4e9327-7a69-4e89-82ed-9a01d3a2d513" />

**Protocols:**
- `ws://` — WebSocket
- `wss://` — Secure WebSocket using TLS

WebSocket connections are initially established through an HTTP handshake and then upgraded to the WebSocket protocol.

## Summary

| Method | Communication | Limitation |
|---|---|---|
| HTTP | Client to server | Requires requests to send data |
| Polling | Repeated client requests | Unnecessary requests |
| Long Polling | Server responds when data arrives | Resource usage for inactive users |
| WebSocket | Bi-directional, persistent | Requires connection management |

## Daily Life Examples

- **HTTP:** Sending a letter to someone and waiting for their reply.
- **Polling:** Repeatedly checking your door to see if a guest has arrived.
- **Long Polling:** Waiting near the door until the guest arrives.
- **WebSocket:** Having a phone call where both people can talk and listen continuously.

**Key Takeaway:** WebSocket is preferred for real-time chat because it supports persistent, bi-directional communication between clients and servers.
