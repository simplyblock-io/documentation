---
title: "Air Gap Installation"
weight: 20999
---

Simplyblock can be installed in an air-gapped environment. However, the necessary images must be downloaded to
install and run the control plane, the storage nodes, and the Kubernetes CSI driver. In addition, for Kubernetes
deployments, you want to download or clone the 
[simplyblock helm repository](https://github.com/simplyblock-io/simplyblock-csi) which contains the helm charts for
Kubernetes-based storage and caching nodes, as well as the Kubernetes CSI driver.

For an air-gapped installation, we recommend an air-gapped container repository installation. Tools such as
[JFrog Artifactory](https://jfrog.com/artifactory/) or
[Sonatype Nexus](https://www.sonatype.com/products/sonatype-nexus-repository) help with the setup and management of
container images in air-gapped environments.

The general installation instructions are similar to non-air-gapped installations, with the need to update the
container download locations to point to your local container repository.

Learn more about the deployment options:

 - [Deploy into Kubernetes](../kubernetes/index.md)
 - [Deploy on Bare Metal (or virtualized) Linux](../baremetal/index.md)
 - [Deploy on AWS EC2](../aws-ec2/index.md)
