---
title: "baremetal or vm-based"
weight: 20200
---

Bare metal and virtualized installations are installations on physically dedicated hosts or virtual machines. These
physical or virtual machines provide a Red Hat-based Linux installation version 9 (rocky, alma or rhel). Several steps, which are otherwise taken care of by the helm chart installing the csi driver, have to be performed manually on a host to which simplyblock storage is to be attached.

### Install nvme client package

```bash title="Install nvme client package"
sudo yum install nvme-cli
```

### Load the kernel module

{% include 'prepare-nvme-tcp.md' %}

### Create a pool

Before lvols can be created and connected, a storage pool is required. If a pool already exists, it can be reused. Otherwise, creating a storage
pool can be created as following:

```bash title="Create a Storage Pool"
{{ cliname }} pool add <POOL_NAME> <CLUSTER_UUID>
```

The last line of a successful storage pool creation returns the new pool id.

```plain title="Example output of creating a storage pool"
[demo@demo ~]# {{ cliname }} pool add test 4502977c-ae2d-4046-a8c5-ccc7fa78eb9a
2025-03-05 06:36:06,093: INFO: Adding pool
2025-03-05 06:36:06,098: INFO: {"cluster_id": "4502977c-ae2d-4046-a8c5-ccc7fa78eb9a", "event": "OBJ_CREATED", "object_name": "Pool", "message": "Pool created test", "caused_by": "cli"}
2025-03-05 06:36:06,100: INFO: Done
ad35b7bb-7703-4d38-884f-d8e56ffdafc6 # <- Pool Id
```

### create an lvol

### connect the lvol

### format the lvol



