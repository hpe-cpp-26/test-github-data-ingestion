# System Design: Scalable High-Throughput Payment System (Stripe-Like)

This document outlines the design for a highly efficient, reliable, and fault-tolerant payment processing system capable of handling thousands of transactions per second (TPS) with exactly-once processing guarantees.

---

## 1. System Requirements

### Functional Requirements

* **Charge Management:** Support credit/debit card processing, digital wallets (Apple Pay, Google Pay), and alternative payment methods.
* **Idempotency:** Guarantee that a payment request is processed exactly once, regardless of network retries.
* **Ledger Logging:** Maintain an immutable, double-entry bookkeeping ledger tracking every cent moved.
* **Webhook & Notifications:** Real-time event notification system for merchants regarding payment states (`payment.succeeded`, `payment.failed`).

### Non-Functional Requirements

* **High Availability & Fault Tolerance:** 99.999% uptime ($5\text{ nines}$). The system must not drop payments during infrastructure failures.
* **Low Latency:** Critical path operations (charging a card) must respond under 500ms.
* **Strict Consistency:** Financial balances and ledger records must be strictly consistent; eventual consistency is unacceptable for account balances.
* **PCI-DSS Compliance:** Secure handling of cardholder data using tokenization.

---

## 2. Core Components & Responsibilities

* **API Gateway:** Handles rate limiting, authentication, SSL termination, and initial request routing.
* **Tokenization Service (Vault):** A highly secured, decoupled microservice that swaps raw credit card data (PAN) for a secure token. This keeps the rest of the ecosystem outside of strict PCI-DSS scope.
* **Payment API Service:** Orchestrates the lifecycle of a payment intent.
* **Idempotency Engine:** Uses a fast, distributed cache (Redis) paired with the core database to prevent duplicate transactions.
* **Payment Executor:** Communicates with third-party payment gateways/acquiring banks.
* **Distributed Ledger:** An append-only, transactionally isolated ledger recording state and balance movements.
* **Kafka Event Bus:** Publishes payment status updates to downstream decoupled services.

---

## 3. Deep Dives & Low-Level Design

### Exactly-Once Processing (Idempotency Protocol)

To prevent double-charging due to network timeouts, clients must submit an `Idempotency-Key` header with every mutating request.

1. **Locking Phase:** On receiving a request, the system attempts to acquire a distributed lock in Redis using the `Idempotency-Key`.
2. **Status Evaluation:** * If the key exists and the status is **In-Flight**, return a `409 Conflict`.
* If the key exists and the status is **Completed**, instantly return the cached response without calling the gateway.
* If the key does not exist, insert it into the database with a status of **Started** inside a database transaction.


3. **Execution Phase:** The API calls the external Payment Gateway.
4. **Completion Phase:** The database record is updated to **Success** or **Failed**, along with the payload response. The Redis lock is released.

### Double-Entry Ledger Schema

> **Rule of Consistency:** For any `transaction_id`, the sum of all `amount` values in `ledger_entries` must equal exactly `0`.
> 
> $$\sum \text{amount} = 0$$
> 
> 

---

## 4. High-Performance Techniques

### Database Strategy: Sharding and Partitioning

* **Storage Engine:** Relational databases (PostgreSQL or NewSQL like CockroachDB) are used for the ledger and API state due to strict ACID requirements.
* **Sharding Key:** Data is horizontally sharded by `merchant_id` or `account_id`. This isolates cross-merchant traffic and scales writes linearly.
* **Time-Series Partitioning:** The `ledger_entries` and `ledger_transactions` tables are partitioned by month/day. Old partitions can be marked read-only or archived to cold storage.

### Connection Management & Gateway Timeouts

Calls to external acquiring banks have erratic latencies.

* **Circuit Breakers:** If an external gateway starts throwing `5xx` errors or experiences latency $> 2000\text{ms}$, the system trips a circuit breaker and routes fallback traffic to an alternative gateway.
* **Dedicated Thread Pools:** Payment executor clients utilize separate, isolated thread pools per gateway. A slowdown in Gateway A will not starve connection threads processing payments for Gateway B.

---

## 5. Failure Modes & Resilience (Reconciliation Loop)

In financial systems, data can drift due to network failures, sudden gateway dropouts, and batch settlement delays. A continuous **Reconciliation Loop** runs out-of-band to guarantee data integrity.

1. **Daily Settlement Files:** Every night, payment gateways provide a CSV/EPX file containing all transactions settled on their end.
2. **Reconciliation Engine:** A batch job (e.g., running on Apache Spark or a distributed worker group) pairs internal ledger entries with the external settlement file using the gateway transaction reference ID.
3. **Mismatches Strategy:**
* **Missing Internally:** The system executes a "chargeback" or "refund" flow, or alerts engineers of untracked revenue.
* **Missing Externally:** The payment executor initiates a status-check API call to force-update the state, or alerts the merchant team of a stuck transaction.
