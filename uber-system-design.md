# System Design Document: Uber (Core Ride-Hailing Platform)

## 1. Executive Summary & Scope
This document outlines the functional requirements, core workflows, API designs, and data entities for a scalable, real-time ride-hailing platform. 

### In Scope
*   **Rider Operations:** Searching for rides, fare estimation, requesting a ride, and tracking a driver in real-time.
*   **Driver Operations:** Accepting/rejecting ride requests, updating availability status, and broadcasting real-time location.
*   **Core Matching Engine:** Pairing an available driver with a rider based on proximity.

### Out of Scope
*   Payment processing gateways, rating systems, surge pricing algorithms, UberEats, and physical deployment architecture (no databases, microservices, load balancers, or streaming clusters are defined here).

---

## 2. Core Functional Requirements

### 2.1 Rider Requirements
*   **Get Fare Estimate:** Riders can input a pickup and drop-off location to receive an upfront price estimate and ETA.
*   **Book a Ride:** Riders can request a trip. The system will hold the request until a driver accepts or it times out.
*   **Live Tracking:** Riders can view the real-time location updates of their assigned driver.

### 2.2 Driver Requirements
*   **Go Online/Offline:** Drivers can toggle their availability status.
*   **Location Ping:** Active drivers automatically broadcast their precise GPS coordinates every 4 seconds.
*   **Accept/Reject Trips:** Drivers receive a flash offer for a nearby ride and have 15 seconds to accept or decline.

---

## 3. Core Entities & Data Model

### 3.1 Entities
*   **User / Rider:** Contains profile information, saved locations, and baseline metrics.
*   **Driver:** Contains license data, vehicle details, and current online status.
*   **Trip:** Tracks the lifecycle of a ride (Requested $\rightarrow$ Accepted $\rightarrow$ Arrived $\rightarrow$ In-Progress $\rightarrow$ Completed).
*   **Driver Location History:** A time-series log of GPS coordinates used for routing and audit trails.

### 3.2 Simplified Schema Concept
