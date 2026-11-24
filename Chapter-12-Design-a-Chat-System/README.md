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



# Components of Chat System

## 1. Stateless Services

- Handle user signup, login, and profile management.
- Use service discovery to identify a suitable chat server.
- Do not maintain users' active WebSocket connection state.
- Requests can be handled independently by different service instances.

## 2. Stateful Services

- Chat servers maintain persistent WebSocket connections.
- Responsible for sending and receiving messages.
- Manage message delivery and synchronization.
- Track active user connections.

## 3. Third-Party Integration

- External services provide additional functionality.
- Push notification services notify users about new messages.
- Notifications can be delivered when the app is inactive or closed.
- Examples: Firebase Cloud Messaging (FCM) and Apple Push Notification Service (APNs).

<img width="656" height="429" alt="image" src="https://github.com/user-attachments/assets/19e564ef-2dd3-451e-8910-957daedf8b06" />

## Daily Life Example

### WhatsApp Chat Example

1. A user opens WhatsApp and logs in. Stateless services handle authentication.
2. Service discovery identifies a suitable chat server for the user.
3. The user's device establishes a WebSocket connection with the chat server.
4. The chat server maintains the user's active connection.
5. When the user sends a message, the chat server processes and delivers it to the receiver.
6. If the receiver is online, the message is delivered through their active connection.
7. If the receiver is offline, the system stores the message for later delivery.
8. A push notification service can notify the receiver about the new message.
9. When the receiver reconnects, the chat system synchronizes and delivers pending messages.

**Key Takeaway:** Stateless services manage user-related operations, Stateful services maintain real-time chat connections, and Third-Party services handle push notifications.



# Chat System — Design
<img width="758" height="488" alt="image" src="https://github.com/user-attachments/assets/e9d24ad9-0469-4e8f-a9e0-0d82e207f7c8" />
<img width="1200" height="1029" alt="image" src="https://github.com/user-attachments/assets/5ce32596-c2f3-4aef-889c-8cada415dce0" />
<img width="1600" height="973" alt="image" src="https://github.com/user-attachments/assets/24ed5861-b4ed-475f-9171-f2131811a5d3" />
<img width="2000" height="1477" alt="image" src="https://github.com/user-attachments/assets/610000da-8da4-4c2e-bd4c-d30b1b62682d" />
<img width="1600" height="1388" alt="image" src="https://github.com/user-attachments/assets/e248b9e0-09d4-4053-92f1-bb2646b4a69f" />


## 1. Client-Server Communication

- The client maintains a persistent WebSocket connection with a chat server.
- WebSocket enables real-time, bi-directional communication.

## 2. Components

### Chat Servers
- Handle sending and receiving messages.
- Maintain WebSocket connections.
- Deliver messages to recipients.

### Presence Servers
- Manage users' online/offline status.
- Track active user connections.

### API Servers
- Handle signup, login, and profile management.
- Provide APIs for user information and chat history.

### Notification Servers
- Send push notifications for new messages.
- Notify users when they are offline or inactive.

### Key-Value Store
- Stores chat history and message data.
- Provides low-latency data access.
- Supports horizontal scaling.
- Handles large volumes of chat data efficiently.

## 3. Why Key-Value Store?

- **Horizontal Scaling:** Easily scale by adding more servers.
- **Low Latency:** Fast read and write operations.
- **Large Data Handling:** Suitable for storing large amounts of chat history.
- **Proven at Scale:** Distributed KV and wide-column databases are used in large messaging systems.

## 4. Data Models

### One-to-One Chat

- Messages are stored with a unique `message_id`.
- The primary key is `message_id`.
- Message IDs help identify messages and maintain their sequence.

### Group Chat

- Uses a composite primary key: `(channel_id, message_id)`.
- `channel_id` identifies the group or chat.
- `message_id` identifies a message within that channel.
- The same message ID can exist in different channels.

## 5. Message ID Generation

### Global Sequence Number

- Generates unique IDs across the entire system.
- Example: Snowflake 64-bit ID generator.
- Requires globally unique ID generation.

### Local Sequence Number

- Generates message IDs independently within each chat channel.
- IDs only need to be unique within the same channel.
- Simplifies message ordering and reduces dependency on a global generator.

## 6. Daily Life Example — WhatsApp

1. Abdullah logs into WhatsApp through API servers.
2. His client establishes a WebSocket connection with a chat server.
3. Abdullah sends a message to Ali.
4. The chat server assigns a message ID and stores the message in the KV store.
5. The presence server checks Ali's online/offline status.
6. If Ali is online, the message is delivered through his active WebSocket connection.
7. If Ali is offline, the message remains stored for later delivery.
8. The notification server sends a push notification to Ali.
9. When Ali reconnects, pending messages are synchronized.

**Key Takeaway:** Chat servers handle real-time messaging, presence servers manage user status, API servers handle user operations, notification servers send alerts, and KV stores efficiently store chat history.



# Step 3: Design Deep Dive

## 1. Service Discovery

- Recommends the best chat server for a client.
- Considers geographical location, server capacity, and current load.
- Uses Apache ZooKeeper to manage chat server information and assist server allocation.
- Ensures efficient load distribution and reduces latency.

## 2. Messaging Flows

### One-on-One Chat

1. User A sends a message to Chat Server 1.
2. Chat Server 1 assigns a unique message ID.
3. The message is stored in the key-value store.
4. If User B is online, the message is forwarded to Chat Server 2.
5. Chat Server 2 delivers the message through User B's WebSocket connection.
6. If User B is offline, a push notification can be sent.

### Group Chat

- Messages are copied into individual inboxes of group recipients.
- Each recipient maintains an inbox or message synchronization queue.
- Messages from different senders can be stored in the same recipient inbox.
- This simplifies message synchronization.
- However, copying messages to many recipients increases storage and write costs.

## 3. Message Synchronization

- Users may access their accounts from multiple devices.
- Each device maintains `cur_max_message_id`.
- This variable tracks the latest message ID received by the device.

A message is considered new when:

1. `recipient_id` matches the currently logged-in user ID.
2. `message_id` is greater than `cur_max_message_id`.

Devices fetch and synchronize new messages using these conditions.

## 4. Online Presence

### Heartbeat Mechanism

- Clients periodically send heartbeat signals to presence servers.
- Heartbeats indicate that the client is still connected.
- If no heartbeat is received within a specified timeout, the user is marked offline.
- Example: A timeout may be configured as 30 seconds.

### Fanout Model

- Presence updates are distributed using a publish-subscribe model.
- Each friend pair maintains a communication channel.
- When User A's status changes, the event is published to friends' channels.
- Friends receive the updated online/offline status through their subscribed channels.
- This approach is effective for small friend groups but becomes expensive for users with many contacts.

## Daily Life Example — WhatsApp

1. Abdullah opens WhatsApp and connects to a suitable chat server through service discovery.
2. Abdullah sends a message to Ali.
3. The chat server assigns a message ID and stores the message.
4. If Ali is online, the message is forwarded to his chat server and delivered through WebSocket.
5. If Ali is offline, the message remains available for later delivery and a push notification may be sent.
6. If Abdullah uses WhatsApp on multiple devices, each device synchronizes new messages using its latest message ID.
7. WhatsApp clients periodically send heartbeats to indicate they are connected.
8. When Abdullah comes online, his presence update is distributed to subscribed friends.

**Key Takeaway:** Service discovery selects chat servers, messaging flows deliver messages, synchronization keeps devices updated, heartbeat tracks connectivity, and fanout distributes presence updates.
