# Performance Analysis of Type-1 (Proxmox VE) vs. Type-2 (VMware Workstation) Hypervisors

## 1. Objective & Scope
The objective of this experiment is to evaluate and compare the computational performance of Type-1 (bare-metal) and Type-2 (hosted) hypervisors. Both virtual environments execute an identical compute-bound Sysbench CPU workload calculating prime numbers up to 20,000 across a 10-second test window.

---

---

## Table of Contents
- [1. Objective & Scope](#1-objective--scope)
- [2. Virtual Machine Test Environment](#2-virtual-machine-test-environment)
- [3. Empirical Results & Console Telemetry](#3-empirical-results--console-telemetry)
  - [Type-1: Proxmox VE Console](#type-1-hypervisor-proxmox-ve-web-console)
  - [Type-2: VMware Workstation Terminal](#type-2-hypervisor-vmware-workstation-terminal)
- [4. Benchmark Performance Metrics](#4-benchmark-performance-metrics)
- [5. Performance Visualizations](#5-performance-visualizations)
  - [5.1 Computational Throughput](#1-computational-throughput-events-per-second)
  - [5.2 Latency Spectrum Analysis](#2-latency-spectrum-analysis-milliseconds)
  - [5.3 Resource Efficiency per GB of RAM](#3-resource-efficiency-throughput-per-gb-of-ram)
  - [5.4 Latency Spread & Scheduling Jitter](#4-latency-spread--scheduling-jitter)
  - [5.5 Cumulative Workload Execution Curve](#5-cumulative-workload-execution-curve)
- [6. Technical Analysis & Findings](#6-technical-analysis--findings)

## 2. Virtual Machine Test Environment

| Parameter | Type-1: Proxmox VE (KVM) | Type-2: VMware Workstation | Control State |
| :--- | :--- | :--- | :--- |
| **Hypervisor Architecture** | Bare-Metal (Ring -1 / KVM) | Hosted (Runs on Windows Host OS) | Architectural Variable |
| **Virtual Machine Name** | `CC-Experiment1-type1` | `CC-Experiment1-Type2` | Standardized |
| **Guest Operating System** | Ubuntu 64-bit | Ubuntu 64-bit | Identical |
| **Virtual CPUs** | 2 vCPU | 2 vCPU | Identical compute threads |
| **Allocated Memory** | 2048 MB (2 GB) | 8192 MB (8 GB) | Documented Variance |
| **Allocated Storage** | 20 GB | 20 GB | Standardized Disk |
| **Benchmark Command** | `sysbench cpu --cpu-max-prime=20000 run` | `sysbench cpu --cpu-max-prime=20000 run` | Standard 1-thread execution |

---

## 3. Empirical Results & Console Telemetry

### Type-1 Hypervisor (Proxmox VE Web Console)
<img width="1597" height="1042" alt="image" src="https://github.com/user-attachments/assets/a4f8dfda-4b85-4e27-ab0b-8644361b35cc" />


### Type-2 Hypervisor (VMware Workstation Terminal)
<img width="1316" height="696" alt="Sysbench_cpu" src="https://github.com/user-attachments/assets/0081b412-c467-478a-834f-6276d5b7d502" />

---

---

## 4. Benchmark Performance Metrics

| Performance Metric | Type-1: Proxmox VE | Type-2: VMware Workstation | Absolute Delta | Percentage Variance |
| :--- | :---: | :---: | :---: | :---: |
| **Execution Duration** | 10.0004 s | 10.0003 s | -0.0001 s | ~0.00% |
| **Total Events Processed** | 17,169 | 17,588 | +419 events | **+2.44%** |
| **Throughput (Events/sec)** | 1,716.69 eps | 1,758.60 eps | +41.91 eps | **+2.44%** |
| **Minimum Latency** | 0.57 ms | 0.55 ms | -0.02 ms | -3.51% |
| **Average Latency (Mean)** | 0.58 ms | 0.57 ms | -0.01 ms | -1.72% |
| **95th Percentile Latency** | 0.65 ms | 0.61 ms | -0.04 ms | -6.15% |
| **Maximum Latency Spike** | 2.78 ms | 1.11 ms | -1.67 ms | -60.07% |

---

## 5. Performance Visualizations

### 1. Computational Throughput (Events per Second)
<img width="2400" height="1500" alt="throughput_comparison" src="https://github.com/user-attachments/assets/bf5f9b19-5ed1-47e2-ba2e-fae5bb21b7b0" />


---

### 2. Latency Spectrum Analysis (Milliseconds)
<img width="2700" height="1650" alt="latency_comparison" src="https://github.com/user-attachments/assets/b575aaf7-8da1-4ce5-8227-a694228f1ffe" />


### 3. Resource Efficiency: Throughput per GB of RAM
<img width="2100" height="1500" alt="resource_efficiency_comparison" src="https://github.com/user-attachments/assets/8f5d49df-0165-47af-8f51-098b1826fdc7" />


*Figure 3: Demonstrates compute efficiency normalized by memory footprint. Proxmox VE achieved ~3.9x higher throughput per allocated gigabyte.*

---

### 4. Latency Spread & Scheduling Jitter
<img width="2400" height="1500" alt="latency_spread_analysis" src="https://github.com/user-attachments/assets/970cd6c0-abe2-4e8a-8ace-d2301c161db1" />


*Figure 4: Latency range from minimum to 95th percentile with outlier tail spikes.*

---

### 5. Cumulative Workload Execution Curve
<img width="2400" height="1440" alt="cumulative_execution_timeline" src="https://github.com/user-attachments/assets/e365547d-a73e-482a-91cc-31f1fab2714c" />
*Figure 5: 10-second linear execution progression showing stable instruction retirement across both platforms.*


---

## 6. Technical Analysis & Findings

1. **Throughput Comparison:**
   VMware Workstation achieved **1,758.60 events/sec**, marginally outpacing Proxmox VE (**1,716.69 events/sec**) by **+2.44%**. While Type-1 hypervisors theoretically offer lower virtualization overhead, this difference is primarily attributed to physical host CPU hardware differences: the desktop processor hosting VMware operated at a higher single-core dynamic frequency boost compared to the institutional server node powering the Proxmox installation. Additionally, VMware was provisioned with 8 GB of RAM versus 2 GB on Proxmox, reducing system memory pressure.

2. **Latency Distribution & Scheduling Stability:**
   Both hypervisors maintained efficient sub-millisecond execution, averaging **0.58 ms** on Proxmox VE and **0.57 ms** on VMware. However, Proxmox VE recorded a maximum tail latency spike of **2.78 ms** compared to **1.11 ms** on VMware. This tail latency on the Proxmox instance reflects concurrent multi-tenant I/O contention on the shared institutional lab server (`admin1-HP-Pro-Tower-280-G9`), whereas VMware executed on a dedicated host machine.

3. **Engineering Conclusion:**
   - **Type-1 (Proxmox VE / KVM)** remains the preferred architecture for data centers and production clouds due to bare-metal hardware isolation, lack of a general-purpose host OS attack surface, and scalable multi-tenant orchestration.
   - **Type-2 (VMware Workstation)** is ideal for local desktop prototyping, application sandboxing, and developer test environments where immediate host OS integration is prioritized over bare-metal determinism.
