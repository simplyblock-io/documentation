---
title: "Kubernetes"
weight: 20100
---

It is possible to either connect the Simplyblock CSI driver to a disaggregated deployment 

Installing simplyblock into and using it with Kubernetes requires two or more components to be installed. The number
of components depends on your deployment strategy and requirements.

For Kubernetes-related installations, simplyblock provides three deployment models: [hyper-converged (also known as
co-located)](../../architecture/concepts/hyper-converged.md),
[disaggregated](../../architecture/concepts/disaggregated.md), and a hybrid model which combines the best of the former
two.

## Installation

After making sure that all requirements are fulfilled, you can start with the installation. Follow the necessary
section depending on your chosen deployment model:

- [Hyper-Converged Setup](k8s-hyperconverged.md)
- [Disaggregated Setup](k8s-disaggregated.md)

In either case, you start with installing the control plane, before going over to the actual storage cluster and
the Kubernetes CSI driver.

