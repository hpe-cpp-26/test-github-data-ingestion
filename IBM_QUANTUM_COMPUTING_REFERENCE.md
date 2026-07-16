# IBM Quantum Computing System: Reference Guide

This document serves as a comprehensive reference guide to the IBM Quantum Computing ecosystem, covering hardware, software, system architecture, and strategic roadmaps up to the 2033+ timeline [1.1.2].


## 1. System Architecture

### IBM Quantum System Two
Unveiled in December 2023, the **IBM Quantum System Two** is the core building block of IBM's vision for "quantum-centric supercomputing" [1.2.1, 1.2.4].
* **Dimensions:** 22 ft x 22 ft x 12 ft (including glass enclosure) [1.2.1].
* **Modularity:** Designed as a fully modular and extensible architecture, allowing multiple quantum processing units (QPUs) to be connected to scale compute power [1.2.1].
* **Cooling:** Operates at ultra-low temperatures (10–20 mK) via advanced dilution refrigeration [1.2.1].
* **Compute Power:** The initial deployment at Yorktown Heights houses three **IBM Quantum Heron** processors [1.2.2]. It integrates cryogenic infrastructure with third-generation control electronics and classical runtime servers to handle hybrid quantum-classical workloads [1.2.2].

---

## 2. Hardware and Quantum Processors

IBM categorizes its processors into "families" (e.g., Eagle, Condor, Heron), denoting the circuit size and connectivity structure [1.2.2].

### IBM Heron (133 Qubits)
* **Status:** Current flagship processor for high-performance quantum utility [1.2.2, 1.2.5].
* **Architecture:** Features 133 fixed-frequency qubits with tunable couplers [1.2.2].
* **Performance:** Yields a 3–5x improvement in device performance over previous flagship processors (Eagle), virtually eliminating cross-talk [1.2.2, 1.2.5].

### IBM Condor (1,121 Qubits)
* **Status:** Innovation milestone for scale and yield [1.2.2, 1.2.5].
* **Architecture:** Arranged in a honeycomb configuration based on cross-resonance gate technology [1.2.2].
* **Advancements:** Features a 50% increase in qubit density and includes over a mile of high-density cryogenic flex I/O wiring within a single dilution refrigerator [1.2.2, 1.2.5].

### Legacy/Previous Processors
* **Eagle (127 Qubits):** Launched in 2021, featuring heavy-hexagonal qubit layouts [1.2.2].
* **Osprey (433 Qubits):** Announced in November 2022 [1.2.2].

---

## 3. Software Stack and Qiskit

IBM's software stack seamlessly connects quantum resources with classical high-performance computing (HPC) environments [1.1.2].

### Qiskit
Qiskit is IBM's open-source quantum computing software development kit (SDK) [1.2.5].
* **Qiskit 1.0 (2024):** Delivered major improvements in programming speed, memory usage, stability, and circuit construction/compilation times [1.2.4, 1.2.5].
* **Qiskit Functions Service:** Deployed for Premium users to simplify the application of algorithms to industry problems [1.1.2].

### Workflow and Execution
* **AI Transpilation:** Uses reinforcement learning to optimize quantum circuits, reducing two-qubit gate counts by 20–50% compared to standard heuristic methods [1.2.5].
* **Execution Modes:** Features optimized batch processing and "Extended Sessions" to handle utility-scale iterative workloads faster [1.2.5].
* **C-API (2025):** Enabling developers to write Qiskit code in C to better orchestrate workloads for HPC integrations [1.1.2].

---

## 4. Hardware Roadmap & Future Milestones

IBM's Development Roadmap targets large-scale, fault-tolerant quantum computing (FTQC) [1.1.2, 1.1.4].

* **2026: IBM Nighthawk & Quantum Advantage:** Nighthawk aims to run circuits with 7,500 gates across multiple 120-qubit modules. IBM expects this to demonstrate the first clear examples of quantum advantage by integrating quantum systems with HPC [1.1.1, 1.1.2].
* **2028: Advancing Nighthawk:** Anticipated to run circuits with up to 15,000 gates on up to 1,080 qubits, alongside the introduction of computational libraries and real-time error correction prototypes [1.1.1, 1.1.2].
* **2029: IBM Starling (Fault-Tolerance):** Starling is projected to be the first fault-tolerant quantum computer, fielding 200 qubits and capable of executing 100 million gates [1.1.1, 1.1.2].
* **2033+: IBM Blue Jay:** A massive leap delivering a 2,000-qubit system capable of running 1 billion gates, unlocking a new era of algorithmic complexity [1.1.1, 1.1.2].

---

## 5. Key Terminology

* **Quantum Utility:** The era where quantum computers can perform reliable computations beyond brute-force classical simulation capabilities [1.2.4].
* **Fault-Tolerant Quantum Computing (FTQC):** Systems capable of correcting logical errors in real-time, preventing them from spreading, which is necessary for deep, large-scale algorithms [1.1.5].
* **Qubit Connectivity (c-couplers / l-couplers):** Crucial for scalable architectures, allowing processing across chips and modules (e.g., up to six degrees of connectivity in the 'Loon' architecture) [1.1.3, 1.1.4].
