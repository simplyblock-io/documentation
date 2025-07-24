---
title: "Deployments"
weight: 10300
---

Simplyblock is a highly flexible storage solution. It can be installed in a variety of different deployment models inside and outside of Kubernetes.

## control plane deployment

Each storage cluster requires a control plane to run. Multiple storage clusters can then be deployed and connected with a single control plane. The deployment of the control plane is independent of and preceeds the storage cluster deployment. See [Control Plane Deployment](kubernetes/prerequisites.md).

For a production deployment of the control plane, the use of 3 virtual machines on hosts independent from the storage is recommended.

## storage cluster deployment

Storage clusters have three main deployment options: hyper-converged (into existing k8s clusters together with the primary workloads), disaggregated (on plain Linux) and hybrid (with some hyper-converged nodes and some disaggregated ones).

Hyper-converged deployments are only supported for CSI storage (not ProxMox). To use ProxMox in a hyper-converged setup, it has to be deployed to separate kvm/qemu VMs on ProxMox hypervisors using one of the disaggregated deployment options.

The disaggregated deployment has two sub-options:
- deployment on plain Linux (this option will be depricated in future releases!)
- deployment into an existing, but dedicated (for Simplyblock) k8s cluster

For the deployment of a storage cluster, it is possible to use both bare-metal hosts and VMs.

## Client-Side Deployments

Single storage clusters can be used from within both k8s Clusters (CSI Driver), plain Linux (manual or custom management based on Linux NVME-TCP or ProxMox. See below.

## System requirements and Sizing

Please read the following to prepare for the deployment: 

    [System Requirements](deployment-planning/recommendations.md)<br/>
    [Node Sizing](deployment-planning/node-sizing.md)<br/>
    [Erasure Coding Configuration](deployment-planning/erasure-coding-scheme.md)<br/>

and if deploying to either__aws__ or __gcp__:
    [Cloud Instance Types](deployment-planning/further-considerations.md)<br/>

<div class="grid cards" markdown>

-   :material-kubernetes:{ .lg .middle } __Kubernetes__

    ---

    Kubernetes deployments include AWS' EKS and GCP's GKE.

    [:octicons-arrow-right-24: Hyper-Converged Setup](kubernetes/install-simplyblock/hyper-converged.md)<br/>
    [:octicons-arrow-right-24: Disaggregated Setup](kubernetes/install-simplyblock/disaggregated.md)<br/>
    [:octicons-arrow-right-24: Hybrid Setup](kubernetes/install-simplyblock/hybrid.md)

-   :material-linux:{ .lg .middle } __Plain Linux__

    ---

    Bare-Metal deployments are either virtualized or physical host.
    [:octicons-arrow-right-24: Install Storage Cluster](baremetal/install-simplyblock.md)<br/>
    [:octicons-arrow-right-24: Install Kubernetes CSI Driver](baremetal/install-simplyblock-csi.md)<br/>

-   :material-aws:{ .lg .middle } __ProxMox__

    ---
    [:octicons-arrow-right-24: Install Storage Cluster](aws-ec2/install-simplyblock.md)<br/>
    [:octicons-arrow-right-24: Install Kubernetes CSI Driver](aws-ec2/install-simplyblock-csi.md)<br/>

-   :material-aws:{ .lg .middle } __Google Compute Engine__

    ---
    [:octicons-arrow-right-24: Install Storage Cluster](gcp/install-simplyblock.md)<br/>
    [:octicons-arrow-right-24: Install Kubernetes CSI Driver](gcp/install-simplyblock-csi.md)<br/>

-   :material-hvac-off:{ .lg .middle } __Air Gapped__

    ---

    [:octicons-arrow-right-24: General Information](air-gap/index.md)
</div>
