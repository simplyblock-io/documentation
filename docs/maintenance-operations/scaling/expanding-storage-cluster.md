---
title: "Expanding a Storage Cluster"
weight: 29001
---

Simplyblock is designed as an always-on storage solution. Hence, storage cluster expansion is an online operation
without a need for maintenance downtime.

However, every operation that changes the cluster topology comes with a set of migration tasks, moving data across
the cluster to ensure equal usage distribution. While these migration tasks are low priority and their overhead is
designed to be minimal, it is still recommended to expand the cluster at times when the storage cluster isn't under
full utilization.

To start a new storage node, follow the storage node installation according to your chosen set-up:

- [Kubernetes](../../deployments/kubernetes/install-simplyblock/index.md)
- [Bare Metal or Virtualized Linux](../../deployments/baremetal/index.md)
- [AWS EC2](../../deployments/aws-ec2/index.md)

