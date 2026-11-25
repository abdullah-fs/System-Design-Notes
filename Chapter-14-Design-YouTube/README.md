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
