
# Chapter 17: Nearby Friends

## Introduction

Nearby Friends is a system that allows users to share their locations and discover friends who are nearby.

### Key Concept

The main challenge is handling continuously changing user locations while providing low-latency nearby friend searches.

### Proximity Service vs Nearby Friends

| Proximity Service | Nearby Friends |
|---|---|
| Finds nearby businesses. | Finds nearby friends. |
| Business locations are mostly static. | User locations change frequently. |
| Location updates are less frequent. | Requires frequent location updates. |
| Example: Nearby restaurants. | Example: Live friend location sharing. |

### Key Challenges

- Handling frequent location updates.
- Tracking users' latest locations.
- Efficiently finding nearby friends.
- Maintaining low-latency responses.
- Scaling with a large number of active users.

