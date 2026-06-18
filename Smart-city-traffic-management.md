## Smart City Traffic Management Platform (SCTMP)

**Document ID:** SCTMP-RS-001  
**Version:** 1.0  
**Status:** Approved  
**Authors:** Kavya Reddy, James Okonkwo (urbantech.io)  
**Reviewed By:** Mei Lin Tan, Soren Bjelke  
**Last Updated:** 2026-06-01  

---

## 1. Introduction

SCTMP is a centralised software platform that ingests real-time data from road-side sensors, traffic cameras, connected vehicles, and pedestrian counters to dynamically manage traffic signal timings, detect incidents, and provide routing recommendations to city operators and commuters. This document covers both functional requirements (what the system must do) and non-functional requirements (the quality attributes it must satisfy).

**Primary users:** City Traffic Control Operators, Field Maintenance Engineers, Municipal Planners, and Public Commuters (via third-party navigation apps).

| Term | Definition |
|------|------------|
| RSU | Road Side Unit — edge device mounted at intersections collecting sensor data |
| Signal Phase | Current state of a traffic signal (green, amber, red) and its configured duration |
| Incident | Any detected anomaly: accident, stalled vehicle, road obstruction, or abnormal congestion |
| Adaptive Cycle | Dynamically adjusted signal timing computed in response to real-time traffic density |
| V2I | Vehicle-to-Infrastructure communication protocol |

---

## 2. User Management & Authentication

**FR-UM-001 — Role-Based Access Control:** The system shall support four roles: `operator`, `engineer`, `planner`, and `admin`, each with a distinct permission set.

**FR-UM-002 — SSO Integration:** The system shall integrate with the city's Active Directory via SAML 2.0 so staff log in with city-issued credentials.

**FR-UM-003 — MFA:** Accounts with `operator` or `admin` roles shall require MFA at login, supporting TOTP authenticator apps and SMS fallback.

**FR-UM-004 — Session Management:** Idle sessions shall be terminated after 30 minutes with a 2-minute warning before expiry.

**FR-UM-005 — Audit Logging:** Every state-modifying action shall be logged with timestamp, user ID, and change description. Logs shall be immutable and retained for at least 5 years.

---

## 3. Data Ingestion

**FR-DI-001 — RSU Data Ingestion:** The system shall ingest real-time telemetry from RSUs at each monitored intersection — vehicle count, average speed, occupancy rate, and timestamp — at a configurable interval (default: 30 seconds).

**FR-DI-002 — Camera Feed Integration:** The system shall process IP camera streams to extract vehicle count, queue length, and incident flags via the on-premise computer vision pipeline. Raw video shall not be stored; only extracted metadata shall be persisted.

**FR-DI-003 — Connected Vehicle Data:** The system shall accept anonymised V2I telemetry (speed, heading, cell-level location) from enrolled connected vehicles to supplement sensor data in low-RSU corridors.

**FR-DI-004 — Pedestrian Counter Integration:** Push-button and PIR counter data shall be ingested to influence pedestrian phase duration in adaptive signal cycles.

**FR-DI-005 — Weather Feed Integration:** Current weather conditions (rain, fog, ice) from the city meteorological API shall be used as a modifier for safe signal cycle durations and incident sensitivity thresholds.

**FR-DI-006 — Data Validation:** All ingested records shall be validated against a defined schema. Failing records shall be quarantined in a dead-letter store with an alert raised to engineering. Invalid records shall never be used for signal computation.

---

## 4. Signal Control

**FR-SC-001 — Fixed Timing Plans:** Administrators shall be able to define and store multiple fixed signal timing plans per intersection (morning peak, off-peak, weekend). Operators shall activate them manually via dashboard.

**FR-SC-002 — Adaptive Signal Timing:** The system shall compute and apply adaptive cycles in real time from RSU and camera data, minimising overall vehicle queue length by dynamically allocating green time to the highest-demand phase.

**FR-SC-003 — Operator Override:** Operators shall be able to manually override signal state at any intersection with immediate effect. A visual indicator shall appear on the map for all overridden intersections.

**FR-SC-004 — Override Expiry:** Overrides shall automatically expire after a configurable duration (default: 15 minutes), reverting the intersection to adaptive or fixed timing unless explicitly extended.

**FR-SC-005 — Emergency Vehicle Preemption:** On receiving an emergency vehicle signal from dispatch, the system shall trigger a preemption sequence along the vehicle's predicted route — clearing its corridor green and holding cross-traffic red.

**FR-SC-006 — Green Wave Corridors:** The system shall support green wave corridor configuration, offsetting signal timing along a road segment for continuous vehicle progression at a configurable target speed (default: 50 km/h).

**FR-SC-007 — Signal Change Confirmation:** After issuing a phase change command, the system shall await RSU acknowledgement within 3 seconds. On failure, it shall retry once then raise a fault alert.

---

## 5. Incident Detection & Management

**FR-ID-001 — Automatic Detection:** The system shall detect: sudden density spikes, stalled vehicles (stationary in a moving lane >90 seconds), wrong-way vehicles, and pedestrians on the carriageway.

**FR-ID-002 — Alert Notification:** On detection, all logged-in operators shall receive an in-dashboard alert and mobile push notification with incident type, location, detected time, and severity.

**FR-ID-003 — Severity Classification:** Each incident shall be auto-assigned a severity — `Low`, `Moderate`, `High`, or `Critical` — based on incident type, affected lane count, and downstream traffic impact.

**FR-ID-004 — Incident Workflow:** Incidents shall progress through statuses: `Detected → Acknowledged → Under Review → Resolved`. Only `Resolved` incidents are removed from the active list.

**FR-ID-005 — Signal Response:** A `High` or `Critical` incident shall trigger automatic upstream signal adjustment to reduce inflow toward the incident zone until resolved or overridden.

**FR-ID-006 — False Positive Suppression:** A configurable confirmation window (default: 45 seconds) shall be applied before promoting a detected event to a confirmed incident.

---

## 6. Reporting & Analytics

**FR-RA-001 — Real-Time Dashboard:** The dashboard shall display a city map with all intersection signal states, a live traffic density heat map, and the active incident panel.

**FR-RA-002 — Historical Reports:** Planners shall generate reports on traffic flow, intersection utilisation, average journey times, and incident frequency for any period within the retention window. Export formats: CSV and PDF.

**FR-RA-003 — Signal Performance Reports:** Weekly per-intersection reports covering average queue length, phase utilisation, and adaptive cycle count shall be emailed to configured recipients every Monday at 07:00 local time.

**FR-RA-004 — Incident Summary Reports:** Monthly incident summaries covering total incidents by type, average resolution time, and false positive rate shall be accessible to `planner` and `admin` roles.

**FR-RA-005 — Custom Report Builder:** `Planner` and `admin` users shall be able to build custom reports by selecting metrics, groupings, time ranges, and filters without engineering involvement.

---

## 7. Public API

**FR-API-001 — Traffic Conditions Endpoint:** A public REST endpoint shall provide current traffic density and average speed by road segment, updated every 60 seconds, for authorised third-party navigation app providers.

**FR-API-002 — Incident Feed:** A public endpoint shall stream active confirmed incidents (type, location, severity) to navigation apps, city information portals, and emergency services.

**FR-API-003 — Rate Limiting:** All public endpoints shall enforce per-key rate limits (default: 60 requests/min, 5,000/day), configurable per partner by `admin`.

**FR-API-004 — Versioning:** The API shall use URL-based versioning. Breaking changes require a new major version; previous versions shall remain supported for at least 12 months.

---

## 8. Notifications, Alerts & Administration

**FR-NA-001 — Sensor Fault Alerting:** An RSU or camera missing 2+ consecutive expected intervals shall raise a fault alert assigned to `engineer` with device ID, last status, and location.

**FR-NA-002 — Configurable Thresholds:** Alert thresholds for density, queue length, and incident sensitivity shall be configurable per intersection or road segment.

**FR-NA-003 — Notification Channels:** Supported channels: in-dashboard alerts, email, SMS, and mobile push. Users configure which channels they receive per alert type.

**FR-NA-004 — Alert Escalation:** Unacknowledged `High` or `Critical` alerts shall auto-escalate to the on-call supervisor via SMS and email after 10 minutes.

**FR-SA-001 — Intersection Configuration:** Administrators shall add, edit, and deactivate intersections and assign RSUs, cameras, and signal controllers without a system deployment.

**FR-SA-002 — Device Registry:** A registry of all connected hardware (type, firmware version, location, status) shall be viewable by `engineer` and `admin`.

**FR-SA-003 — Configuration Version History:** All intersection and timing plan changes shall be versioned with rollback capability.

**FR-SA-004 — Maintenance Mode:** Administrators shall schedule maintenance windows per intersection or device, suppressing false alerts and flagging gaps as planned in reports.

---

## 9. Performance

**NFR-PF-001 — Signal Command Latency:** Phase change commands shall be acknowledged by the target RSU within **500ms** (p99) under normal operating conditions.  
*Verification: 1-hour load test, 200 concurrent intersections.*

**NFR-PF-002 — Dashboard Load Time:** The dashboard shall fully render within **3 seconds** (p95) on a 100 Mbps intranet workstation with 50 concurrent sessions.  
*Verification: Browser automation performance test.*

**NFR-PF-003 — Ingestion Throughput:** The ingestion pipeline shall sustain **10,000 events/second** with end-to-end latency ≤ 5 seconds (p99) and zero record loss.  
*Verification: 30-minute synthetic load test.*

**NFR-PF-004 — Report Generation:** Standard reports (≤90 days, single intersection) shall complete in **10 seconds**; city-wide annual reports in **60 seconds**.

**NFR-PF-005 — API Response Time:** All public API endpoints shall respond within **200ms** (p95) at 500 concurrent clients.

---

## 10. Availability, Reliability & Scalability

**NFR-AR-001 — Availability:** Core functions (ingestion, signal control, dashboard) shall maintain **99.9% uptime** over any rolling 90-day period, excluding approved maintenance windows.

**NFR-AR-002 — Maintenance Windows:** Rolling deployments shall keep the platform available during upgrades. Full maintenance windows shall not exceed 2 hours and shall be scheduled 02:00–04:00 local time.

**NFR-AR-003 — Failover Recovery:** Automatic failover to a standby node shall restore full service within **60 seconds** with no data loss for in-flight events.

**NFR-AR-004 — RSU Resilience:** RSUs shall continue operating autonomously on their last-known fixed timing plan during platform connectivity loss, leaving no intersection in an indeterminate state.

**NFR-AR-005 — Data Durability:** All sensor and incident records shall be persisted with **99.999999% durability**. No committed record shall be lost due to a single node failure.

**NFR-SC-001 — Intersection Scale:** The platform shall support up to **2,000 concurrent intersections** without architectural change or NFR degradation.

**NFR-SC-002 — Concurrent Sessions:** At least **200 concurrent operator sessions** shall be supported without dashboard performance degradation.

**NFR-SC-003 — Horizontal Scaling:** All stateless components shall scale horizontally by adding instances with no configuration changes and no downtime.

**NFR-SC-004 — Storage Growth:** The system shall accommodate at least **3 TB/year** of raw sensor records and **500 GB/year** of processed data without schema migration.

---

## 11. Security

**NFR-SE-001 — Encryption in Transit:** All component and external communication shall use TLS 1.2 or higher. TLS 1.0 and 1.1 shall be explicitly disabled.

**NFR-SE-002 — Encryption at Rest:** All stored data (sensor records, incidents, user data, audit logs) shall be encrypted with AES-256.

**NFR-SE-003 — Penetration Testing:** An independent third-party pentest shall be conducted before production launch and annually thereafter. All Critical and High findings shall be remediated before go-live.

**NFR-SE-004 — Secrets Management:** No credentials or API keys shall be stored in source control. All secrets shall be managed via a dedicated secrets manager (e.g., HashiCorp Vault) with access auditing enabled.

**NFR-SE-005 — Vulnerability Patching:** Critical/High CVEs shall be patched within **14 days** of disclosure; Medium CVEs within 60 days. Dependency scanning shall be integrated into the CI/CD pipeline.

**NFR-SE-006 — Signal Override Authorisation:** Only `operator` and `admin` roles may issue signal override commands. Every command shall be cryptographically signed and verified server-side before execution.

---

## 12. Maintainability, Usability & Compliance

**NFR-MT-001 — Observability:** All core services shall emit structured logs, metrics, and distributed traces with a correlation ID enabling end-to-end event tracing from RSU ingestion to signal command.

**NFR-MT-002 — Mean Time to Diagnose:** On-call engineers shall diagnose production signal control incidents within **30 minutes** using available tooling and runbooks (target: 80% of incidents in first 6 months).

**NFR-MT-003 — Test Coverage:** Backend services shall maintain minimum **80% unit test coverage**, enforced as a CI merge gate.

**NFR-MT-004 — Deployment Frequency:** The CI/CD pipeline shall support at least one release per week using blue-green or rolling deployments with no system downtime.

**NFR-MT-005 — API Documentation:** Public API documentation shall be auto-generated from OpenAPI annotations and published automatically on each deployment.

**NFR-US-001 — Operator Training:** New operators shall complete core tasks (incident acknowledgement, signal override, report generation) after no more than **4 hours** of structured training, validated during UAT.

**NFR-US-002 — Accessibility:** The operator dashboard and public portal shall conform to **WCAG 2.1 Level AA**.

**NFR-US-003 — Browser Compatibility:** The dashboard shall be fully functional on the latest two major versions of Chrome, Firefox, and Edge.

**NFR-CG-001 — Data Residency:** All data shall reside within the city's data governance jurisdiction. Cross-boundary transfer requires explicit DPO approval.

**NFR-CG-002 — Data Minimisation:** No individually identifiable driver or pedestrian data shall be collected. V2I data shall be cell-level only (no VIN or plate). Camera metadata shall contain aggregate counts and flags only.

**NFR-CG-003 — Audit Log Retention:** Audit logs shall be retained for a minimum of **5 years** in a tamper-evident format, per city record-keeping obligations.

---

*End of Requirements Specification — SCTMP-RS-001 v1.0*
