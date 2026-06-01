# Instagram System Design

## 1. System Overview

The Instagram feed system is a high-scale, read-heavy service designed to deliver personalized content. The primary goal is to provide a low-latency, reliable, and eventually consistent feed retrieval experience for users while managing massive write volumes and the "celebrity fan-out" problem.

## 2. Requirements

### 2.1 Functional Requirements

* **Feed Retrieval:** View a personalized list of posts from followed users.
* **Content Creation:** Users can upload media (photos/videos) with metadata.
* **Pagination:** Support cursor-based pagination for endless scrolling.

### 2.2 Non-Functional Requirements

* **Latency:** P99 feed retrieval latency under 200ms.
* **Availability:** High availability for feed discovery (99.99%).
* **Consistency:** Eventual consistency is acceptable; new posts should appear within seconds.
* **Scalability:** Support 500 Million+ Daily Active Users (DAU).

## 3. Core Architecture

The system uses a microservices approach to decouple the write path from the read path.

* **API Gateway:** Handles authentication, rate limiting, and request routing.
* **Post Service:** Manages media ingestion, database entry, and event broadcasting to a message queue (e.g., Kafka).
* **Fan-out Workers:** Asynchronously processes posts to update the feed caches of active followers.
* **Feed Service:** Aggregates data from Redis caches and the database to assemble the final response.

## 4. Data Modeling

* **Post Table:** Sharded by `user_id` to keep a creator's posts on the same physical database node.
* **Follow Table:** An adjacency list storing relationship mappings (`follower_id` -> `followee_id`).
* **Media Storage:** All raw media is stored in Object Storage (e.g., S3) and served via a global CDN.

## 5. Feed Generation Strategy

To handle massive scale, a **Hybrid Fan-out** model is used:

1. **Push Model (for standard users):** When a user posts, the system pushes the `post_id` into the Redis timeline cache of all their active followers. This ensures $O(1)$ read performance.
2. **Pull Model (for celebrity/high-influence accounts):** To avoid the "fan-out bottleneck," posts from accounts with millions of followers are *not* pushed. Instead, the feed service fetches these posts at runtime when a user requests their feed.
3. **Merging:** The Feed Service performs a multi-way merge sort of the pre-computed Redis feed and the dynamically fetched celebrity posts.

## 6. Caching & Optimization

* **Redis ZSET:** Used to store timelines in memory, with the post timestamp as the score for efficient sorting.
* **Cache Pruning:** Caches are capped (e.g., 500 posts) and only maintained for active users (those who logged in recently).
* **CDN:** All media is served via edge locations using signed URLs to ensure security and reduce origin server load.
* **Cache Stampede Mitigation:** Uses distributed mutex locks to prevent database overload during cache misses for popular content.
