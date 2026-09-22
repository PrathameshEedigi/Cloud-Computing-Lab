Lab 2: Performance Analysis Using Type-2 Hypervisor – VMware Workstation

Overview:
VMware Workstation is a Type-2 hypervisor that runs on top of a host operating system. In this experiment, a virtual machine is created using VMware Workstation with the same configuration used for the Proxmox VE virtual machine. The performance of the virtual machine is then analyzed using Sysbench.

---

1. Virtual Machine Specifications

| Resource | Configuration |
| :--- | :--- |
| Virtual Machine Name | CC-Experiment1-Type2 |
| Guest Operating System | Linux (Ubuntu 64-bit) |
| vCPU Allocation | 2 vCPU (1 Processor, 2 Cores) |
| Memory (RAM) | 8 GB (8192 MB) |
| Disk Capacity | 20 GB (Single disk) |
| Network Adapter | NAT |

---

2. System Verification

2.1 Hostname and OS Verification
Command: hostnamectl

2.2 CPU Configuration Verification
Command: lscpu


2.3 Memory Configuration Verification
Command: free -h

2.4 Disk Storage Verification
Command: df -h


2.5 Real-Time System Monitoring
Command: top


---

3. CPU Performance Benchmark (Sysbench)

Installation Commands:
sudo apt update
sudo apt install sysbench -y
sysbench --version

Execution Command:
sysbench cpu --cpu-max-prime=20000 run



---

4. Observation Tables

Table 1: Type-2 Hypervisor Observation Table
| Parameter | Observation |
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
| Average Latency | 0.57 ms |

Table 2: Type-2 Hypervisor Performance Results
| Performance Metric | Result |
| :--- | :--- |
| Hypervisor | VMware Workstation |
| Hypervisor Type | Type-2 |
| CPU Configuration | 2 vCPU |
| Memory Configuration | 8 GB |
| Disk Configuration | 20 GB |
| Total Execution Time | 10.0003s |
| Total Events | 17588 |
| Events per Second | 1758.60 |
| Minimum Latency | 0.55 ms |
| Average Latency | 0.57 ms |
| Maximum Latency | 1.11 ms |

---

5. Workflow Summary
1. Launch VMware Workstation
2. Create a New Virtual Machine (Typical configuration)
3. Select Ubuntu ISO installer
4. Configure VM Name (CC-Experiment1-Type2) and location
5. Configure 20 GB virtual disk
6. Customize Hardware (2 vCPU, 8 GB RAM, NAT network)
7. Power on VM and install Ubuntu
8. Verify configuration using hostnamectl, lscpu, free -h, and df -h
9. Install Sysbench
10. Run CPU benchmark with prime limit 20000
11. Record observation metrics in tables
12. Shut down virtual machine using sudo poweroff
