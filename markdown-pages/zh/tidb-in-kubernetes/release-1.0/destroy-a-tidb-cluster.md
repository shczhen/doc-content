---
title: 销毁 Kubernetes 上的 TiDB 集群
summary: 介绍如何销毁 Kubernetes 集群上的 TiDB 集群。
aliases: ['/docs-cn/tidb-in-kubernetes/v1.0/destroy-a-tidb-cluster/','/docs-cn/dev/tidb-in-kubernetes/maintain/destroy-tidb-cluster/','/docs-cn/v3.1/tidb-in-kubernetes/maintain/destroy-tidb-cluster/','/docs-cn/v3.0/tidb-in-kubernetes/maintain/destroy-tidb-cluster/']
---

# 销毁 Kubernetes 上的 TiDB 集群

本文描述了如何销毁 Kubernetes 集群上的 TiDB 集群。

要销毁 TiDB 集群，执行以下命令：


```shell
helm delete <release-name> --purge
```

上述命令只是删除运行的 Pod，数据仍然会保留。如果你不再需要那些数据，可以通过下面命令清除数据：

> **警告：**
>
> 下列命令会彻底删除数据，务必考虑清楚再执行。


```shell
kubectl delete pvc -n <namespace> -l app.kubernetes.io/instance=<release-name>,app.kubernetes.io/managed-by=tidb-operator
```


```shell
kubectl get pv -l app.kubernetes.io/namespace=<namespace>,app.kubernetes.io/managed-by=tidb-operator,app.kubernetes.io/instance=<release-name> -o name | xargs -I {} kubectl patch {} -p '{"spec":{"persistentVolumeReclaimPolicy":"Delete"}}'
```
