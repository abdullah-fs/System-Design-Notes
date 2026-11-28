
# Chapter 16: Proximity Service

## Step 1: Understanding the Problem and Establishing Scope

### Introduction

A **Proximity Service** helps users find nearby places or businesses based on their geographic location.

Examples include Google Maps and Yelp, where users can search for nearby restaurants, hotels, gas stations, and other businesses.

### Functional Requirements

1. Search for businesses based on user latitude, longitude, and search radius.
2. Allow business owners to add, update, or delete business information.
3. Provide detailed information about a business when requested.

### Non-Functional Requirements

- **Low Latency:** Search results should be returned quickly.
- **Data Privacy:** Protect user location and comply with privacy regulations such as GDPR and CCPA.
- **High Availability:** Keep the service available during peak hours and traffic spikes.

### Back-of-the-Envelope Estimation

| Metric | Estimate |
|---|---:|
| Daily Active Users (DAU) | 100 million |
| Total Businesses | 200 million |
| Searches per User per Day | 5 |
| Daily Searches | 500 million |

#### Search QPS Calculation

Daily searches:

\[
100M \times 5 = 500M
\]

Average Search QPS:

\[
\frac{500M}{86,400} \approx 5,787
\]

Approximately **5,000–5,800 search requests per second** on average.

QPS (Queries Per Second) represents the number of search requests handled by the system per second.

Note: Peak-hour traffic can be significantly higher than average QPS, so the system must be designed to handle traffic spikes.

