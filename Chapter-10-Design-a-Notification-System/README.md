## Introduction

A **Notification System** delivers timely updates, alerts, and information to users through different channels.

### Types of Notifications

1. **Push Notifications:** Alerts sent to mobile or desktop devices.
2. **SMS:** Text messages sent to users' mobile numbers.
3. **Emails:** Messages sent to users' email addresses.

### Example

When a user places an order on an e-commerce website:
- Email: Order confirmation.
- Push Notification: Order shipped.
- SMS: Delivery confirmation.
<img width="2000" height="757" alt="image" src="https://github.com/user-attachments/assets/bf7a275d-5e69-4ab1-b882-828958c8a1f4" />


### Goal

Design a scalable and reliable system capable of sending millions of notifications daily.



## Step 1: Understanding the Problem

### Requirements

1. **Notification Types:** Support Push Notifications, SMS, and Emails.

2. **Delivery:** Soft real-time delivery with minimal delays.

3. **Platforms:** Support iOS, Android, and Desktop.

4. **Triggers:** Notifications can be triggered by client applications or scheduled on servers.

5. **Scale:** Handle millions of notifications daily:
   - Push Notifications: 10 million/day.
   - SMS: 1 million/day.
   - Emails: 5 million/day.

6. **Opt-out Support:** Allow users to disable specific notification types through their preferences.



## Step 2: High-Level Design

### 1. Notification Types

- **APNS:** Sends push notifications to iOS devices.
- **FCM:** Sends push notifications to Android devices.
- **Twilio / Nexmo:** Sends SMS messages.
- **SendGrid / Mailchimp:** Sends emails.

### 2. Contact Info Gathering
<img width="1400" height="826" alt="image" src="https://github.com/user-attachments/assets/1aad72f2-6ad3-4330-b7c4-137b5820e6e6" />

- Collect device tokens during app installation.
- Collect email addresses and phone numbers during signup.
- Store contact information in the database:
  - **Device Tokens Table:** Stores device tokens.
  - **User Table:** Stores email addresses and phone numbers.

### 3. Notification Sending Flow

**A. Trigger Services**
- Generate notification events.
- Examples: Billing reminders, shipping updates, and scheduled notifications.
- Can be microservices, cron jobs, or distributed systems.

**B. Notification Server**
- Provides APIs to receive notification requests.
- Validates emails and phone numbers.
- Fetches required data from database or cache.
- Prepares notification content.

**C. Third-Party Services**
<img width="1210" height="812" alt="image" src="https://github.com/user-attachments/assets/19e5f4f3-e3bd-46e2-9799-a83cf4911598" />

- Deliver notifications to users.
- Examples: APNS, FCM, Twilio, and SendGrid.

### 4. Challenges in Initial Design

1. **Single Point of Failure (SPOF):** A single server failure can stop the entire notification system.
2. **Scalability Issues:** Difficult to scale databases, caches, and processing components independently.
3. **Performance Bottlenecks:** High notification volumes can overload the server.

### 5. Improved Design
![Uploading image.png…]()


- **Separate Database and Cache:** Move databases and caches outside notification servers.
- **Horizontal Scaling:** Use multiple notification servers to handle increased traffic.
- **Message Queues:** Buffer notification requests and decouple system components.
- **Workers:** Pull events from queues and send notifications through appropriate third-party services.

### Daily Life Example: Courier Company
![Uploading image.png…]()


Imagine a courier company handling 10,000 parcels daily:

| System Component | Courier Company Example |
|---|---|
| Trigger Services | Customer places an order |
| Notification Server | Courier office receives instructions |
| Database | Stores customer addresses and phone numbers |
| Message Queue | Waiting line for pending parcels |
| Workers | Delivery staff |
| Third-Party Services | SMS, Email, and Push providers |

**Flow:**
1. Customer places an order.
2. Notification server receives the request.
3. Request is added to the message queue.
4. Workers pick requests from the queue.
5. Notifications are delivered through third-party services.
