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
However, for nodes, which serve as storage nodes, the [following requirements](../deployment-planning/recommendations.md#operating-system-requirements-control-plane-storage-nodes) apply. Also, other requirements such as for [networking](../deployment-planning/recommendations.md), [minimum system requirements](../deployment-planning/recommendations.md) and [node sizing](../deployment-planning/recommendations.md) apply.

## Retrieving credentials and creating a pool

[see here](install-csi.md#getting-credentials) 

## Labeling Nodes

Before the helm chart is installed, it is required to label all nodes, which shall be added as storage nodes (it is possible to label additional nodes later on to add them to the storage cluster, but cluster expansion in an HA model always requires two nodes to be added in pairs).  

```bash title="Label the Kubernetes Worker Node"
kubectl label nodes <NODE_NAME> type=simplyblock-storage-plane
```

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

Anyhow, deploying simplyblock using the provided helm chart comes down to providing the four necessary
values, adding the helm chart repository, and installing the driver. In addition to the storage nodes, this will also
install the Simplyblock CSI driver for seamless integration with the Kubernetes CSI persistent storage subsystem.

To enable Kubernetes to decide where to install storage nodes, the helm chart uses a Kubernetes node label. This can be
used to mark only specific nodes to act as storage nodes, or to use all nodes for the hyper-converged or hybrid setup. 



!!! warning
    The label must be applied to all nodes that operate as part of the storage plane.

After labeling the nodes, the Helm chart can be deployed.

```bash title="Install the helm chart"
CLUSTER_UUID="<UUID>"
CLUSTER_SECRET="<SECRET>"
CNTR_ADDR="<CONTROL-PLANE-ADDR>"
POOL_NAME="<POOL-NAME>"
helm repo add simplyblock-csi https://install.simplyblock.io/helm/csi
helm repo add simplyblock-controller https://install.simplyblock.io/helm/controller
helm repo update

# Install Simplyblock CSI Driver and Storage Node API
helm install -n simplyblock \
    --create-namespace simplyblock \
    simplyblock-csi/spdk-csi \
    --set csiConfig.simplybk.uuid=<CLUSTER_UUID> \
    --set csiConfig.simplybk.ip=<CNTR_ADDR> \
    --set csiSecret.simplybk.secret=<CLUSTER_SECRET> \
    --set logicalVolume.pool_name=<POOL_NAME> \
    --set storagenode.create=true
```

```plain title="Example output of the Simplyblock Kubernetes deployment"
demo@demo ~> export CLUSTER_UUID="4502977c-ae2d-4046-a8c5-ccc7fa78eb9a"
demo@demo ~> export CLUSTER_SECRET="oal4PVNbZ80uhLMah2Bs"
demo@demo ~> export CNTR_ADDR="http://192.168.10.1/"
demo@demo ~> export POOL_NAME="test"
demo@demo ~> helm repo add simplyblock-csi https://install.simplyblock.io/helm/csi
"simplyblock-csi" has been added to your repositories
demo@demo ~> helm repo add simplyblock-controller https://install.simplyblock.io/helm/controller
"simplyblock-controller" has been added to your repositories
demo@demo ~> helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "simplyblock-csi" chart repository
...Successfully got an update from the "simplyblock-controller" chart repository
Update Complete. ⎈Happy Helming!⎈
demo@demo ~> helm install -n simplyblock --create-namespace simplyblock simplyblock-csi/spdk-csi \
  --set csiConfig.simplybk.uuid=${CLUSTER_UUID} \
  --set csiConfig.simplybk.ip=${CNTR_ADDR} \
  --set csiSecret.simplybk.secret=${CLUSTER_SECRET} \
  --set logicalVolume.pool_name=${POOL_NAME}
NAME: simplyblock-csi
LAST DEPLOYED: Wed Mar  5 15:06:02 2025
NAMESPACE: simplyblock
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Simplyblock SPDK Driver is getting deployed to your cluster.

To check CSI SPDK Driver pods status, please run:

  kubectl --namespace=simplyblock get pods --selector="release=simplyblock-csi" --watch
demo@demo ~> kubectl --namespace=simplyblock get pods --selector="release=simplyblock-csi" --watch
NAME                   READY   STATUS    RESTARTS   AGE
spdkcsi-controller-0   6/6     Running   0          30s
spdkcsi-node-tzclt     2/2     Running   0          30s
```

When the storage cluster nodes are deployed, it is recommended to apply CPU core isolation for highest performance to
the Kubernetes worker nodes that act as storage node hosts.

During the installation of the simplyblock controller, a configuration file with the system configuration has been
created. To apply core isolation to the Kubernetes worker, an SSH login to the worker node is required.


    ```

!!! notes
    Potentially, the number of CPU cores, assigned to simplyblock, should be adjusted. This is especially true for
    hyper-converged setups. Simplyblock, by default, will take all CPUs but 20% for itself. To change number of CPUs,
    the [Change the number of CPUs](#changing-the-number-of-utilized-cpu-cores) section explains the necessary steps
    which should be executed before following up here.
    
Following the installation of _tuned_, the tuning profile file must be created. The following snippet automates the
creation based on the generated configuration file.

```bash title="Generate the core isolation tuning profile"
sudo -i
SIMPLYBLOCK_CONFIG="/var/simplyblock/sn_config_file"
pip install -y yq jq
ISOLATED=$(yq '.isolated_cores' ${SIMPLYBLOCK_CONFIG} | jq -r '. | join(",")'); echo "isolcpus=${ISOLATED}"
mkdir -p /etc/tuned/realtime
cat << EOF > /etc/tuned/realtime/tuned.conf
[main]
include=latency-performance
[bootloader]
cmdline_add=isolcpus={$ISOLATED} nohz_full={$ISOLATED} rcu_nocbs={$ISOLATED}
EOF
```

Now the profile file must be applied and the worker node restarted.

!!! info
    Remember to drain potentially remaining services on the Kubernetes worker node before rebooting.

```bash title="Apply the profile and reboot"
sudo systemctl enable tuned
sudo systemctl start tuned
sudo tuned-adm profile realtime
sudo reboot 
```

#### Install the Storage Nodes

Last but not least, install the actual storage nodes into Kubernetes via Helm.

```bash
helm install -n simplyblock \
    simplyblock-controller/sb-controller \
    --set storagenode.create=true
```

#### Changing the Number of Utilized CPU Cores

!!! info
    The following section is optional and only required if additional services share the same machine, as happens in
    a hyper-converged setup.

By default, simplyblock assumes that the whole host is available to it and will configure itself to use everything
but 20% of the host. In hyper-converged setups, this assumption is not true and the number of utilized CPU cores must
be adjusted.

To adjust the number of CPU cores, an SSH login to the Kubernetes worker node is required. After logging in, the
configuration file at _/var/simplyblock/sn_config_file_ must be updated.

```bash title="Open the configuration file in VI"
vi /var/simplyblock/sn_config_file
```

Inside the configuration file, the _cpu_mask_ value must be updated to represent the number and assignment of cores to
be used by simplyblock. To create the required CPU mask, the [CPU Mask Calculator](../../reference/cpumask-calculator.md)
can be used. 

```json title="Updating the CPU Mask configuration"
{
    "nodes": [
        {
            "socket": 0,
            "cpu_mask": "0xfffbffc",
            "isolated": [
                2,
                3,
                4,
                5,
                ...
            ]
        }
    ]
}
```

After saving the file and exiting _vi_, the new configuration must be applied. For simplicity, this shell script at
[GitHub](https://github.com/simplyblock-io/simplyblock-csi/blob/master/scripts/config-gen-upgrade.sh) automates the
creation and submission of the Kubernetes job.

```bash title="Apply the configuration change"
curl -s -L https://raw.githubusercontent.com/simplyblock-io/simplyblock-csi/refs/heads/master/scripts/config-gen-upgrade.sh | bash
```


{% include 'kubernetes-install-storage-node-helm.md' %}
