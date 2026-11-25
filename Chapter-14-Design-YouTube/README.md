# Chapter 14: Design YouTube

## Introduction

YouTube is a massive video streaming platform that allows users to upload, watch, and share videos.

This chapter focuses on designing a scalable video streaming system.

### Core Features

- **Fast Video Uploads:** Allow users to upload videos quickly.
- **Smooth Video Streaming:** Provide uninterrupted video playback with minimal buffering.
- **Video Quality Selection:** Allow users to choose different video resolutions (144p, 360p, 720p, 1080p).
- **Low Infrastructure Cost:** Minimize video storage and streaming costs.
- **High Availability and Reliability:** Keep the system available and reliable, even during failures.

### Key Statistics (2020)

- **2 Billion Monthly Active Users**
- **5 Billion Videos Watched Per Day**
- **37% of Mobile Internet Traffic** came from YouTube.
- **80 Languages** supported.
- **$15.1 Billion Ad Revenue** generated in 2019.

### Daily-Life Example

Imagine uploading a video to YouTube that users around the world can watch.

- Videos should upload quickly.
- Users should experience smooth playback.
- Users with slow internet should be able to select lower video quality.
- Millions of users should be able to watch videos simultaneously.

**Goal:** Design a scalable system that supports fast uploads, smooth streaming, and millions of users.



## Step 1: Understand the Problem and Scope

### Core Functionalities

1. **Upload Videos:** Users can upload videos to the platform.
2. **Watch Videos:** Users can stream and watch uploaded videos.

### Supported Platforms

- Mobile Applications
- Web Browsers
- Smart TVs

### Assumptions

- **Daily Active Users (DAU):** 5 million users.
- **Average Video Size:** 300 MB.
- **Upload Limit:** Maximum 1 GB per video.
- **Daily Storage Need:** 150 TB.

### CDN Cost Calculation

Assumptions:
- 5 million daily active users.
- Each user watches 5 videos per day.
- Average video size is 0.3 GB.
- CDN cost is $0.02 per GB.

Calculation:

```text
5,000,000 × 5 × 0.3 GB × $0.02

= $150,000/day
```

**Estimated Daily CDN Cost: $150,000**

### CDN (Content Delivery Network)

- Distributes video content across geographically distributed servers.
- Delivers videos from nearby servers.
- Reduces latency and improves streaming performance.
- Helps handle large amounts of video traffic.

### Daily-Life Example

Imagine a cinema platform where:

- Users upload their own videos.
- Other users watch those videos.
- The platform supports mobile phones, computers, and smart TVs.
- Millions of users watch videos every day.
- The platform needs significant storage and CDN resources to deliver videos smoothly.

**Goal:** Design a system that efficiently handles video uploads, video playback, storage, and large-scale traffic.



## Step 2: High-Level Design

### Main Components

1. **Client:** Devices such as smartphones, computers, and smart TVs used to access YouTube.
<img width="2048" height="2048" alt="image" src="https://github.com/user-attachments/assets/c47b7570-1d16-46f0-9bb5-07dff799fa97" />
2. **CDN (Content Delivery Network):** Stores and streams videos through geographically distributed edge servers.
<img width="1024" height="1024" alt="image" src="https://github.com/user-attachments/assets/b27f78b4-1e3a-4207-a2d2-449892ee5221" />
3. **API Servers:** Handle user interactions such as video uploads and metadata updates, excluding video streaming.
<img width="813" height="813" alt="image" src="https://github.com/user-attachments/assets/10b6d002-1916-4abb-bf45-8f54682e26d2" />
4. **Metadata Database:** Stores video information such as title, description, size, and format.
<img width="512" height="512" alt="image" src="https://github.com/user-attachments/assets/dbe9d754-7dd3-471e-a564-d665bac56b18" />
5. **Original Storage:** Blob/object storage that stores uploaded original videos.
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/268da338-f7f6-4b74-9def-8816569e38d7" />
6. **Transcoding Servers:** Convert videos into multiple resolutions and formats.
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/5a3a2d0d-67b7-4092-b39b-1a529b8cbd85" />
7. **Transcoded Storage:** Stores converted videos for CDN distribution.
<img width="2048" height="2048" alt="image" src="https://github.com/user-attachments/assets/526dbca9-f590-419e-a7c7-00753d1470ab" />

### Core Workflows

#### 1. Video Uploading Flow

**Parallel Processes:**
- Upload the original video to storage.
- Send a request to update video metadata.

**Video Upload Steps:**

1. Videos are uploaded to Original Storage.
2. Transcoding Servers convert videos into multiple formats and resolutions.
3. After transcoding completes, two processes execute in parallel:
   - **3a:** Transcoded videos are saved in Transcoded Storage.
   - **3b:** Transcoding completion events are added to the Completion Queue.
4. **3a.1:** Videos are distributed to the CDN.
5. **3b.1:** Completion Handlers process events, update metadata, and notify users.

**Metadata Upload Steps:**

- The client sends a metadata update request in parallel with the video upload.
- The request contains video information, including:
  - File name
  - File size
  - File format
  - Title
  - Description
- API Servers process the request and store metadata in the Metadata Database.

#### 2. Video Streaming Flow

1. The client requests video content from the CDN.
2. CDN edge servers deliver videos directly to users.
3. Nearby edge servers help minimize latency and buffering.
![Uploading image.png…]()

**Popular Streaming Protocols:**

- **MPEG-DASH:** Adaptive bitrate streaming protocol.
- **Apple HLS:** HTTP Live Streaming protocol.
- **Adobe HDS:** HTTP Dynamic Streaming protocol.

Different streaming protocols support different video encodings and playback players.

### Daily-Life Example

Imagine uploading a video to YouTube:

1. **Original Storage:** Your original video is saved.
2. **Transcoding Servers:** YouTube creates different video qualities, such as 360p, 720p, and 1080p.
3. **Transcoded Storage:** All converted versions are saved.
4. **Metadata Database:** Video title, description, and other details are stored.
5. **Completion Queue:** A notification event is created when transcoding finishes.
6. **CDN:** Video copies are distributed to servers around the world.
7. **Client:** Viewers watch the video from their phones, computers, or TVs.

**In short:** Upload → Store → Transcode → Distribute → Stream.



## Step 3: Design Deep Dive

### Video Transcoding

Video transcoding converts uploaded videos into different formats, resolutions, and bitrates for efficient storage and playback.

### 1. Importance of Video Transcoding

1. **Reduce Storage Space:** Compresses raw videos to reduce storage requirements.
2. **Device Compatibility:** Ensures videos work across different devices and browsers.
3. **Adapt to Network Conditions:** Provides different video qualities based on network speed.

### 2. Components

#### Container
- A file format that encapsulates video, audio, and metadata.
- Examples: MP4, AVI, MKV.

#### Codecs
- Compression and decompression algorithms for video and audio.
- Help reduce storage space and bandwidth usage.
- Examples: H.264, VP9.

**Difference:**
- Container packages video, audio, and metadata together.
- Codec compresses and decompresses video or audio.

### 3. Directed Acyclic Graph (DAG) Model

- Video transcoding is computationally expensive and time-consuming.
- DAG organizes video processing tasks into a dependency graph.
- Allows independent tasks to execute in parallel.
- Improves processing efficiency and reduces overall processing time.

#### Processing Tasks

The original video is split into video, audio, and metadata components.

**A. Video Encoding**
- Converts videos into different resolutions.
- Supports multiple codecs and bitrates.
- Example: 360p, 720p, and 1080p.

**B. Thumbnail Generation**
- Generates a thumbnail for the video.
- Users can upload their own thumbnails or the system can generate them automatically.

**C. Watermarking**
- Adds an image or logo overlay to the video.
- Can contain branding or identifying information.

### Daily-Life Example

Imagine uploading a video to YouTube.

- **Transcoding:** YouTube creates different quality versions of your video.
- **Container:** MP4 packages video, audio, and metadata together.
- **Codec:** H.264 compresses the video to reduce its size.
- **DAG:** Different processing tasks are organized and executed in parallel.
- **Thumbnail:** YouTube creates a preview image for your video.
- **Watermark:** A logo can be added as an overlay.

**In short:** Video Transcoding → Encoding → Thumbnail Generation → Watermarking → Ready for Playback.



# Video Transcoding Architecture

Video Transcoding Architecture processes uploaded videos by dividing them into smaller tasks, scheduling those tasks, allocating resources, and producing transcoded videos ready for distribution.

## 1. Preprocessor

The Preprocessor prepares videos for transcoding. It has four main responsibilities:

- **Video Splitting:** Divides videos into smaller chunks called Groups of Pictures (GOPs).
- **GOP Alignment:** Splits videos according to GOP boundaries to support older clients.
- **DAG Generation:** Generates a Directed Acyclic Graph (DAG) using configuration files written by client programmers. The DAG defines tasks and their dependencies.
- **Temporary Storage:** Stores GOPs and metadata temporarily so failed encoding tasks can be retried without repeating earlier processing.

## 2. DAG Scheduler

The DAG Scheduler organizes tasks into sequential or parallel stages.

- Divides the DAG into different stages.
- Places tasks into the Resource Manager's Task Queue.
- Ensures dependent tasks execute in the correct order.
- Allows independent tasks to run in parallel.

**Example Stages:**
- **Stage 1:** Processes video, audio, and metadata.
- **Stage 2:** Splits video processing into video encoding and thumbnail generation.

## 3. Resource Manager

The Resource Manager efficiently allocates system resources. It contains three queues and a Task Scheduler.

- **Task Queue:** A priority queue containing pending tasks.
- **Worker Queue:** A priority queue containing worker availability and utilization information.
- **Running Queue:** Tracks currently running tasks and the workers executing them.
- **Task Scheduler:** Selects suitable tasks and workers, then assigns tasks to workers.

## 4. Task Workers

Task Workers execute the assigned processing tasks.

Examples include:
- Video encoding
- Thumbnail generation
- Watermarking
- Audio processing

Different workers can perform different types of tasks.

## 5. Temporary Storage

Temporary Storage keeps intermediate processing data.

- Stores GOPs and metadata.
- Supports retry operations when processing fails.
- Storage selection depends on data type, size, access frequency, and lifespan.

## 6. Output

After all required tasks are completed, the system produces transcoded videos.

These videos are available in different resolutions and formats and are ready for distribution through the CDN.

## Complete Workflow

1. **Original Video:** The user uploads a video.
2. **Preprocessor:** Splits the video into GOPs and generates the DAG.
3. **DAG Scheduler:** Organizes tasks into processing stages.
4. **Resource Manager:** Assigns tasks to suitable workers.
5. **Task Workers:** Perform encoding, thumbnail generation, watermarking, and other operations.
6. **Temporary Storage:** Keeps intermediate data for retry operations.
7. **Output:** Produces transcoded videos ready for CDN distribution.

## Daily-Life Example

When a user uploads a video to YouTube:

1. The Preprocessor divides the video into smaller chunks.
2. The DAG Scheduler organizes the required processing tasks.
3. The Resource Manager assigns tasks to available workers.
4. Task Workers generate different video qualities and thumbnails.
5. Temporary Storage keeps intermediate data in case a task fails.
6. The final transcoded videos become ready for CDN distribution.

## Summary

- **Preprocessor:** Splits videos and generates DAGs.
- **DAG Scheduler:** Organizes tasks into stages.
- **Resource Manager:** Allocates tasks and workers.
- **Task Workers:** Execute processing tasks.
- **Temporary Storage:** Stores intermediate data for retries.
- **Output:** Produces videos ready for distribution.



# System Optimizations

System optimizations improve video upload speed, processing efficiency, security, and cost-effectiveness.

## 1. Speed Optimizations

### 1.1 Parallel Video Uploads
- Split videos into smaller chunks.
- Upload multiple chunks simultaneously.
- Improve upload speed.
- Support resumable uploads by retrying only failed chunks.

### 1.2 Distributed Upload Centers
- Use geographically distributed upload centers close to users.
- Allow users to upload videos through nearby locations.
- Reduce network latency and upload time.

### 1.3 Parallel Processing
- Decouple system modules using message queues.
- Allow independent modules to process tasks simultaneously.
- Improve scalability and processing efficiency.
- Prevent one busy module from blocking other independent tasks.

## 2. Safety Optimizations

### 2.1 Pre-Signed URLs
- Generate temporary URLs for authorized video uploads.
- Restrict uploads to users with valid permissions.
- Provide time-limited access to storage.
- Avoid exposing permanent storage credentials.

### 2.2 Protect Videos

Different techniques protect videos from unauthorized access and copying.

- **DRM (Digital Rights Management):** Controls access to protected video content. Examples include Apple FairPlay and Google Widevine.
- **AES Encryption:** Encrypts video data so it cannot be read without the appropriate decryption key.
- **Watermarking:** Adds logos or identifying information to videos to help identify their ownership or source.

## 3. Cost-Saving Optimizations

### 3.1 CDN for Popular Videos
- Serve popular videos through CDNs.
- Deliver less popular videos from high-capacity storage servers.
- Reduce CDN bandwidth costs.

### 3.2 On-Demand Encoding
- Encode rarely accessed videos only when needed.
- Avoid unnecessary encoding of videos that receive few views.
- Reduce processing and storage costs.

### 3.3 Regional Video Distribution
- Distribute videos across regions according to their popularity.
- Store frequently accessed regional videos closer to users.
- Reduce delivery latency and bandwidth costs.

### 3.4 Custom CDNs and ISP Partnerships
- Build custom CDN infrastructure.
- Partner with Internet Service Providers (ISPs).
- Reduce bandwidth and video delivery costs.

## 4. Error Handling

Error handling ensures that system failures are managed properly.

### 4.1 Recoverable Errors
These are temporary errors that may be resolved by retrying the operation.

Examples:
- Failed video uploads
- Failed transcoding tasks
- Resource allocation failures

**Solution:** Retry the failed operation.

### 4.2 Non-Recoverable Errors
These errors cannot normally be fixed by retrying because the input or request is invalid.

Examples:
- Corrupted video files
- Malformed video data
- Unsupported video formats

**Solution:** Stop processing and return an appropriate error code.

## Daily-Life Example

When a user uploads a video to YouTube:

1. Parallel uploads divide the video into chunks and upload them simultaneously.
2. Distributed upload centers reduce upload latency.
3. Message queues allow independent processing tasks to run in parallel.
4. Pre-signed URLs authorize secure uploads.
5. DRM, AES encryption, and watermarking help protect video content.
6. Popular videos are delivered through CDNs to reduce latency.
7. Rarely watched videos can use on-demand encoding to save resources.
8. Error handling retries temporary failures and stops invalid video processing.

## Summary

- **Speed:** Parallel uploads, distributed upload centers, and message queues.
- **Safety:** Pre-signed URLs, DRM, AES encryption, and watermarking.
- **Cost Saving:** CDN optimization, on-demand encoding, regional distribution, and ISP partnerships.
- **Error Handling:** Retry recoverable errors and stop non-recoverable errors.
