---
title: Overview of TiDB Operator
summary: Learn the overview of TiDB Operator.
aliases: ['/docs/tidb-in-kubernetes/v1.0/tidb-operator-overview/','/docs/dev/tidb-in-kubernetes/tidb-operator-overview/','/docs/v3.1/tidb-in-kubernetes/tidb-operator-overview/','/docs/v3.0/tidb-in-kubernetes/tidb-operator-overview/']
---

# TiDB Operator Overview

TiDB Operator is an automatic operation system for TiDB clusters in Kubernetes. It provides a full management life-cycle for TiDB including deployment, upgrades, scaling, backup, fail-over, and configuration changes. With TiDB Operator, TiDB can run seamlessly in the Kubernetes clusters deployed on a public or private cloud.

> **Note:**
>
> You can only deploy one TiDB Operator in a Kubernetes cluster.

The corresponding relationship between TiDB Operator and TiDB versions is as follows:

| TiDB versions | Compatible TiDB Operator versions |
|:---|:---|
| dev               | dev                 |
| TiDB >= 5.4       | 1.3                 |
| 5.1 <= TiDB < 5.4 | 1.3 (Recommended), 1.2      |
| 3.0 <= TiDB < 5.1 | 1.3 (Recommended), 1.2, 1.1 |
| 2.1 <= TiDB < v3.0| 1.0 (End of support)       |

## TiDB Operator architecture

![TiDB Operator Overview](https://download.pingcap.com/images/tidb-in-kubernetes/tidb-operator-overview-1.0.png)

`TidbCluster` is a custom resource defined by CRD (`CustomResourceDefinition`) and is used to describe the desired state of the TiDB cluster. The following components are responsible for the orchestration and scheduling logic in a TiDB cluster:

* `tidb-controller-manager` is a set of custom controllers in Kubernetes. These controllers constantly compare the desired state recorded in the `TidbCluster` object with the actual state of the TiDB cluster. They adjust the resources in Kubernetes to drive the TiDB cluster to meet the desired state;
* `tidb-scheduler` is a Kubernetes scheduler extension that injects the TiDB specific scheduling policies to the Kubernetes scheduler.

In addition, TiDB Operator also provides `tkctl`, the command-line interface for TiDB clusters in Kubernetes. It is used for cluster operations and troubleshooting cluster issues.

![TiDB Operator Control Flow](https://download.pingcap.com/images/tidb-in-kubernetes/tidb-operator-control-flow-1.0.png)

The diagram above is the analysis of the control flow of TiDB Operator. Because TiDB clusters also need components such as monitoring, initialization, scheduled backup, Binlog and so on, TiDB Operator encapsulates the definition of these components in the Helm chart. The overall control process is as follows:

1. The user creates a `TidbCluster` object and a corresponding series of Kubernetes-native objects through Helm, such as a `CronJob` that performs scheduled backups;
2. TiDB Operator watches `TidbCluster` and other related objects, and constantly adjust the `StatefulSet` and `Service` objects of PD, TiKV, and TiDB based on the actual state of the cluster;
3. Kubernetes' native controller creates, updates, and deletes the corresponding `Pod` based on objects such as `StatefulSet`, `Deployment`, and `CronJob`;
4. In the `Pod` declaration of PD, TiKV, and TiDB, the `tidb-scheduler` scheduler is specified. `tidb-scheduler` applies the specific scheduling logic of TiDB when scheduling the corresponding `Pod`.

Based on the above declarative control flow, TiDB Operator automatically performs health check and fault recovery for the cluster nodes. You can easily modify the `TidbCluster` object declaration to perform operations such as deployment, upgrade and scaling.

## Manage TiDB clusters using TiDB Operator

TiDB Operator provides several ways to deploy TiDB clusters in Kubernetes:

+ For test environment:

    - [Minikube](deploy-tidb-from-kubernetes-minikube.md): Deploy TiDB clusters in a local Minikube environment using TiDB Operator
    - [GKE](deploy-tidb-from-kubernetes-gke.md): Deploy TiDB clusters on GKE using TiDB Operator

+ For production environment:

    + On public cloud:
        - [Deploy TiDB on AWS EKS](deploy-on-aws-eks.md)
        - [Deploy TiDB on GCP GKE (beta)](deploy-on-gcp-gke.md)
        - [Deploy TiDB on Alibaba Cloud ACK](deploy-on-alibaba-cloud.md)

    - In an existing Kubernetes cluster:

        First install TiDB Operator in a Kubernetes cluster according to [Deploy TiDB Operator in Kubernetes](deploy-tidb-operator.md), then deploy your TiDB clusters according to [Deploy TiDB in General Kubernetes](deploy-on-general-kubernetes.md).

        You also need to adjust the configuration of the Kubernetes cluster based on [Prerequisites for TiDB in Kubernetes](prerequisites.md) and configure the local PV for your Kubernetes cluster to achieve low latency of local storage for TiKV according to [Local PV Configuration](configure-storage-class.md#local-pv-configuration).

Before deploying TiDB on any of the above two environments, you can always refer to [TiDB Cluster Configuration Document](configure-a-tidb-cluster.md) to customize TiDB configurations.

After the deployment is complete, see the following documents to use, operate, and maintain TiDB clusters in Kubernetes:

+ [Access the TiDB Cluster](access-tidb.md)
+ [Scale TiDB Cluster](scale-a-tidb-cluster.md)
+ [Upgrade TiDB Cluster](upgrade-a-tidb-cluster.md#upgrade-the-version-of-tidb-cluster)
+ [Change the Configuration of TiDB Cluster](upgrade-a-tidb-cluster.md#change-the-configuration-of-tidb-cluster)
+ [Backup and Restore using Helm Charts](backup-and-restore-using-helm-charts.md)
+ [Automatic Failover](use-auto-failover.md)
+ [Monitor a TiDB Cluster in Kubernetes](monitor-a-tidb-cluster.md)
+ [Collect TiDB Logs in Kubernetes](collect-tidb-logs.md)
+ [Maintain Kubernetes Nodes that Hold the TiDB Cluster](maintain-a-kubernetes-node.md)

When a problem occurs and the cluster needs diagnosis, you can:

+ See [TiDB FAQs in Kubernetes](faq.md) for any available solution;
+ See [Troubleshoot TiDB in Kubernetes](troubleshoot.md) to shoot troubles.

TiDB in Kubernetes provides a dedicated command-line tool `tkctl` for cluster management and auxiliary diagnostics. Meanwhile, some of TiDB's tools are used differently in Kubernetes. You can:

+ Use `tkctl` according to [`tkctl` Guide](use-tkctl.md );
+ See [Tools in Kubernetes](tidb-toolkit.md) to understand how TiDB tools are used in Kubernetes.

Finally, when a new version of TiDB Operator is released, you can refer to [Upgrade TiDB Operator](upgrade-tidb-operator.md) to upgrade to the latest version.
