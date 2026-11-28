
# Chapter 15: Design Google Drive

## Introduction

Google Drive is a cloud-based file storage and synchronization service that allows users to store, access, and share files across multiple devices.

### Core Features

- File Upload and Download
- File Sync Across Devices
- File Sharing
- File Revision History
- Notifications for Edits, Deletes, and Shares
<img width="1280" height="720" alt="image" src="https://github.com/user-attachments/assets/d5c70b2f-1964-44bc-951b-8bb4e6d3ef42" />
<img width="692" height="690" alt="image" src="https://github.com/user-attachments/assets/d3bf62c5-4ac9-4345-8d32-54e7f699347b" />


## 1. File Upload and Download

- **Upload:** Transfer files from a user's device to cloud storage.
- **Download:** Retrieve files from cloud storage to a user's device.
- File metadata, such as filename, owner, and size, is stored in a database.
- Actual file content is stored in object storage.

**Example:** Uploading an assignment from a laptop and downloading it later on a mobile device.
<img width="1024" height="595" alt="image" src="https://github.com/user-attachments/assets/87897593-d283-481b-ad7b-684c4b89b1a3" />
<img width="2048" height="1669" alt="image" src="https://github.com/user-attachments/assets/b09813df-c9b6-4f6d-bc07-a0b1e588aa0b" />

## 2. File Sync Across Devices

File synchronization ensures that changes made to a file on one device are reflected on other devices.

- Detect file changes.
- Upload updated content to cloud storage.
- Notify other devices about the changes.
- Download the latest version when needed.

**Example:** Editing notes on a laptop and accessing the updated notes on a mobile phone.
<img width="800" height="533" alt="image" src="https://github.com/user-attachments/assets/fc4d4510-0703-4318-8504-40babd44cfd4" />

## 3. File Sharing

File sharing allows users to grant access to their files to other users.

Common permissions include:

- **Viewer:** Can view the file.
- **Commenter:** Can view and comment.
- **Editor:** Can modify the file.

The system stores sharing permissions and checks authorization whenever a user accesses a file.

**Example:** Sharing an assignment with a friend and giving them Viewer access.
<img width="640" height="360" alt="image" src="https://github.com/user-attachments/assets/3ecb370d-6f29-4da1-b96e-697a4f0cb2cb" />
<img width="1000" height="667" alt="image" src="https://github.com/user-attachments/assets/a19736ee-641a-4aef-ba51-65955f64665d" />

## 4. File Revision History

Revision history maintains previous versions of a file so users can recover earlier content.

- Each update creates a new revision or version.
- Version metadata is stored in the database.
- Previous file content can be preserved in object storage.
- Users can restore an earlier version when needed.

**Example:** Restoring yesterday's assignment after accidentally deleting an important paragraph.
<img width="701" height="466" alt="image" src="https://github.com/user-attachments/assets/4f0da1dc-6d1a-4982-a135-d361ba0527e8" />
<img width="850" height="531" alt="image" src="https://github.com/user-attachments/assets/c7af0d90-d59f-4a9b-aca0-5ced87c6aae8" />
<img width="507" height="416" alt="image" src="https://github.com/user-attachments/assets/a2d770ed-9559-4337-a260-e3c37e916e0e" />
<img width="509" height="481" alt="image" src="https://github.com/user-attachments/assets/4124a3ce-e5d4-40ea-8ec8-928e6f847950" />

## 5. Notifications

Notifications inform users about important changes to shared files.

Events may include:

- File edited
- File deleted
- File shared
- Permissions changed

A message queue can process notification events asynchronously, allowing file operations to complete without waiting for every notification.

**Example:** Receiving a notification when a friend edits a shared document.
<img width="750" height="375" alt="image" src="https://github.com/user-attachments/assets/7793923f-3c04-4f41-9cf7-def0fd858e52" />

## Key System Components

- **API Server:** Handles file requests and access control.
- **Metadata Database:** Stores file information, permissions, and versions.
- **Object Storage:** Stores actual file content.
- **Sync Service:** Keeps files updated across devices.
- **Message Queue:** Handles asynchronous events.
- **Notification Service:** Sends notifications to users.

