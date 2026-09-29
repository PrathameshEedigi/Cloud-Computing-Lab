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
