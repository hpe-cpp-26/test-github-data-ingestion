# Troubleshooting Guide: Travel Planner System

This document provides solutions for common issues encountered by administrators, developers, and support teams working with the Travel Planner System.

---

## 🛠️ System Overview & Quick Checks

Before diving into specific issues, always verify the basics:
* **Service Status:** Ensure the Frontend, Backend API, and Database are running.
* **Network:** Verify the server has outbound internet access (required for external Travel APIs).
* **Logs:** Check the centralized log management system (e.g., Datadog, ELK) or local container logs:
    ```bash
    docker compose logs -f api
    ```

---

## 🔍 Common Issues & Resolutions

### 1. External API Failures (Flight, Hotel, or Map Services)
**Symptoms:** Users see "No flights found" or map widgets fail to load, despite entering valid search criteria.

| Potential Cause | Validation | Resolution |
| :--- | :--- | :--- |
| **API Key Expired/Invalid** | Check backend logs for `401 Unauthorized` or `403 Forbidden` from third-party endpoints (Amadeus, Google Maps, etc.). | Update the respective API keys in your environment variables/secret manager and restart the service. |
| **Rate Limiting (Throttling)** | Look for `429 Too Many Requests` in the logs. | Upgrade the API tier or implement aggressive caching (Redis) for high-traffic search queries. |
| **Provider Downtime** | Execute a direct `curl` request to the provider's health endpoint. | Check the external provider's status page. If down, notify users via an in-app banner. |

---

### 2. Itinerary Generation & AI Errors
**Symptoms:** The "Auto-Generate Itinerary" feature spins indefinitely or returns a generic error.

> ⚠️ **Note:** Itinerary generation relies heavily on asynchronous background workers.

* **Cause A: Celery/Redis Worker Crash**
    * *How to check:* Check if the background task queue is backed up.
    * *Fix:* Restart the worker service:
        ```bash
        docker compose restart background-worker
        ```
* **Cause B: LLM/AI Timeout**
    * *How to check:* Look for `Gateway Timeout (504)` or read timeouts in the AI orchestration service.
    * *Fix:* Increase the timeout threshold in `config.py` to 30 seconds, or implement a retry mechanism with exponential backoff.

---

### 3. Database & Synchronization Issues
**Symptoms:** Users report that shared itineraries are not updating in real-time for other collaborators.

* **Cause A: WebSocket Disconnection**
    * *Fix:* Inspect the browser console for connection drops. Ensure your reverse proxy (Nginx/HAProxy) is configured to allow WebSocket upgrading:
        ```nginx
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        ```
* **Cause B: Database Deadlocks during concurrent edits**
    * *Fix:* Check the Postgres/MySQL logs for deadlock detections. Optimize the application layer to use optimistic locking via a `version` column on the `Itinerary` model.

---

### 4. Authentication & Session Failures
**Symptoms:** Users are repeatedly kicked out of their sessions or cannot log in via OAuth (Google/Apple).

* **JWT Token Expiration:** If users are logged out mid-planning, verify the `JWT_EXPIRATION_DELTA` setting. Consider implementing refresh tokens.
* **Time Desynchronization:** If the server clock drifts from the OAuth provider's clock, authentication will fail. Synchronize the host clock:
    ```bash
    sudo ntpdate pool.ntp.org
    ```

---

## 📈 Performance Troubleshooting

### High Latency on Route Optimization
If the system takes too long to calculate the optimal route between multiple attractions:
1.  **Check the Matrix Limit:** Ensure the frontend restricts users from adding more than 25 waypoints to a single day's itinerary.
2.  **Enable Geocoding Cache:** Ensure that frequently visited landmarks (e.g., "Eiffel Tower") are cached in Redis to prevent redundant external geocoding API calls.

---

## 📞 Escalation Path

If the issue persists after following this guide:
1.  **Internal Slack:** Post the error trace in `#travel-planner-infra`.
2.  **On-Call:** Contact the DevOps on-call engineer via PagerDuty if production is completely degraded.
3.  **Template:** When opening an issue, include:
    * Steps to reproduce
    * Relevant Request/Response payloads
    * Correlated `X-Request-ID` from the logs
    
