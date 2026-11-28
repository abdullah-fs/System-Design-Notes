
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




## Step 1: Understanding the Problem

### Functional Requirements

Functional requirements define what the system should do.

- **File Upload and Download:** Users can upload and download files.
- **File Sync:** Keep files synchronized across multiple devices.
- **Revision History:** Maintain previous versions of files.
- **File Sharing:** Allow users to share files with different permissions.
- **Notifications:** Notify users about file edits, deletions, and shares.

### Non-Functional Requirements

Non-functional requirements define how well the system should operate.

- **Reliability:** Prevent data loss and ensure files are safely stored.
- **Fast Sync Speed:** Synchronize file changes quickly across devices.
- **Bandwidth Efficiency:** Minimize unnecessary data transfers.
- **Scalability:** Support 10 million daily active users (DAU).
- **High Availability:** Keep the service accessible during server failures.

### Constraints and Assumptions

| Constraint | Value |
|---|---|
| Free Storage per User | 10 GB |
| Maximum File Size | 10 GB |
| Average Upload Size | 500 KB |
| Upload Frequency | 2 files per user per day |
| Total Storage Requirement | 500 PB |

### Key Design Considerations

- Large files may be uploaded in smaller chunks.
- File changes should synchronize efficiently across devices.
- Distributed storage can help handle large storage requirements.
- Replication and redundancy can improve reliability and availability.
- The system should scale to support millions of users.

**Note:** The 500 PB storage requirement is a given assumption. Calculating it requires additional information, such as total users, average storage usage, and data retention period.




## Step 2: High-Level Design

### 1. Single-Server Setup

A basic system consists of:

- **Web Server:** Handles file upload and download requests.
- **Metadata Database:** Stores user information, file names, paths, and other metadata.
- **Storage Directory:** Stores actual files organized by user namespaces.

#### Namespaces

- A root directory `drive/` contains separate directories for each user.
- Each namespace stores files belonging to a specific user.
- A file can be identified using its namespace and relative path.

Example:

```text
drive/
  user101/
    assignment.pdf
    notes.docx
  user202/
    photo.jpg
```

**Limitation:** A single server cannot efficiently handle unlimited storage and traffic. It also creates a single point of failure.

### 2. APIs

#### File Upload

- **Simple Upload:** Used for small files uploaded in a single request.
- **Resumable Upload:** Used for large files that may require multiple requests.

Resumable upload process:

1. Client initiates an upload session.
2. Server returns a resumable URL.
3. Client uploads file data.
4. Interrupted uploads can resume from the last uploaded position.

#### File Download

Allows users to retrieve files from cloud storage after authorization.

#### Get File Revisions

Returns the previous versions of a file.

### 3. Moving to Distributed Systems

#### Sharding

- Divides data across multiple servers.
- Storage can be partitioned based on `user_id`.

#### Cloud Storage

- Stores actual files in scalable object storage, such as Amazon S3.
- Supports redundancy and cross-region replication.

#### Load Balancer

Distributes incoming requests across multiple API servers to prevent overload.

#### Metadata Database Replication

Maintains database replicas to improve availability and reliability.

### 4. Sync Conflicts

A sync conflict occurs when multiple users modify the same file simultaneously.

- The first update is accepted by the server.
- The second update may conflict with the latest server version.
- The system preserves conflicting copies.
- Users can merge changes or choose which version to keep.
<img width="1616" height="508" alt="image" src="https://github.com/user-attachments/assets/c1b3f140-6bfa-4400-a1d5-358c3ceb3577" />

### 5. Improved High-Level Design
<img width="800" height="549" alt="image" src="https://github.com/user-attachments/assets/df231d31-837e-41f4-947f-5475261db518" />

#### Block Servers

- Split files into smaller blocks.
- Maximum block size: **4 MB**.
- Assign each block a unique hash.
- Store blocks independently.
- Reconstruct files by combining blocks in the correct order.

#### Cloud Storage

Stores file blocks with scalable and redundant storage.

#### Cold Storage

Moves inactive files to cheaper storage to reduce storage costs.

#### API Servers

Handle authentication, user profiles, file metadata, and other non-upload workflows.

#### Metadata Database and Cache

- Stores metadata for users, files, blocks, and versions.
- Cache frequently accessed metadata for faster retrieval.

#### Notification Service

Uses a publisher/subscriber system to notify clients about file changes, such as additions, edits, and deletions.

#### Offline Backup Queue

Stores file change information for offline clients so they can synchronize when they reconnect.

### Key Design Benefits

- **Scalability:** Distributes storage and traffic across multiple servers.
- **Reliability:** Replication helps protect against server failures.
- **Bandwidth Efficiency:** Block-based uploads can reduce unnecessary data transfer.
- **Availability:** Load balancing and database replication help keep the service accessible.
- **Cost Efficiency:** Cold storage reduces the cost of storing inactive files.




## Step 3: Design Deep Dive

### 1. Metadata Database

The metadata database stores information about users, files, blocks, and file versions.

#### Main Tables

- **User Table:** Stores user profiles and preferences.
- **File Table:** Stores file metadata such as name, size, owner, and path.
- **Block Table:** Tracks file blocks and their storage locations.
- **File Version Table:** Maintains file revision history.

Actual file content is stored in cloud storage, while metadata is stored in the database.

### 2. File Upload Flow

1. **File Upload:** Files are split into blocks, compressed, and encrypted by the block server.
2. **Block Storage:** Blocks are uploaded to cloud storage such as Amazon S3.
3. **Metadata Upload:** Client sends file metadata to the API server.
4. **Pending Status:** Metadata is stored with status `pending`.
5. **Completion:** After successful block storage, file status changes to `uploaded`.
6. **Notification:** Relevant users are notified about the file update.

### 3. File Sync

#### Delta Sync

Transfers only modified blocks instead of uploading the entire file again.

#### Compression

Reduces block size to save bandwidth. Compression efficiency depends on the file type.

#### Conflict Resolution

- The first processed update is accepted.
- Conflicting versions are preserved separately.
- Users can merge changes or choose a version.

### 4. File Download Flow

1. **Trigger:** Notification service informs online clients about file changes.
2. **Metadata Fetch:** Client retrieves updated metadata through API servers.
3. **Block Download:** Client downloads required blocks.
4. **File Reconstruction:** Blocks are combined in the correct order.

Offline clients retrieve pending changes when they reconnect.

### 5. Notification Service

- Keeps clients informed about file changes.
- Uses long polling for asynchronous notifications.
- Notifies relevant clients about file additions, edits, and deletions.

### 6. Storage Optimization

#### De-duplication

Identifies duplicate blocks using hash-based comparisons to reduce storage usage.

#### Versioning Strategy

Limits or manages saved revisions to control storage costs while preserving useful versions.

#### Cold Storage

Moves rarely accessed files to cheaper storage, such as Amazon S3 Glacier.

### 7. Failure Handling

- **Load Balancer Failure:** Activate a secondary load balancer.
- **Block Server Failure:** Reassign pending tasks to healthy servers.
- **Metadata Database Failure:** Promote a replica to primary and redirect traffic.
- **Cloud Storage Failure:** Retrieve data from replicated storage in another region.
- **Notification Service Failure:** Clients reconnect to alternative servers.

### Key Design Concepts

- Metadata and actual file content are stored separately.
- Block-based storage supports efficient file transfer.
- Delta sync reduces unnecessary bandwidth usage.
- Notifications keep clients synchronized.
- Replication improves reliability and availability.
- De-duplication and cold storage reduce storage costs.
