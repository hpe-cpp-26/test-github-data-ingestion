# Project Design Document: IBM Quantum System Two & Modular Quantum Centric Supercomputing

This design document outlines the system architecture, hardware engineering, and software stack governing the **IBM Quantum System Two**, the industry’s first fully modular, enterprise-grade quantum computing platform designed to initiate the era of **Quantum Utility** and scale toward fault-tolerant supercomputing.

---

## 1. Project Vision & Architectural Strategy

The primary bottleneck of quantum scaling has historically been the physical limitations of a single dilution refrigerator and monolithic chip architectures. IBM Quantum System Two addresses this by shifting from a monolithic design to a **modular, distributed-node supercomputing cluster**.

* **Quantum-Centric Supercomputing:** Integrating quantum processing units (QPUs), classical high-performance computing (HPC) resources, and advanced control infrastructure into a single, cohesive network fabric.
* **Modular Elasticity:** Providing a flexible 22-foot-wide by 12-foot-high environmental shell that allows the system to link multiple separate quantum processors via quantum communication links, bypassing the physical space constraints of a single cryogenic environment.
* **Continuous Availability:** Engineering a target uptime exceeding 95% via hot-swappable classical control electronics, modular cryogenics, and automated cloud-delivered calibration loops.

---

## 2. Core Hardware & Processing Unit (QPU) Infrastructure

### The Quantum Processor Family: Heron & Nighthawk

The system is built to house IBM’s premium superconducting architectures, transitioning away from the older Eagle generations to focus on structural fidelity and low error rates:

* **Tunable Couplers:** Utilizing **Heron (133/156-qubit nodes)** and the **Nighthawk (120-qubit grid nodes)** architectures. By implementing tunable c-couplers between fixed-frequency transmon qubits, the system actively mitigates chronic quantum cross-talk.
* **Performance Gain:** The hardware architecture yields a 3x to 5x improvement in device performance over legacy chips, practically eliminating multi-qubit collision errors.

### Cryogenic Infrastructure & High-Density Flex I/O

Superconducting qubits must be preserved at a base temperature of approximately 15 milli-Kelvin ($15\text{ mK}$), which is significantly colder than deep space.

* **High-Density Packaging:** Traditional heavy, semi-rigid coaxial cables have been entirely replaced. System Two relies on **high-density cryogenic flex I/O ribbon cabling**.
* **Footprint Reduction:** This flexible, high-density laminate wiring packs over a mile of microwave signal channels into a highly compressed space, decreasing the thermal load on the dilution refrigerator while expanding input/output density by over 50%.

---

## 3. Classical Control & Runtime Infrastructure

### Third-Generation Control Electronics

Quantum processors require continuous, highly precise analog microwave pulses to manipulate qubit states ($X$, $Y$, $Z$ gates) and read out results.

* **FPGA Matrix:** System Two deploys integrated, ultra-low-latency FPGA-based control boards directly co-located inside the modular chassis.
* **Real-Time Decoding Prototype:** This tier houses the system's hardware error-correction decoder prototypes. It handles real-time error detection within the qubit coherence window, enabling adaptive "if-then" branching logic during mid-circuit measurements.

### The Hybrid Classical-Quantum Runtime

Quantum operations do not run in a vacuum; they depend heavily on ultra-fast classical compute assistance.

* **Co-located Runtime Servers:** Standard enterprise servers are physically integrated into the System Two framework, connected directly via high-bandwidth, low-latency classical networking to the control hardware.
* **Execution Acceleration:** This setup handles heavy classical optimization loops—such as updating parameter tensors in Variational Quantum Eigensolvers (VQE)—directly on-site, accelerating hybrid workflows up to 85x faster than legacy cloud-routing infrastructures.

---

## 4. Software Orchestration & The Developer Stack

The ecosystem relies on an open-source, highly modular software framework to compile, optimize, and execute algorithms across the distributed hardware.

### Qiskit SDK Core

[Qiskit](https://quantum.cloud.ibm.com/docs/guides/processor-types) acts as the fundamental operating engine for the system, abstracting the complex physics of the qubits into logical compiler patterns.

* **AI-Driven Transpilation:** The software integrates reinforcement learning (RL) models into its compilation engine. The AI service reduces overall two-qubit gate counts by 20% to 50% compared to traditional mathematical heuristics, compressing execution depth before quantum states naturally decohere.
* **C-API Native Performance:** Low-level circuit compilation bridges directly into native C-libraries, eliminating heavy serialization overhead and optimizing memory profiles during massive-scale parallel operations.

### Execution Frameworks: Batching & Sessions

To optimize QPU multi-tenancy and maximize hardware utilization, the system enforces strict execution models:

* **Batch Mode:** Groups independent, non-iterative quantum jobs together to execute in parallel across independent multi-chip clusters, driving up hardware throughput.
* **Extended Sessions:** Locks an exclusive reservation thread on the hardware for iterative, utility-scale algorithms. This permits continuous, unbroken communication between the local classical runtime servers and the QPUs without losing priority in the global cloud queue.

