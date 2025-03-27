---
title: NUMA Considerations
---

Modern multi-socket servers use a memory architecture called
[NUMA (Non-Uniform Memory Access)](https://en.wikipedia.org/wiki/Non-uniform_memory_access){:target="_blank" rel="noopener"}.
In a NUMA system, each CPU socket has its own local memory and I/O paths. Accessing local resources is faster than
reaching across sockets to remote memory or devices.

Linux is NUMA-aware, but performance can still degrade if workloads or I/O devices aren't correctly aligned with the CPU
topology. This matters especially when working with high-throughput components like network interface cards (NICs).

Simplyblock requires NUMA-aware configuration in multi-socket systems. To ensure optimal performance, each CPU socket
must have a dedicated NIC and dedicated NVMe devices. These NICs and NVMe devices must be installed in PCI-e slots that
are physically connected to their respective CPUs. This setup ensures low-latency, high-bandwidth data paths between
the backing storage devices, the storage software and the network.

## Local NUMA Configuration

```bash title="How to check the NUMA configuration"
lscpu | grep -i numa
```

```plain title="Example output of the NUMA configuration"
root@demo:~# lscpu | grep -i numa
NUMA node(s):                         2
NUMA node0 CPU(s):                    0-31
NUMA node1 CPU(s):                    32-63
```

```bash
yum install pciutils
```

```bash
#!/bin/bash

for i in  /sys/class/*/*/device; do
    pci=$(basename "$(readlink $i)")
    if [ -e $i/numa_node ]; then
        echo "NUMA Node: `cat $i/numa_node` ($i): `lspci -s $pci`" ;
    fi
done | sort
```
