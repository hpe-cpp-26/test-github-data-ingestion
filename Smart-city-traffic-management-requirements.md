# Project: Smart City Traffic Management System (SCTMS)
## Software Requirements Specification (SRS) 

**Date:** July 8, 2026  
**Version:** 1.0  
**Status:** Draft / Proposed  

---

## Table of Contents
1. [Introduction](#1-introduction)
2. [Overall Description](#2-overall-description)
3. [Functional Requirements](#3-functional-requirements)
4. [External Interface Requirements](#4-external-interface-requirements)
5. [Non-Functional Requirements](#5-non-functional-requirements)
6. [Compliance & Security](#6-compliance--security)


## 1. Introduction

### 1.1 Purpose
The purpose of this document is to define the software and hardware requirements for the Smart City Traffic Management System (SCTMS). This document serves as the primary reference for the development, testing, and deployment teams, as well as city administrators and stakeholders.

### 1.2 Scope
SCTMS is an integrated, AI-driven traffic management platform designed to optimize traffic flow, reduce congestion, enhance road safety, and prioritize emergency and public transportation vehicles. The system will ingest data from IoT sensors, CCTV cameras, and GPS units to dynamically adjust traffic signals and dispatch incident alerts.

### 1.3 Definitions and Acronyms
* **SCTMS:** Smart City Traffic Management System
* **IoT:** Internet of Things
* **ANPR:** Automatic Number Plate Recognition
* **EVP:** Emergency Vehicle Preemption
* **VMS:** Variable Message Signs
* **AI/ML:** Artificial Intelligence / Machine Learning

---

## 2. Overall Description

### 2.1 Product Perspective
The SCTMS will act as the central brain of the city's transportation infrastructure. It interfaces directly with legacy traffic light controllers, modern IoT road sensors, emergency dispatch systems (CAD), and public-facing mobile applications.

### 2.2 User Classes and Characteristics
| User Class | Description | Access Level |
| :--- | :--- | :--- |
| **City Traffic Operators** | Monitor traffic flow, manual override capabilities, view alerts. | High |
| **System Administrators** | Manage user roles, configure system parameters, maintain server health. | Administrative |
| **Emergency Dispatchers** | Request route clearance (EVP) for ambulances, fire trucks, and police. | Medium (Specific modules) |
| **Data Analysts** | Generate reports, analyze historical traffic trends and AI models. | Medium (Read-only/Reporting) |
| **General Public** | Access traffic conditions via mobile app or web portal. | Low (Read-only APIs) |

### 2.3 Operating Environment
* **Cloud Infrastructure:** AWS / Azure / GCP (High Availability setup)
* **Edge Computing:** Edge nodes at major intersections for localized ML inference (low latency).
* **Client Interfaces:** Web-based Command Center (Chrome, Firefox, Edge) and Mobile Applications (iOS, Android).

---

## 3. Functional Requirements

### 3.1 Real-Time Traffic Monitoring (FR-01)
* **FR-01.1:** The system shall ingest real-time video feeds from CCTV cameras at intersections.
* **FR-01.2:** The system shall use computer vision to classify and count vehicles (cars, buses, trucks, two-wheelers, pedestrians).
* **FR-01.3:** The system shall calculate average speeds, queue lengths, and waiting times at each junction.

### 3.2 Adaptive Traffic Signal Control (FR-02)
* **FR-02.1:** The AI engine shall dynamically adjust the green/red light phases based on real-time traffic density.
* **FR-02.2:** The system shall synchronize consecutive traffic signals to create "green waves" during peak hours to improve corridor throughput.
* **FR-02.3:** The system must allow Traffic Operators to manually override signal phases in case of extreme anomalies.

### 3.3 Emergency Vehicle Preemption (EVP) (FR-03)
* **FR-03.1:** The system shall integrate with emergency vehicle GPS trackers.
* **FR-03.2:** Upon detecting an approaching active emergency vehicle, the system shall preemptively turn the signal green in its path and red for conflicting traffic.
* **FR-03.3:** Normal traffic signal operations must automatically resume once the emergency vehicle has cleared the intersection.

### 3.4 Incident Detection and Management (FR-04)
* **FR-04.1:** The system shall automatically detect anomalies such as accidents, stalled vehicles, or wrong-way driving using AI video analytics.
* **FR-04.2:** Upon detection, the system shall generate an alert in the Command Center within 5 seconds.
* **FR-04.3:** The system shall automatically update Variable Message Signs (VMS) on approaching roads to warn drivers of the incident.

### 3.5 Public Transit Prioritization (FR-05)
* **FR-05.1:** The system shall recognize approaching city buses (via GPS or ANPR).
* **FR-05.2:** If a bus is behind schedule, the system shall extend the green light duration slightly to allow the bus to pass without stopping.

---

## 4. External Interface Requirements

### 4.1 Hardware Interfaces
* **Traffic Controllers:** Must communicate with NTCIP (National Transportation Communications for ITS Protocol) compliant controllers.
* **Sensors:** Integration with inductive loop detectors, radar sensors, and LiDAR setups.
* **VMS:** Support for standard IP-based LED messaging boards.

### 4.2 Software Interfaces
* **Emergency Dispatch API:** RESTful API integration with the city's 911/CAD (Computer-Aided Dispatch) software.
* **Weather Service API:** Fetch real-time weather data to adjust driving condition parameters (e.g., longer stopping times during rain/snow).
* **Mapping Providers:** Integration with Google Maps / Waze for bidirectional data sharing.

---

## 5. Non-Functional Requirements

### 5.1 Performance
* **Latency:** Edge node video processing and decision-making must occur in under 500 milliseconds.
* **Availability:** The core command center and signal control modules must have 99.999% uptime (High Availability architecture).
* **Throughput:** The cloud backend must be capable of ingesting at least 100,000 telemetry messages per second from city-wide sensors.

### 5.2 Scalability
* The system architecture must support the addition of up to 10,000 intersections and 50,000 IoT devices without requiring a core codebase rewrite.
* Cloud resources must auto-scale during peak traffic hours (e.g., 7:00 AM - 9:30 AM; 4:30 PM - 7:00 PM).

### 5.3 Reliability
* **Fail-Safe Mode:** If communication between the central server and an intersection is lost, the local controller must automatically revert to a pre-programmed, time-of-day static signal plan.

---

## 6. Compliance & Security

### 6.1 Data Privacy
* **Anonymization:** Video feeds must be processed on the edge. License plates and faces must be blurred or hashed before any video data is transmitted to the cloud, complying with local privacy laws (e.g., GDPR, CCPA).
* **Retention:** Raw traffic footage will be retained for a maximum of 72 hours unless flagged for an incident investigation.

### 6.2 Cybersecurity
* **Encryption:** All data in transit must use TLS 1.3. All data at rest must use AES-256 encryption.
* **Authentication:** Multi-Factor Authentication (MFA) is strictly required for all Command Center logins.
* **Network Security:** Traffic controllers must sit on an isolated, zero-trust V-LAN, unreachable from the public internet.

---
*End of Document*
