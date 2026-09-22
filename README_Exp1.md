Performance Analysis of Virtualization: Type-1 (Proxmox VE) vs Type-2 (VMware Workstation) Hypervisors

Overview:
This laboratory experiment evaluates and compares the performance characteristics of Type-1 (bare-metal) and Type-2 (hosted) hypervisors. Proxmox VE is deployed as the Type-1 hypervisor running directly on physical hardware, while VMware Workstation serves as the Type-2 hypervisor running on top of a host operating system. Virtual machines running Ubuntu are configured on both platforms with identical CPU allocations, and their processing performance is benchmarked using Sysbench.

---

PART A: Performance Analysis Using Type-1 Hypervisor – Proxmox VE

1. Virtual Machine Specifications (Type-1)

| Parameter | Configuration |
| :--- | :--- |
| Hypervisor | Proxmox VE |
| Hypervisor Type | Type-1 |
| Node | Selected Proxmox Node (pve) |
| VM Name | CC-Experiment1-Type1 |
| Guest Operating System | Linux (Ubuntu 64-bit) |
| ISO Image | ubuntu-22.04.iso |
| CPU Allocation | 2 vCPU (1 Socket, 2 Cores) |
| Memory Allocation | 2048 MiB (2 GB RAM) |
| Disk Storage | 20 GB (local-lvm) |
| Network Bridge | vmbr0 (VirtIO) |

2. System Verification (Type-1)

Hostname and OS Verification:
Command: hostnamectl

CPU Configuration Verification:
Command: lscpu

Memory Configuration Verification:
Command: free -h

Disk Storage Verification:
Command: df -h

Real-Time System Monitoring:
Command: top

3. CPU Performance Benchmark (Type-1)

Sysbench Execution Command:
sysbench cpu --cpu-max-prime=20000 run


4. Observation Table (Type-1 Hypervisor)

| Parameter / Metric | Observation / Result |
| :--- | :--- |
| Hypervisor | Proxmox VE |
| Hypervisor Type | Type-1 |
| Guest Operating System | Ubuntu |
| CPU Allocation | 2 vCPU |
| Memory Allocation | 2 GB |
| Disk Allocation | 20 GB |
| Total Execution Time | 10.0006s |
| Total Events | 16903 |
| Events per Second | 1689.43 |
| Minimum Latency | 0.57 ms |
| Average Latency | 0.59 ms |
| Maximum Latency | 1.09 ms |

5. Workflow Summary (Type-1)

Connect to the designated network and open the Proxmox VE web interface at https://<PROXMOX_SERVER_IP>:8006
Log in using the authorized credentials and realm
Select Datacenter -> pve and launch the Create VM wizard
Configure VM settings: General, OS (Ubuntu ISO), System, Disk (20 GB), CPU (2 vCPUs), Memory (2048 MiB), and Network (vmbr0)
Review and finalize configuration to instantiate the VM
Start the virtual machine and launch the noVNC Console
Complete Ubuntu OS setup and reboot the VM
Log in via terminal and verify system architecture with hostnamectl, lscpu, free -h, and df -h
Install the Sysbench benchmarking utility
Execute the CPU benchmark (sysbench cpu --cpu-max-prime=20000 run)
Record performance statistics in the observation table
Monitor hypervisor-level metrics from the Proxmox summary dashboard and gracefully power off the VM

---

PART B: Performance Analysis Using Type-2 Hypervisor – VMware Workstation

1. Virtual Machine Specifications (Type-2)

| Parameter | Configuration |
| :--- | :--- |
| Hypervisor | VMware Workstation |
| Hypervisor Type | Type-2 |
| VM Name | CC-Experiment1-Type2 |
| Guest Operating System | Linux (Ubuntu 64-bit) |
| ISO Image | ubuntu-22.04.iso |
| CPU Allocation | 2 vCPU (1 Processor, 2 Cores) |
| Memory Allocation | 8 GB (8192 MB) |
| Disk Storage | 20 GB (Single disk) |
| Network Adapter | NAT |

2. System Verification (Type-2)

Hostname and OS Verification:
Command: hostnamectl

CPU Configuration Verification:
Command: lscpu

Memory Configuration Verification:
Command: free -h

Disk Storage Verification:
Command: df -h

Real-Time System Monitoring:
Command: top

3. CPU Performance Benchmark (Type-2)

Sysbench Execution Command:
sysbench cpu --cpu-max-prime=20000 run


4. Observation Table (Type-2 Hypervisor)

| Parameter / Metric | Observation / Result |
| :--- | :--- |
| Hypervisor | VMware Workstation |
| Hypervisor Type | Type-2 |
| Guest Operating System | Ubuntu |
| CPU Allocation | 2 vCPU |
| Memory Allocation | 8 GB |
| Disk Allocation | 20 GB |
| Total Execution Time | 10.0003s |
| Total Events | 17588 |
| Events per Second | 1758.60 |
| Minimum Latency | 0.55 ms |
| Average Latency | 0.57 ms |
| Maximum Latency | 1.11 ms |

5. Workflow Summary (Type-2)

Launch VMware Workstation
Create a New Virtual Machine (Typical configuration)
Select Ubuntu ISO installer
Configure VM Name (CC-Experiment1-Type2) and location
Configure 20 GB virtual disk
Customize Hardware (2 vCPU, 8 GB RAM, NAT network)
Power on VM and install Ubuntu
Verify configuration using hostnamectl, lscpu, free -h, and df -h
Install Sysbench utility
Run CPU benchmark with prime limit 20000
Record observation metrics in tables
Shut down virtual machine using sudo poweroff

---

PART C: Comparative Performance Analysis (Type-1 vs Type-2)

1. Side-by-Side Benchmark Comparison Table

| Metric / Parameter | Type-1 Hypervisor (Proxmox VE) | Type-2 Hypervisor (VMware Workstation) |
| :--- | :--- | :--- |
| Architecture Level | Bare-Metal (Direct hardware access) | Hosted (Runs on host OS) |
| vCPU Allocation | 2 vCPU | 2 vCPU |
| Memory Allocation | 2 GB | 8 GB |
| Disk Allocation | 20 GB | 20 GB |
| Benchmark Test | Sysbench CPU (Max Prime: 20000) | Sysbench CPU (Max Prime: 20000) |
| Total Execution Time | 10.0006s | 10.0003s |
| Total Number of Events | 16903 | 17588 |
| Events per Second | 1689.43 | 1758.60 |
| Minimum Latency | 0.57 ms | 0.55 ms |
| Average Latency | 0.59 ms | 0.57 ms |
| Maximum Latency | 1.09 ms | 1.11 ms |

2. Performance Inferences & Analysis

Throughput and Execution Capacity:
VMware Workstation processed 17,588 total events at a rate of 1758.60 events/second, whereas Proxmox VE completed 16,903 total events at 1689.43 events/second[cite: 1, 2]. The difference in event throughput reflects variation in memory overhead allocation (8 GB allocated to VMware VM vs 2 GB to Proxmox VM) alongside differences in the underlying host physical processor specifications.

Latency Characteristics:
Both hypervisors maintained sub-millisecond execution times[cite: 1, 2]. Proxmox VE exhibited an average latency of 0.59 ms (ranging between 0.57 ms and 1.09 ms)[cite: 2], while VMware Workstation delivered an average latency of 0.57 ms (ranging between 0.55 ms and 1.11 ms)[cite: 1]. Proxmox VE demonstrated a tighter maximum latency ceiling (1.09 ms vs 1.11 ms)[cite: 1, 2], reflecting bare-metal scheduling stability without host OS interrupt interference.

Architectural Trade-offs:
Type-1 Hypervisor (Proxmox VE) operates directly on bare metal without an intermediary OS layer, minimizing resource contention, eliminating host OS overhead, and providing predictable enterprise virtualization.
Type-2 Hypervisor (VMware Workstation) operates on top of a general-purpose host OS, which provides setup convenience and flexible desktop integration, but shares physical compute resources with host-level processes.
