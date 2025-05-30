---
title: "Hyper-Converged Setup"
weight: 50000
---

<!-- include: install control plane documentation -->
{% include 'install-control-plane.md' %}

## Storage Plane Installation

Caching nodes, like storage nodes, require huge page memory to hold the internal state. Huge pages should be 2MiB in
size, and a minimum of 4096 huge pages should be allocated at boot time of the operating system.

```bash
demo@worker-1 ~> sudo sysctl -w vm.nr_hugepages=4096
```

!!! info
    To see how huge pages can be pre-reserved at boot time, see the node sizing documentation section on  
    [Huge Pages](../../deployment-planning/node-sizing.md#memory-requirements).


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
