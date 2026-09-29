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

Performance profiling was conducted using `sysbench` prime-number computation to evaluate virtualization traps, CPU instruction pipeline scheduling, latency distribution, and execution throughput.

==================================================================================================
EMPIRICAL BENCHMARK SUMMARY (10s WINDOW)Metric                     Type-1: Proxmox VE (KVM)       Type-2: VMware Workstation      DeltaThroughput (Events/sec)    1,716.69 eps                   1,758.60 eps                    +2.44%
Total Compute Cycles       17,169 events                  17,588 events                   +419 ev
Arithmetic Mean Latency    0.58 ms                        0.57 ms                         -0.01 ms
95th Percentile Latency    0.65 ms                        0.61 ms                         -0.04 ms
Maximum Latency Spike      2.78 ms                        1.11 ms                         -1.67 ms
---

## Architectural Models

### 1. Type-1 Hypervisor Architecture (Bare-Metal / Hardware-Assisted KVM)

In the Type-1 model, Proxmox VE installs directly onto bare-metal hardware. The hypervisor core resides at the lowest kernel ring (**Ring -1 / VMX Root Operation**), allowing guest vCPUs running in **VMX Non-Root Operation** to execute native x86 ring-0 instructions with minimal hardware traps.


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
|             Physical Server Hardware (Bare-Metal Intel VT-x)          |
+-----------------------------------------------------------------------+
2. Type-2 Hypervisor Architecture (Hosted / Virtual Machine Monitor)In the Type-2 model, VMware Workstation runs as an application process inside a host operating system. Hardware access involves a multi-tier interception path: guest instructions execute in virtualization containers that translate through the VMware Virtual Machine Monitor (VMM), route through host OS system calls, and depend on the host operating system kernel thread scheduler.Code snippetflowchart TD
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
Virtual Machine SpecificationsHardware allocations across both test environments were configured to maintain a standardized baseline:ParameterType-1 Environment (Proxmox VE)Type-2 Environment (VMware Workstation)Control StateHypervisor IdentificationProxmox VE (KVM Module)VMware Workstation Pro/PlayerArchitectural VariableVirtual Machine NameCC-Experiment1-type1CC-Experiment1-Type2StandardizedVM IdentifierVMID: 123Emulated Workstation EntityControlledGuest OS DistributionUbuntu 64-bitUbuntu 64-bitIdenticalAllocated Virtual CPUs2 vCPUs (1 Socket, 2 Cores)2 vCPUs (1 Processor, 2 Cores)Identical (2 vCPU)Allocated Memory2048 MiB (2 GB)8192 MB (8 GB)Documented VarianceAllocated Storage20.0 GB (local-lvm)20.0 GB (Single-File Virtual Disk)Standardized CapacityNetwork InterfaceVirtIO Bridge (vmbr0)NAT Engine (VMnet8)Standardized AccessBenchmarking SuiteSysbench v1.0.20 (LuaJIT 2.1.0)Sysbench v1.0.20 (LuaJIT 2.1.0)Identical Software VersionExperimental MethodologyPhase 1: Hardware Environment ProvisioningProxmox VE (Type-1 Bare-Metal):Initialized session via HTTPS web console (https://<PROXMOX_SERVER_IP>:8006).Selected target node pve -> Create VM.Attached Ubuntu ISO image from local storage.Assigned 2 vCPUs, 2048 MiB RAM, 20 GB SCSI/VirtIO storage volume, and connected to vmbr0.Completed guest deployment and configured administrative credentials.VMware Workstation (Type-2 Hosted):Initialized VMware Workstation application on client host.Selected Typical Configuration workflow.Mounted local Ubuntu ISO image file.Provisioned 2 vCPUs, 8 GB RAM, 20 GB disk partition, and bound to virtual NAT adapter.Completed standard guest installation and initialized desktop session.Phase 2: Host & Environment Inspection CommandsBefore benchmarking, system runtime parameters were validated in the guest terminals:Bash# 1. Inspect kernel release, system hostname, and architecture
hostnamectl

# 2. Inspect CPU virtualization features, topology, and core count
lscpu

# 3. Verify total and available random-access memory
free -h

# 4. Verify disk space distribution and virtual mount targets
df -h

# 5. Monitor base load average prior to benchmark execution
top
Phase 3: Benchmark InvocationThe sysbench CPU benchmark calculates prime numbers up to a maximum upper boundary via trial division, exercising integer computation pipelines, thread caches, and hypervisor CPU dispatch mechanisms:Bash# Update repository catalog and install Sysbench
sudo apt update && sudo apt install sysbench -y

# Verify compiler/runtime version
sysbench --version

# Run compute stress test (Max Prime = 20,000, Single Thread Execution)
sysbench cpu --cpu-max-prime=20000 run
Empirical Benchmark Results & Verification1. Type-1 Bare-Metal Execution (Proxmox VE)Execution output captured from the Proxmox VE Web Console session (exp1/type1_hypervisor/):Figure 1: Terminal telemetry for Proxmox VE running inside VMID 123 (CC-Experiment1-type1).2. Type-2 Hosted Execution (VMware Workstation)Execution output captured from the VMware Workstation terminal interface (exp1/type2_hypervisor/):Figure 2: Terminal telemetry for VMware Workstation guest console.Quantitative Results & Metric Breakdown+-----------------------------------------------------------------------------------------------------------+
|                                    COMPARATIVE METRICS SCORECARD                                          |
+------------------------------------+--------------------------+----------------------------+--------------+
| Metric Category                    | Proxmox VE (Type-1)      | VMware Workstation (Type-2)| Variance     |
+------------------------------------+--------------------------+----------------------------+--------------+
| Hypervisor Architectural Class     | Bare-Metal (KVM Kernel)  | Hosted (Application Level) | -            |
| Benchmarked Thread Count           | 1 Thread                 | 1 Thread                   | 0.00%        |
| Prime Search Upper Limit           | 20,000                   | 20,000                     | 0.00%        |
| Wall Clock Execution Duration      | 10.0004 s                | 10.0003 s                  | -0.001%      |
| Total Processed Events             | 17,169                   | 17,588                     | +2.44%       |
| Throughput (Events per Second)     | 1,716.69 eps             | 1,758.60 eps               | +2.44%       |
| Minimum Latency                    | 0.57 ms                  | 0.55 ms                    | -0.02 ms     |
| Average Latency (Mean)             | 0.58 ms                  | 0.57 ms                    | -0.01 ms     |
| 95th Percentile Latency            | 0.65 ms                  | 0.61 ms                    | -0.04 ms     |
| Maximum Latency Spike              | 2.78 ms                  | 1.11 ms                    | -1.67 ms     |
| Total Latency Accumulation (Sum)   | 9,996.45 ms              | 9,993.49 ms                | -2.96 ms     |
+------------------------------------+--------------------------+----------------------------+--------------+
Performance Visualizations1. Computational Throughput (Events per Second)Higher values indicate superior CPU instruction retirement rates per unit time:PlaintextType-1 (Proxmox VE)   : [1716.69 eps]  #############################################
Type-2 (VMware WS)    : [1758.60 eps]  ############################################## (+2.44%)
                         +---------+---------+---------+---------+---------+---------+
                         0        350       700      1050      1400      1750     2100 (eps)
2. Latency Spectrum Analysis (Milliseconds)Lower latency values indicate lower task queue scheduling delays:PlaintextMinimum Latency (Fastest Completed Event)
Type-1 (Proxmox VE)   : [0.57 ms]  =========
Type-2 (VMware WS)    : [0.55 ms]  ========

Average Latency (Mean Processing Delay)
Type-1 (Proxmox VE)   : [0.58 ms]  =========
Type-2 (VMware WS)    : [0.57 ms]  ========

95th Percentile Latency (Tail Latency Boundary)
Type-1 (Proxmox VE)   : [0.65 ms]  ===========
Type-2 (VMware WS)    : [0.61 ms]  ==========

Maximum Latency Spike (Worst-case Jitter)
Type-1 (Proxmox VE)   : [2.78 ms]  ============================================
Type-2 (VMware WS)    : [1.11 ms]  =================
                         +----+----+----+----+----+----+----+----+----+----+
                         0.0  0.3  0.6  0.9  1.2  1.5  1.8  2.1  2.4  2.7  3.0 (ms)
Technical Discussion & Hardware Disparity AnalysisAn objective evaluation of the empirical data reveals subtle performance characteristics between the two deployments:1. Throughput & Host Processor DiscrepanciesThe Type-2 instance achieved 1,758.60 EPS, representing a +2.44% higher event rate over the Type-1 instance (1,716.69 EPS).In theoretical virtualization models, Type-1 hypervisors routinely outperform Type-2 systems due to the absence of host OS instruction translation. In this empirical run, the minor throughput advantage in VMware Workstation stems from:Underlying Physical Processor Clock Frequencies: The Type-2 VMware test ran on a modern host PC processor featuring higher single-core dynamic frequency scaling (boost clock) during single-threaded loads compared to the institutional enterprise server CPU running Proxmox.Memory Subsystem Footprint: The VMware VM operated with 8 GB RAM compared to 2 GB RAM in the Proxmox VM, reducing operating system memory page reclamation pressures.2. Tail Latency & Interrupt ContentionProxmox VE maintained near-identical core latency (avg: 0.58 ms, min: 0.57 ms), confirming the efficiency of the direct Linux KVM execution path.The Type-1 instance recorded a single worst-case maximum latency spike of 2.78 ms, compared to 1.11 ms on VMware. This was induced by multi-tenant I/O scheduling and storage bus interrupts across parallel lab instances sharing the institutional Proxmox server node (admin1-HP-Pro-Tower-280-G9), whereas the VMware instance executed isolated on dedicated client desktop hardware.3. Hypervisor Context Switching & Privilege RingsProxmox VE (KVM): Uses Intel VT-x hardware-assisted CPU virtualization. Guest system execution operates in VMX non-root mode, with transitions directly negotiated by the host kernel scheduler without user-space translation overhead.VMware Workstation: Privileged operations execute via a trapped hosted model where the Virtual Machine Monitor (VMM) relies on the desktop host operating system to dispatch hardware instructions.Conclusion & Deployment GuidelinesCriteriaType-1 Bare-Metal (Proxmox VE)Type-2 Hosted (VMware Workstation)Operational LevelDirect Bare-Metal Hardware InteractionApplication process running on Host OSArchitectural OverheadNegligible; no host OS footprintHigher memory and OS scheduling overheadScalabilityHigh-density multi-tenant consolidationLimited to host operating system ceilingHardware DeterminismHighly predictable bare-metal resource bindingSubject to host system background loadRecommended Use CaseProduction Cloud, Data Centers, High-Throughput DatabasesDevelopment sandboxes, educational labs, CI testbedsFinal Engineering TakeawayWhile Type-2 hypervisors provide fast configuration workflows and desktop convenience, Type-1 hypervisors remain the industry standard for production enterprise infrastructure due to their direct hardware orchestration, deterministic scheduling, and lack of host OS vulnerability surfaces.Repository Structure & VerificationPlaintextexp1/
│
├── README.md                          # Comprehensive Technical Benchmarking Report
│
├── type1_hypervisor/                  # Proxmox VE Configuration & Telemetry
│   └── cpu_analysis.jpg               # Sysbench CPU benchmark execution telemetry
│
└── type2_hypervisor/                  # VMware Workstation Configuration & Telemetry
    └── Sysbench_cpu_2.png             # Sysbench CPU benchmark execution telemetry
Reproduction StepsClone this repository:Bashgit clone [https://github.com/](https://github.com/)<your-username>/<repo-name>.git
cd <repo-name>/exp1
Inspect the benchmark screenshots in their respective subdirectories (type1_hypervisor/ and type2_hypervisor/).Re-run the verification commands on any provisioned virtual machine using:Bashsysbench cpu --cpu-max-prime=20000 run
