# Standard Operating Procedures (SOP): HPE GreenLake Infrastructure as a Service

## Document Control
| Version | Date | Author | Description of Changes |
| :--- | :--- | :--- | :--- |
| 1.0 | July 8, 2026 | IT Infrastructure Team | Initial Draft and Baseline SOP for HPE GreenLake |

---

## 1. Introduction and Purpose
This Standard Operating Procedure (SOP) defines the operational guidelines, roles, and administrative processes required to manage the HPE GreenLake environment. HPE GreenLake provides an on-premises cloud experience with a consumption-based IT model. This document ensures consistent, secure, and efficient management of hardware, software, metering, billing, and support life cycles.

### 1.1 Scope
This SOP applies to all IT administrators, cloud architects, and financial controllers involved in the day-to-day operation, monitoring, and financial management of the HPE GreenLake deployment. It covers compute, storage, and networking resources governed under the GreenLake contract.

---

## 2. Roles and Responsibilities

Successful operation of a GreenLake environment requires a shared responsibility model between the Internal IT Organization and HPE Pointnext Services.

### 2.1 Internal IT Organization Roles
* **Infrastructure Lead / Cloud Architect:** Oversees the entire GreenLake footprint, aligns capacity with business needs, and approves architecture changes.
* **IT Operations Administrator:** Handles daily monitoring, VM/container provisioning, and initial troubleshooting within the active capacity.
* **Financial / FinOps Analyst:** Reviews monthly consumption reports, verifies metered billing, and manages cost allocation using GreenLake Central.

### 2.2 HPE Pointnext Roles
* **Customer Success Manager (CSM):** Primary strategic contact. Conducts quarterly business reviews (QBRs), advises on optimization, and oversees contract lifecycle.
* **Account Support Manager (ASM):** Primary technical contact. Manages incident escalation, coordinates patch management, and provides technical reporting.
* **HPE Remote Monitoring Team:** Provides 24/7 telemetry monitoring of the hardware and initiates proactive support tickets for hardware degradation.

---

## 3. Access and Portal Management

All management of the GreenLake consumption model is performed via **HPE GreenLake Central**.

### 3.1 Portal Provisioning
1.  **Access Request:** Users requiring access must submit a ticket via the internal IT Service Management (ITSM) tool.
2.  **Role-Based Access Control (RBAC):**
    * *IT Admins:* Granted `Infrastructure Manager` role for capacity and health monitoring.
    * *FinOps:* Granted `Financial Analyst` role for billing, budgeting, and capacity planning views only.
3.  **Authentication:** Access is federated via the organization’s Azure Active Directory (AAD) / Okta SSO enforcing Multi-Factor Authentication (MFA).

### 3.2 Regular Access Reviews
* The IAM team will conduct quarterly audits of GreenLake Central accounts to ensure offboarded employees or transferred roles have their access revoked.

---

## 4. Capacity Planning and Management

HPE GreenLake operates on a model of "Active Capacity" (what is currently being used) and "Buffer Capacity" (extra hardware on-site ready to be provisioned instantly).

### 4.1 Monitoring Consumption
1.  **Daily Review:** IT Operations will monitor the Capacity Dashboard in GreenLake Central.
2.  **Thresholds & Alerts:** * **Warning (70% Active Capacity Used):** Triggers an automated alert to the Infrastructure Lead.
    * **Critical (80% Active Capacity Used):** Triggers an automated alert to the FinOps team and Infrastructure Lead. At this stage, the buffer is being heavily consumed.

### 4.2 Triggering a Capacity Expansion (Change Order)
When active capacity reaches the 80% threshold, the organization must initiate a Change Request to add more physical buffer to the datacenter before the existing buffer runs out.
1.  **Initiation:** Infrastructure Lead generates a capacity forecast report from GreenLake Central.
2.  **HPE Notification:** Infrastructure Lead contacts the HPE CSM to request a quote for additional compute/storage blocks.
3.  **Approval:** The quote is routed to IT Procurement and the CIO for financial approval.
4.  **Deployment:** Once approved, HPE Pointnext ships, installs, and connects the new hardware to the existing cluster, updating the buffer baseline without system downtime.

---

## 5. Metering and Billing Procedures

GreenLake charges based on metered usage (e.g., per GB of storage, per Compute Unit) processed through HPE's metering scripts.

### 5.1 Monthly Billing Cycle
1.  **Invoice Generation:** HPE generates the monthly invoice based on daily telemetry averages over the billing period.
2.  **Invoice Verification (Days 1-5 of Month):**
    * The FinOps Analyst downloads the detailed CSV consumption report from GreenLake Central.
    * The analyst cross-references the billed units against the internal telemetry dashboards.
3.  **Dispute Resolution:**
    * If discrepancies exceed 2%, the FinOps Analyst must open a billing inquiry with the HPE CSM within 5 business days.
    * No payment is withheld; adjustments are processed as credits on the subsequent invoice.
4.  **Cost Allocation:** FinOps tags workloads in GreenLake Central to perform chargebacks to internal business units (e.g., HR, R&D, Marketing).

---

## 6. Incident and Problem Management

### 6.1 Hardware Failures (Proactive)
1.  HPE InfoSight and GreenLake continuous monitoring detect a failing component (e.g., disk drive pre-failure alert, RAM ECC errors).
2.  HPE automatically generates an L3 support ticket and dispatches a field technician with the replacement part (Datacenter Care SLA: 4-hour onsite).
3.  The ASM notifies the IT Operations Admin of the incoming technician and requires an escorted entry to the datacenter.

### 6.2 Software/Performance Incidents (Reactive)
1.  **L1/L2 Triage:** Internal IT Operations detects a performance issue (e.g., high latency on storage volumes) and performs standard troubleshooting.
2.  **L3 Escalation to HPE:**
    * If the issue cannot be resolved, IT Operations opens a priority ticket via the HPE Support Portal.
    * **Sev 1 (Critical Business Impact):** 15-minute response SLA. Requires phone escalation to the ASM.
    * **Sev 2 (Degraded):** 2-hour response SLA.
    * **Sev 3 (Informational/Minor):** Next business day response.

---

## 7. Change and Patch Management

Hardware firmware and management software updates are jointly managed to maintain supportability and security compliance.

### 7.1 Firmware and Driver Updates
1.  **Assessment:** The HPE ASM provides a quarterly patch advisory (Service Pack for ProLiant - SPP, storage OS updates).
2.  **Review:** Internal IT Operations and Security teams review the release notes to assess security fixes and operational impacts.
3.  **Scheduling:** * Updates are scheduled during the organization's standard maintenance window (e.g., 02:00 AM - 06:00 AM on Sundays).
    * Change tickets are submitted to the internal Change Advisory Board (CAB) for approval.
4.  **Execution:** HPE Pointnext executes the firmware updates remotely or onsite. Internal IT verifies system health and workload failover post-update.

---

## 8. Lifecycle Management and Decommissioning

### 8.1 Hardware Refresh Cycle
HPE GreenLake hardware is typically refreshed every 3 to 5 years depending on the contract terms.
1.  **6 Months Prior to Refresh:** HPE CSM presents new generation hardware options and a migration plan.
2.  **Migration:** IT Operations executes workload migrations (e.g., vMotion) to the newly installed hardware footprint.
3.  **Decommissioning:**
    * Once all workloads are evacuated, HPE engineers decommission the old nodes.
    * HPE performs Department of Defense (DoD) standard data wiping on all storage media.
    * HPE provides a formal Certificate of Destruction (CoD) to the internal IT Security/Compliance team.

---

## 9. Security and Compliance

### 9.1 Data Sovereignty and Security
* All data resides on-premises; HPE GreenLake metering telemetry only sends non-payload metadata (CPU utilization, RAM consumption, storage capacity) to the cloud.
* Data at rest must be encrypted using self-encrypting drives (SEDs) with keys managed by the internal IT Key Management Server (KMS).

### 9.2 Compliance Auditing
* The Infrastructure team will export system access logs and configuration baselines from GreenLake Central quarterly.
* These reports will be stored in the internal compliance repository for ISO 27001 and SOC 2 audits.

---
*End of Document*
