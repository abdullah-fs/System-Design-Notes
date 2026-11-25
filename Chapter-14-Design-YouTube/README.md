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
