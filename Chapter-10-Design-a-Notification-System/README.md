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
![Uploading image.png…]()


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
