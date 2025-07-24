---
title: "Deployments"
weight: 10300
---

Simplyblock is a highly flexible storage solution. It can be installed in a variety of different deployment models inside and outside of Kubernetes.

## control plane deployment

Each storage cluster requires a control plane to run. Multiple storage clusters can then be deployed and connected with a single control plane. The deployment of the control plane is independent of and preceeds the storage cluster deployments. See [Control Plane Deployment](kubernetes/prerequisites.md).

For a production deployment of the control plane, the use of 3 virtual machines on hosts independent from the storage is recommended.

## storage cluster deployment

Storage nodes can be installed directly under a plain linux operating system in a disaggregated deployment model. In this case, Linux Rocky, Alma or RHEL version 9 must be pre-installed. A k8s worker or cp must not be present: 

It is also possible to alternatively install Simplyblock storage nodes into existing k8s clusters, allowing for hyper-converged, disaggregated and hybrid deployment models (see below Kubernetes). This alternative can be chosen, if storage is mainly provisioned via CSI driver (k8s workloads).

## Client-Side Deployments

Single storage clusters can be used from within both k8s Clusters (CSI Driver), plain Linux (based on nvme-tcp) or ProxMox. See below.

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

    You may use Simplyblock straight from the Linux Operating System via the kernel nvmf-tcp
    module. /reference/supported-linux-distributions/
    [:octicons-arrow-right-24: Install on Linux](docs/deployments/baremetal/install-simplyblock-linux.md/)<br/>
    [:octicons-arrow-right-24: Supported Kernels](docs/deployments/baremetal/install-simplyblock-linux.md/)<br/>
    [:octicons-arrow-right-24: Supported Distributions](docs//reference/supported-linux-kernels.md/)<br/>

-   :material-ProxMox:{ .lg .middle } __ProxMox__

    ---
    [:octicons-arrow-right-24: Install ProxMox Driver](docs/deployments/proxmox/index.md)<br/>
    <br/>
</div>
