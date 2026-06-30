# Troubleshooting Guide: HPE GreenLake Project

**Document Version:** 1.0

**Last Updated:** [Date]

**Target Audience:** IT Administrators, Cloud Ops, and Support Teams

This document outlines the standard troubleshooting procedures for the HPE GreenLake environment, covering common platform access, infrastructure, and capacity management issues.

---

## 1. Initial Checks & Prerequisites

Before diving into specific troubleshooting steps, ensure the following basic checks are completed:

* **Network Connectivity:** Verify that the on-premises site has an active outbound internet connection to the HPE GreenLake Central portal and HPE InfoSight.
* **Authentication:** Ensure your user account is active in your Identity Provider (IdP) and mapped to the correct Role-Based Access Control (RBAC) policy within GreenLake Central.
* **Service Status:** Check the [HPE Status Page](https://www.google.com/search?q=https://status.hpe.com/) for any ongoing global or regional outages affecting GreenLake services.
* **Local Hardware:** Confirm there are no visible amber/red fault lights on the physical HPE racks (Compute, Storage, Networking) in your local data center.

---

## 2. Common Issues and Resolutions

### Issue 2.1: Unable to Access HPE GreenLake Central

**Symptoms:** Browser times out, access denied errors, or blank screens when navigating to GreenLake Central.

* **Step 1:** Clear your browser cache and cookies, or try an Incognito/Private browsing window.
* **Step 2:** Verify SSO (Single Sign-On) token validity. If using SAML/Active Directory integration, check if your local AD password has expired.
* **Step 3:** Check corporate firewall rules. Ensure outbound HTTPS (Port 443) traffic is permitted to `*.greenlake.hpe.com`.
* **Step 4:** Verify your RBAC assignment with your local GreenLake Workspace Administrator.

### Issue 2.2: Compute or Storage Provisioning Failures

**Symptoms:** VMs fail to deploy, or storage volumes cannot be attached via the self-service portal.

* **Step 1:** Check local resource availability. Have you exceeded your reserved capacity buffer?
* **Step 2:** Review the hypervisor logs (e.g., VMware vCenter or Microsoft Hyper-V) for underlying hardware constraints or lock files.
* **Step 3:** If using HPE Alletra or Nimble storage, log into the local array UI to check for disconnected initiators or full storage pools.
* **Step 4:** Validate that the network VLANs required for the new deployment are properly trunked to the compute nodes.

### Issue 2.3: Capacity and Consumption Threshold Alerts

**Symptoms:** Receiving automated email alerts indicating capacity is reaching 80%+ or consumption is spiking.

* **Step 1:** Log into GreenLake Central and navigate to the **Capacity Planning** and **Consumption Analytics** dashboard.
* **Step 2:** Identify the top-consuming workloads (e.g., a specific database VM or storage volume).
* **Step 3:** Determine if the spike is expected (e.g., batch processing, end-of-month reporting) or a sign of a runaway process.
* **Step 4:** If the growth is permanent, contact your HPE GreenLake Account Manager to trigger the Change Order process for adding more buffer capacity before you hit 100% utilization.

### Issue 2.4: Disconnected Data Sync (Metering Issues)

**Symptoms:** The consumption dashboard hasn't updated in 24-48 hours.

* **Step 1:** Check the status of the on-premises HPE GreenLake metering appliance or Cloud Cruiser data collectors.
* **Step 2:** Verify that the metering appliance can reach the HPE billing endpoints over the network.
* **Step 3:** Restart the metering service daemon via SSH on the collector VM:
```bash
sudo systemctl restart hpe-metering-collector
sudo systemctl status hpe-metering-collector

```

---

## 3. Log Collection and Diagnostics

When escalating an issue, gathering the right logs is critical for a fast resolution.

* **Physical Server Issues (Compute):** Generate an Active Health System (AHS) log from the server's iLO interface.
* **Storage Issues:** Generate a diagnostic bundle from the HPE Alletra/Nimble/Primera UI.
* **Virtualization Issues:** Export a vCenter Support Bundle or Hyper-V cluster logs.
* **HPE InfoSight:** Ensure your devices are actively sending telemetry data to InfoSight to allow HPE support to remotely diagnose hardware faults.

---

**End of Document**
