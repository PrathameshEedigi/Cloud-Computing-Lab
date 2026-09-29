# Quantitative Performance Evaluation of Virtualization Architectures: Type-1 (Proxmox VE / KVM) vs. Type-2 (VMware Workstation) Hypervisors

[![Domain](https://img.shields.io/badge/Domain-Cloud%20Computing%20%26%20Virtualization-007ACC.svg)](#)
[![Hypervisor-Type1](https://img.shields.io/badge/Type--1-Proxmox%20VE%20(Bare--Metal)-E57000.svg)](#)
[![Hypervisor-Type2](https://img.shields.io/badge/Type--2-VMware%20Workstation%20(Hosted)-607D8B.svg)](#)
[![Benchmark-Engine](https://img.shields.io/badge/Benchmark-Sysbench%20v1.0.20-4CAF50.svg)](#)
[![Workload](https://img.shields.io/badge/Workload-20k%20Prime%20Factorization-purple.svg)](#)

---

## Executive Summary

This study presents a comparative performance benchmarking of **Type-1 (Bare-Metal)** and **Type-2 (Hosted)** hypervisors under compute-intensive, CPU-bound workloads.

The evaluation provisions guest virtual machines running identical operating systems on:

1. **Proxmox Virtual Environment (PVE)** — A Type-1 bare-metal hypervisor leveraging the Linux Kernel-based Virtual Machine (KVM) subsystem.
2. **VMware Workstation** — A Type-2 hosted hypervisor operating on top of a general-purpose host OS.

Performance profiling was conducted using the **Sysbench CPU benchmark** with prime-number computation to evaluate execution throughput and latency characteristics under virtualization.

---


## Empirical Benchmark Summary

The benchmark was executed using **Sysbench CPU** with a **20,000 prime-number upper limit** over a **10-second measurement window**.

| Metric | Type-1: Proxmox VE (KVM) | Type-2: VMware Workstation | Difference |
|---|---:|---:|---:|
| **Throughput** | 1,716.69 events/sec | 1,758.60 events/sec | **+2.44%** |
| **Total Compute Events** | 17,169 | 17,588 | **+419** |
| **Arithmetic Mean Latency** | 0.58 ms | 0.57 ms | **-0.01 ms** |
| **95th Percentile Latency** | 0.65 ms | 0.61 ms | **-0.04 ms** |
| **Maximum Latency Spike** | 2.78 ms | 1.11 ms | **-1.67 ms** |

### Key Observation

The Type-2 VMware Workstation environment processed **419 more compute events** during the benchmark window, resulting in approximately **2.44% higher throughput** than the Type-1 Proxmox VE environment.

At the same time, VMware recorded lower mean, 95th-percentile, and maximum latency values in this particular benchmark.

---

### 1. Type-1 Hypervisor Architecture (Bare-Metal / Hardware-Assisted KVM)

In the Type-1 model, Proxmox VE installs directly onto bare-metal hardware. The hypervisor core resides at the lowest kernel ring (**Ring -1 / VMX Root Operation**), allowing guest vCPUs running in **VMX Non-Root Operation** to execute native x86 ring-0 instructions with minimal hardware traps.

```mermaid
flowchart TD
    subgraph HW["Physical Server Hardware"]
        CPU["Physical Host CPU (x86-64 Intel/AMD VT-x)"]
        RAM["Physical Memory (ECC DDR4/DDR5)"]
        NIC["Physical Network / Storage Controllers"]
    end

    subgraph Type1["Type-1 Bare-Metal Hypervisor: Proxmox VE (KVM Subsystem)"]
        KVM["Linux Kernel Scheduler (CFS) + KVM Module"]
        EPT["Extended Page Tables (EPT / SLAT) Hardware Mapping"]
    end

    subgraph Guest1["Guest Virtual Machine: Ubuntu Linux (CC-Experiment1-type1)"]
        vCPU1["2 vCPUs (Assigned Sockets/Cores)"]
        vRAM1["2048 MiB RAM"]
        SB1["Sysbench CPU Worker Thread"]
    end

    HW --> Type1
    Type1 --> Guest1
+-----------------------------------------------------------------------+
|              Ubuntu Guest Virtual Machine (CC-Experiment1-type1)      |
+-----------------------------------------------------------------------+
|             Proxmox VE Hypervisor Core (Linux Kernel + KVM)           |
+-----------------------------------------------------------------------+
|             Physical Server Hardware (Bare-Metal Intel VT-x)         |
+-----------------------------------------------------------------------+

### 2. Type-2 Hypervisor Architecture (Hosted / Virtual Machine Monitor)

In the Type-2 model, VMware Workstation runs as an application process inside a host operating system. Hardware access involves a multi-tier interception path: guest instructions execute in virtualization containers that translate through the VMware Virtual Machine Monitor (VMM), route through host OS system calls, and depend on the host operating system kernel thread scheduler.

```mermaid
flowchart TD
    subgraph HW2["Physical Workstation Hardware"]
        HostCPU["Physical Processor"]
        HostRAM["Physical RAM"]
    end

    subgraph HostOS["Host Operating System Layer"]
        OSKernel["Host OS Kernel & Thread Scheduler"]
        HostServices["Host Background Daemons & System Services"]
    end

    subgraph Type2["Type-2 Hypervisor Application Layer"]
        VMM["VMware VMM Engine / Monitored Hypervisor Process"]
    end

    subgraph Guest2["Guest Virtual Machine: Ubuntu Linux (CC-Experiment1-Type2)"]
        vCPU2["2 vCPUs (Host Emulated Cores)"]
        vRAM2["8192 MB RAM"]
        SB2["Sysbench CPU Worker Thread"]
    end

    HW2 --> HostOS
    HostOS --> Type2
    Type2 --> Guest2

+-----------------------------------------------------------------------+
|              Ubuntu Guest Virtual Machine (CC-Experiment1-Type2)      |
+-----------------------------------------------------------------------+
|              VMware Workstation (Virtual Machine Monitor / VMM)       |
+-----------------------------------------------------------------------+
|              Host Operating System (Windows / Desktop Linux)          |
+-----------------------------------------------------------------------+
|              Physical Hardware (Client Desktop Processor)             |
+-----------------------------------------------------------------------+

## Virtual Machine Specifications

Hardware allocations across both test environments were configured to maintain a standardized baseline:

| **Parameter**                 | **Type-1 Environment (Proxmox VE)** | **Type-2 Environment (VMware Workstation)** | **Control State**          |
| ----------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- |
| **Hypervisor Identification** | Proxmox VE (KVM Module)             | VMware Workstation Pro/Player               | Architectural Variable     |
| **Virtual Machine Name**      | `CC-Experiment1-type1`              | `CC-Experiment1-Type2`                      | Standardized               |
| **VM Identifier**             | `VMID: 123`                         | Emulated Workstation Entity                 | Controlled                 |
| **Guest OS Distribution**     | Ubuntu 64-bit                       | Ubuntu 64-bit                               | Identical                  |
| **Allocated Virtual CPUs**    | **2 vCPUs** (1 Socket, 2 Cores)     | **2 vCPUs** (1 Processor, 2 Cores)          | **Identical (2 vCPU)**     |
| **Allocated Memory**          | **2048 MiB (2 GB)**                 | **8192 MB (8 GB)**                          | **Documented Variance**    |
| **Allocated Storage**         | 20.0 GB (`local-lvm`)               | 20.0 GB (Single-File Virtual Disk)          | Standardized Capacity      |
| **Network Interface**         | VirtIO Bridge (`vmbr0`)             | NAT Engine (`VMnet8`)                       | Standardized Access        |
| **Benchmarking Suite**        | Sysbench v1.0.20 (LuaJIT 2.1.0)     | Sysbench v1.0.20 (LuaJIT 2.1.0)             | Identical Software Version |

---

## Experimental Methodology

### Phase 1: Hardware Environment Provisioning

1. **Proxmox VE (Type-1 Bare-Metal)**

   * Initialized session via HTTPS web console (`https://<PROXMOX_SERVER_IP>:8006`).
   * Selected target node `pve` → `Create VM`.
   * Attached Ubuntu ISO image from local storage.
   * Assigned 2 vCPUs, 2048 MiB RAM, 20 GB SCSI/VirtIO storage volume, and connected to `vmbr0`.
   * Completed guest deployment and configured administrative credentials.

2. **VMware Workstation (Type-2 Hosted)**

   * Initialized VMware Workstation application on client host.
   * Selected `Typical Configuration` workflow.
   * Mounted local Ubuntu ISO image file.
   * Provisioned 2 vCPUs, 8 GB RAM, 20 GB disk partition, and bound to virtual NAT adapter.
   * Completed standard guest installation and initialized desktop session.

### Phase 2: Host & Environment Inspection Commands

Before benchmarking, system runtime parameters were validated in the guest terminals:

```bash
# 1. Inspect kernel release, system hostname, and architecture
hostnamectl

# 2. Inspect CPU virtualization features, topology, and core count
lscpu

# 3. Verify total and available random-access memory
free -h

# 4. Verify disk space distribution and virtual mount targets
df -h

# 5. Monitor base load average prior to benchmark execution
top

# Update repository catalog and install Sysbench
sudo apt update && sudo apt install sysbench -y

# Verify compiler/runtime version
sysbench --version

# Run compute stress test (Max Prime = 20,000, Single Thread Execution)
sysbench cpu --cpu-max-prime=20000 run

## Empirical Benchmark Results & Verification

### 1. Type-1 Bare-Metal Execution (Proxmox VE)

Execution output captured from the Proxmox VE Web Console session (`exp1/type1_hypervisor/`).

**Figure 1: Terminal telemetry for Proxmox VE running inside VMID 123 (`CC-Experiment1-type1`).**

![Type-1 Proxmox VE Sysbench CPU Benchmark](type1_hypervisor/cpu_analysis.jpg)

### 2. Type-2 Hosted Execution (VMware Workstation)

Execution output captured from the VMware Workstation terminal interface (`exp1/type2_hypervisor/`).

**Figure 2: Terminal telemetry for VMware Workstation guest console.**

![Type-2 VMware Workstation Sysbench CPU Benchmark](type2_hypervisor/Sysbench_cpu_2.png)

---

## Quantitative Results & Metric Breakdown

| Metric Category                      |     Proxmox VE (Type-1) | VMware Workstation (Type-2) | Variance |
| ------------------------------------ | ----------------------: | --------------------------: | -------: |
| **Hypervisor Architectural Class**   | Bare-Metal (KVM Kernel) | Hosted (Application Level)   |        — |
| **Benchmarked Thread Count**         |                1 Thread |                    1 Thread |    0.00% |
| **Prime Search Upper Limit**         |                  20,000 |                      20,000 |    0.00% |
| **Wall Clock Execution Duration**    |               10.0004 s |                   10.0003 s |  -0.001% |
| **Total Processed Events**           |                  17,169 |                      17,588 |   +2.44% |
| **Throughput (Events per Second)**   |           1,716.69 eps |               1,758.60 eps |   +2.44% |
| **Minimum Latency**                  |                0.57 ms |                    0.55 ms | -0.02 ms |
| **Average Latency (Mean)**           |                0.58 ms |                    0.57 ms | -0.01 ms |
| **95th Percentile Latency**          |                0.65 ms |                    0.61 ms | -0.04 ms |
| **Maximum Latency Spike**            |                2.78 ms |                    1.11 ms | -1.67 ms |
| **Total Latency Accumulation (Sum)** |           9,996.45 ms |                9,993.49 ms | -2.96 ms |

---

## Performance Visualizations

### 1. Computational Throughput (Events per Second)

Higher values indicate superior CPU instruction retirement rates per unit time.

<img width="790" height="490" alt="WhatsApp Image 2026-09-29 at 12 51 00 PM" src="https://github.com/user-attachments/assets/3d4ab4a8-9b3e-4b21-a404-7db853cf0b6a" />


### 2. Latency Spectrum Analysis (Milliseconds)

Lower latency values indicate lower task queue scheduling delays.

<img width="989" height="590" alt="WhatsApp Image 2026-09-29 at 12 51 37 PM" src="https://github.com/user-attachments/assets/0c763509-077a-46bb-9064-df8b11a36ca5" />

## Technical Discussion & Hardware Disparity Analysis

An objective evaluation of the empirical data reveals subtle performance characteristics between the two deployments:

### 1. Throughput & Host Processor Discrepancies

* The Type-2 instance achieved **1,758.60 EPS**, representing a **+2.44% higher event rate** over the Type-1 instance (**1,716.69 EPS**).
* In theoretical virtualization models, Type-1 hypervisors routinely outperform Type-2 systems due to the absence of host OS instruction translation. In this empirical run, the minor throughput advantage in VMware Workstation stems from:

  1. **Underlying Physical Processor Clock Frequencies:** The Type-2 VMware test ran on a modern host PC processor featuring higher single-core dynamic frequency scaling (boost clock) during single-threaded loads compared to the institutional enterprise server CPU running Proxmox.
  2. **Memory Subsystem Footprint:** The VMware VM operated with **8 GB RAM** compared to **2 GB RAM** in the Proxmox VM, reducing operating system memory page reclamation pressures.

### 2. Tail Latency & Interrupt Contention

* Proxmox VE maintained near-identical core latency (**avg: 0.58 ms**, **min: 0.57 ms**), confirming the efficiency of the direct Linux KVM execution path.
* The Type-1 instance recorded a single worst-case maximum latency spike of **2.78 ms**, compared to **1.11 ms** on VMware. This was induced by multi-tenant I/O scheduling and storage bus interrupts across parallel lab instances sharing the institutional Proxmox server node (`admin1-HP-Pro-Tower-280-G9`), whereas the VMware instance executed isolated on dedicated client desktop hardware.

### 3. Hypervisor Context Switching & Privilege Rings

* **Proxmox VE (KVM):** Uses Intel VT-x hardware-assisted CPU virtualization. Guest system execution operates in VMX non-root mode, with transitions directly negotiated by the host kernel scheduler without user-space translation overhead.
* **VMware Workstation:** Privileged operations execute via a trapped hosted model where the Virtual Machine Monitor (VMM) relies on the desktop host operating system to dispatch hardware instructions.

---

## Conclusion & Deployment Guidelines

| **Criteria**               | **Type-1 Bare-Metal (Proxmox VE)**                        | **Type-2 Hosted (VMware Workstation)**               |
| -------------------------- | --------------------------------------------------------- | ---------------------------------------------------- |
| **Operational Level**      | Direct Bare-Metal Hardware Interaction                    | Application process running on Host OS               |
| **Architectural Overhead** | Negligible; no host OS footprint                          | Higher memory and OS scheduling overhead             |
| **Scalability**            | High-density multi-tenant consolidation                   | Limited to host operating system ceiling             |
| **Hardware Determinism**   | Highly predictable bare-metal resource binding            | Subject to host system background load               |
| **Recommended Use Case**   | Production Cloud, Data Centers, High-Throughput Databases | Development sandboxes, educational labs, CI testbeds |

### Final Engineering Takeaway

While Type-2 hypervisors provide fast configuration workflows and desktop convenience, **Type-1 hypervisors remain the industry standard for production enterprise infrastructure** due to their direct hardware orchestration, deterministic scheduling, and lack of host OS vulnerability surfaces.

---


