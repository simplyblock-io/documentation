---
title: "Hyper-Converged Setup"
weight: 50000
---

In the hyper-converged or hybrid deployment, csi driver (node-part) and storage nodes are at least partially co-located on the same hosts (k8s worker nodes). 

!!! info
    However, this does not mean that each worker node with the csi driver node-part has to become a storage node. This is  
    rather defined by a node label. Also, it is possible to add dedicated storage worker nodes to the same kubernetes cluster 
    for a hybrid deployment model. 

 As for the plain CSI driver installation, the control plane must be present and a storage cluster must have been created. 
The storage cluster will however not have any storage nodes attached yet.

## CSI Driver and Storage Node System Requirements

System requirements for CSI-only (node part) installation can be found [here](install-csi.md#csi-driver-system-requirements).
However, for nodes, which serve as storage nodes, the [following requirements](../deployment-planning/recommendations.md#operating-system-requirements-control-plane-storage-nodes) apply. Also, other requirements such as for [networking](), [minimum system requirements]() and [node sizing]() apply.

## Retrieving credentials and creating a pool

These are the [same steps](install-csi.md#getting-credentials) as for installation of the csi driver only.

## Labeling Nodes

Before the helm chart is installed, it is required to label all nodes, which shall be added as storage nodes (it is possible to label additional nodes later on to add them to the storage cluster, but cluster expansion in an HA model always requires two nodes to be added in pairs).  



## Networking Configuration

Multiple ports are required to be opened on storage node hosts. ports used with the same source and target networks (vlans) will not require firewall settings. port openings may be required between control plane and storage network as those will be frequently on different vlans. 

| Service                     | Direction | Source / Target Network | Port(s)   | Protocol(s) |
|-----------------------------|-----------|-------------------------|-----------|-------------|
| ICMP                        | ingress   | control / storage       | -         | ICMP        |
| Storage node API            | ingress   | control / storage       | 5000      | TCP         |
| spdk-http-proxy             | ingress   | control / storage       | 8080-8180 | TCP         |
| hublvol-nvmf-subsys-port    | ingress   | storage / storage       | 9030-9059 | TCP         |
| internal-nvmf-subsys-port   | ingress   | storage / storage       | 9060-9099 | TCP         |
| lvol-nvmf-subsys-port       | ingress   | storage / storage       | 9100-9200 | TCP         |
| SSH                         | ingress   | admin / storage         | 22        | TCP         |
| FoundationDB                | egress    | storage / control       | 4500      | TCP         |
| Graylog                     | egress    | storage / control       | 12202     | TCP         |

## Installing CSI Driver and Storage Nodes via Helm

Caching nodes, like storage nodes, require huge page memory to hold the internal state. Huge pages should be 2MiB in
size, and a minimum of 4096 huge pages should be allocated at boot time of the operating system.

```bash
demo@worker-1 ~> sudo sysctl -w vm.nr_hugepages=4096
```

!!! info
    To see how huge pages can be pre-reserved at boot time, see the node sizing documentation section on  
    [Huge Pages](../deployment-planning/node-sizing.md#memory-requirements).


```bash
demo@worker-1 ~> sudo systemctl restart kubelet
```

```bash
demo@worker-1 ~> kubectl describe node worker-1.kubernetes-cluster.local | \
    grep hugepages-2Mi
```

```plain
demo@demo ~> kubectl describe node worker-1.kubernetes-cluster.local | \
    grep hugepages-2Mi
  hugepages-2Mi:      9440Mi
  hugepages-2Mi:      9440Mi
  hugepages-2Mi      0 (0%)    0 (0%)
```

```bash
demo@worker-1 ~> sudo yum install -y nvme-cli
demo@worker-1 ~> sudo modprobe nvme-tcp
demo@worker-1 ~> sudo modprobe nbd
```

### Firewall Configuration (SP)

| Service                     | Direction | Source / Target Network | Port(s)   | Protocol(s) |
|-----------------------------|-----------|-------------------------|-----------|-------------|
| ICMP                        | ingress   | control                 | -         | ICMP        |
| Storage node API            | ingress   | storage                 | 5000      | TCP         |
| spdk-http-proxy             | ingress   | storage, control        | 8080-8180 | TCP         |
| hublvol-nvmf-subsys-port    | ingress   | storage, control        | 9030-9059 | TCP         |
| internal-nvmf-subsys-port   | ingress   | storage, control        | 9060-9099 | TCP         |
| lvol-nvmf-subsys-port       | ingress   | storage, control        | 9100-9200 | TCP         |
| SSH                         | ingress   | storage, control, admin | 22        | TCP         |
| Docker Daemon Remote Access | ingress   | storage, control        | 2375      | TCP         |
|                             |           |                         |           |             |
| FoundationDB                | egress    | storage                 | 4500      | TCP         |
| Docker Daemon Remote Access | egress    | storage, control        | 2375      | TCP         |
| Docker Swarm Remote Access  | egress    | storage, control        | 2377      | TCP         |
| Docker Overlay Network      | egress    | storage, control        | 4789      | UDP         |
| Docker Network Discovery    | egress    | storage, control        | 7946      | TCP / UDP   |
| Graylog                     | egress    | control                 | 12202     | TCP         |

### Storage Node Installation

{% include 'kubernetes-install-storage-node-helm.md' %}
