---
title: System Requirements
weight: 29999
---

## Hardware Architecture Support

- For the control plane, simplyblock **requires** x86-64 (AMD64 / Intel 64) compatible CPUs.
- For the storage plane, simplyblock **supports** x86-64 (AMD64 / Intel 64) or ARM64 (AArch64) compatible CPUs.

## Virtualization Support

Both Simplyblock storage nodes and control plane nodes can run on virtualization. It has been tested on plain kvm, proxmox, nitro (aws ec2) and gcp. 
For production and storage nodes, SR-IOV is required for NVMEs and NICs and dedicated cores must be exclusively assigned to the VMs (no over-provisioning).

## Deployment Models

Simplyblock allows deployment of storage nodes in disaggregated and a hyper-converged setups. The disaggregated setup requires dedicated hosts (bare metal or       vm) for the storage nodes. In hyper-converged setup within kubernetes, simplyblock storage nodes are co-located with other workloads on kubernetes workers.
The minimum system requirements below concern simplyblock only and must be dedicated to simplyblock. 

## Minimum System Requirements

The required resources (vcpu, ram, locally attached     
virtual or physical nvme devices, network bandwidth, free space on boot disk) must be exclusively reserved for and dedicated to simplyblock and are not  
available to the underlying operating system or other processes. 

| Node Type       | vCPU(s) | RAM    | Locally Attached Storage | Network Performance | Free Boot Disk | Number of Nodes | 
|-----------------|---------|--------|--------------------------|---------------------|----------------|-----------------|
| Storage Node    | 8       | 32 GB  | 1x fully dedicated NVMe  | 10 GBit/s           | 10 GB          | 3               | 
| Control Plane   | 2       | 16 GB  | 4x 3750 GB               | 1 GBit/s            | 50 GB          | 3               | 
*disaggregated mode

!!! Important note
    On Storage Nodes, the vcpus must be dedicated to Simplyblock and will be isolated from the operating system so that no kernel-space or user-space 
    processes or interrupt handlers can be scheduled on these vcpu. 

!!! Multiple Storage Nodes per Host
    It is possible and recommended to deploy multiple storage nodes per host, if the node has more than one NUMA socket or if there are more than 32 cores  
    available per socket. During deployment, simplyblock detects the underlying configuration and prepares a configuration file with the recommended deployment, 
    including the recommended amount of storage nodes per host based on the detected configuration. This file is later processed when adding the nodes to the host; 
    it can be edited, if the proposed configuration is not applicable.

## Hyperthreading

If 16 or more physical cores are available per storage node, it is highly recommended to turn off hyperthreading in the UEFI.

## NVMEs

NVMe must support 4KB native block size and should be sized in between 1.9 TiB and 7.68 TiB. While larger NVMe devices (32 and
64 TiB) are generally supported, their performance profile and rebuild time are typically not in alignment with
high-performance storage, and rebuild times are higher. Within a single cluster, all NVMEs must be of the same size.
Simplyblock is SSD-vendor agnostic but recommends NVMe devices of the same model within a cluster. This is not a hard
requirement, in particular if new (replacement) devices are faster than the installed ones.


## Network

In production, Simplyblock requires a __HA network__ for storage traffic (e.g. via LACP, Stacked Switches,
MLAG, active/active or active/passive NICs, STP or MSTP).
It is recommended to use a separate network interface, which should also be highly available, for network traffic. 

!!! Important Note
    Simplyblock requires a TCP/IP network, and all storage nodes in a cluster and hosts connected should reside in the same _VLAN_ for performance reasons.

For maximum performance and depending on the capacity of the NVMEs, a dedicated storage network bandwidth of at least 10
gb/s is recommended per NVMe and not more than 10 NVMe are recommended per socket.

## PCIe Version

PCIe 3.0 is a minimum requirement, and if possible, PCIe 4.0 or higher is recommended.

## NUMA

Simplyblock is numa-aware and can run on one or two socket systems. A minimum of one storage node per NUMA socket has to
be deployed per host for production use cases. Each NUMA socket requires directly attached NVMe and NIC to deploy a storage node.

## Operating System Requirements

The control plane nodes require one of the following: rocky, rhel or alma 9.
The storage nodes require rocky, rhel or alma 9 in the disaggregated setup.

In the hyper-converged setup, the following operating systems are supported:

| Operating System           | Versions |
|----------------------------|----------|
| Rocky                      | 9, 10    |
| RHEL                       | 9, 10    |
| Alma                       | 9, 10    |
| Ubuntu                     | ???      |
| Debian                     | ???      |
| Talos                      | ???      |

