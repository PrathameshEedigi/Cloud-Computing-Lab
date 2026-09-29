# Performance Analysis of Type-1 (Proxmox VE) vs. Type-2 (VMware Workstation) Hypervisors

## 1. Objective & Scope
The objective of this experiment is to evaluate and compare the computational performance of Type-1 (bare-metal) and Type-2 (hosted) hypervisors. Both virtual environments execute an identical compute-bound Sysbench CPU workload calculating prime numbers up to 20,000 across a 10-second test window.

---

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


### Type-2 Hypervisor (VMware Workstation Terminal)
<img width="1316" height="696" alt="Sysbench_cpu" src="https://github.com/user-attachments/assets/0081b412-c467-478a-834f-6276d5b7d502" />


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
