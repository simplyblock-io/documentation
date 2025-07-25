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

[see here](install-csi.md#getting-credentials) 

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



### Storage Node Installation

{% include 'kubernetes-install-storage-node-helm.md' %}
