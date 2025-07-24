---
title: "Deployments"
weight: 10300
---

Simplyblock is a highly flexible storage solution. It can be installed in a variety of different deployment models inside and outside of Kubernetes.

## control plane deployment

Each storage cluster requires a control plane to run. Multiple storage clusters can then be deployed and connected with a single control plane. The deployment of the control plane is independent of and preceeds the storage cluster deployments. See [Control Plane Deployment](install-simplyblock/install-cp.md).

For a production deployment of the control plane, the use of 3 virtual machines on hosts independent from the storage is recommended.

## storage cluster deployment

Storage nodes can be installed directly under Linux Rocky, Alma or RHEL version 9. A k8s worker must not be present in this case and a minimum os image is sufficient: [Install Simplyblock Storage Nodes](install-simplyblock/install-sn.md). This setup is not suitable for kubernetes [hyper-converged](../architecture/concepts/hyper-converged.md) deployments.

It is also possible to __alternatively__ install Simplyblock storage nodes into existing k8s clusters, allowing for both hyper-converged, disaggregated and hybrid deployment models (see below Kubernetes). This alternative can be chosen, if storage is mainly provisioned via CSI driver (k8s workloads).

## Client-Side Deployments

Single storage clusters can be used from within both k8s Clusters (CSI Driver), plain Linux (based on nvme-tcp) or ProxMox. See below.

## System requirements and Sizing

Please read the following to prepare for the deployment: 

[System Requirements](deployment-planning/recommendations.md)
[Node Sizing](deployment-planning/node-sizing.md)
[Erasure Coding Configuration](deployment-planning/erasure-coding-scheme.md)
[Air Gapped Installation](docs/deployments/deployment-planning/air-gapped.md)

and if deploying to either aws or gcp:
[Cloud Instance Types](deployment-planning/further-considerations.md)

<div class="grid cards" markdown>

-   :material-kubernetes:{ .lg .middle } __Kubernetes__

    ---

    Kubernetes deployments include AWS' EKS and GCP's GKE.

    [:octicons-arrow-right-24: Install CSI Driver Only](kubernetes/install-csi.md)<br/>    
    [:octicons-arrow-right-24: Hyper-Converged Setup](kubernetes/k8s-hyperconverged.md)<br/>
    [:octicons-arrow-right-24: Disaggregated Setup under Kubernetes](kubernetes/k8s-disaggregated.md)<br/>

-   :material-linux:{ .lg .middle } __Plain Linux__

    ---

    You may use Simplyblock straight from the Linux Operating System via the kernel nvmf-tcp
    module. /reference/supported-linux-distributions/
    [:octicons-arrow-right-24: Install on Linux](baremetal/index.md/)<br/>

-   :material-ProxMox:{ .lg .middle } __ProxMox__

    ---
    [:octicons-arrow-right-24: Install ProxMox Driver](proxmox/index.md)<br/>
    <br/>
</div>
