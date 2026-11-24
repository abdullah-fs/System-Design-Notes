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
<img width="1210" height="812" alt="image" src="https://github.com/user-attachments/assets/167bc8b9-c7d4-43c9-b4d0-5913287b5acf" />


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



## Step 3: Design Deep Dive

### 1. Reliability

Reliability ensures notifications are not lost or unnecessarily duplicated, even when failures occur.

**A. Prevent Data Loss**
- Store notification data in a database.
- Maintain a Notification Log Database.
- Implement a retry mechanism for failed notifications.

**Example:** A courier company saves every parcel record and reschedules delivery if an attempt fails.

**B. Deduplication**
- Assign a unique Event ID to every notification.
- Check whether the Event ID has already been processed.
- Discard duplicate events to prevent duplicate notifications.

**Example:** A courier company checks the parcel ID to avoid processing the same delivery order twice.

### 2. Additional Components

**A. Notification Templates**
- Use preformatted templates for consistent notifications.
- Insert dynamic data such as user names and order IDs.

**Example:** A courier company uses the same delivery message template and changes only the customer's name and parcel ID.

**B. Notification Settings**
- Allow users to opt-in or opt-out of specific notification channels.
- Support Push, SMS, and Email preferences.
- Store preferences in a dedicated Notification Settings Table.

**Example:** A user disables SMS notifications but keeps email notifications enabled.

**C. Rate Limiting**
- Limit the frequency of notifications sent to users.
- Prevent unnecessary notifications and spam.

**Example:** A courier company limits repeated delivery messages instead of sending an SMS every five minutes.

**D. Retry Mechanism**
- Retry sending notifications when third-party services fail.
- Handle temporary delivery failures.

**Example:** A courier company attempts delivery again if the customer is unavailable.

**E. Monitoring Queues**
- Monitor pending notifications in message queues.
- Add more workers when queue traffic increases.
- Maintain efficient notification processing.

**Example:** A courier company hires additional delivery workers when the number of pending parcels increases.

**F. Event Tracking**
- Track notification performance and user engagement.
- Important metrics:
  - **Open Rate:** Percentage of users who open notifications.
  - **Click Rate:** Percentage of users who click notifications.
  - **Engagement:** How users interact with notifications.

**Example:** A courier company tracks how many customers open SMS messages and click tracking links.

### 3. Security

**AppKey and AppSecret**
- Authenticate and secure notification APIs.
- Verify authorized applications.
- Prevent unauthorized access.

**Example:** A courier company allows only authorized staff with valid ID cards and access codes to enter its office.

### 4. Updated Notification Flow
![Uploading image.png…]()


1. **Trigger Services:** Call APIs to initiate notifications.

   **Example:** A customer places an order.

2. **Notification Servers:** Validate requests and fetch metadata from caches or databases.

   **Example:** The courier office checks the customer's address and phone number.

3. **Message Queues:** Store notification events for processing.

   **Example:** Pending parcels are placed in a waiting area.

4. **Workers:** Retrieve events from queues and interact with third-party services.

   **Example:** Delivery staff collect parcels for dispatch.

5. **Third-Party Services:** Deliver notifications to users through Push, SMS, or Email.

   **Example:** The courier company sends a delivery update through SMS or email.



   ## Key Optimizations

### 1. Horizontal Scaling
- Add multiple notification servers to distribute incoming requests.
- Reduce load on individual servers.
- Add more servers when traffic increases.

**Example:** A courier company opens multiple offices to distribute customer traffic instead of relying on one office.

### 2. Message Queues
- Decouple notification processing from application services.
- Temporarily store notification requests in queues.
- Workers retrieve and process messages.
- Handle high notification volumes efficiently.

**Example:** A restaurant maintains an order queue so chefs can prepare orders systematically.

### 3. Caching
- Store frequently accessed data in cache.
- Reduce database queries.
- Minimize latency and improve performance.

**Example:** A courier office keeps frequently used customer addresses readily available instead of checking the main register every time.

### 4. Geographical Distribution
- Distribute notification processing across different geographical regions.
- Use nearby servers to reduce network latency.
- Improve notification delivery performance.

**Example:** A courier company opens regional offices in different cities to deliver parcels faster.
