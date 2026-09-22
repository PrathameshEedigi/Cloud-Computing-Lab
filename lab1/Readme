Lab 1: Performance Analysis Using Type-1 Hypervisor – Proxmox VE

Overview:
Proxmox VE is an open-source bare-metal Type-1 hypervisor deployed directly on physical server hardware. In this experiment, a virtual machine running Ubuntu is configured and deployed using the Proxmox VE web interface. The system resources are verified, and CPU performance is benchmarked using Sysbench to analyze bare-metal virtualization efficiency.

---

1. Virtual Machine Specifications

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

---

2. System Verification

2.1 Hostname and OS Verification
Command: hostnamectl
![Hostname Output](hostname.png)

2.2 CPU Configuration Verification
Command: lscpu
![CPU Configuration](lscpu.png)

2.3 Memory Configuration Verification
Command: free -h
![Memory Output](free.png)

2.4 Disk Storage Verification
Command: df -h
![Disk Storage](df.png)

2.5 Real-Time System Monitoring
Command: top
![Resource Utilization](top.png)

---

3. CPU Performance Benchmark (Sysbench)

Installation Commands:
sudo apt update
sudo apt install sysbench -y
sysbench --version

Execution Command:
sysbench cpu --cpu-max-prime=20000 run

![Sysbench CPU Analysis Result](cpu_analysis.jpg)

---

4. Observation & Results

Observation Table – Type-1 Hypervisor
| Parameter | Observation |
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

---

5. Workflow Summary

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
