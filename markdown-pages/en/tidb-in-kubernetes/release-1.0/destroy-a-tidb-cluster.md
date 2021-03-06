---
title: Destroy TiDB Clusters in Kubernetes
summary: Learn how to delete TiDB Cluster in Kubernetes.
aliases: ['/docs/tidb-in-kubernetes/v1.0/destroy-a-tidb-cluster/','/docs/dev/tidb-in-kubernetes/maintain/destroy-tidb-cluster/','/docs/v3.1/tidb-in-kubernetes/maintain/destroy-tidb-cluster/','/docs/v3.0/tidb-in-kubernetes/maintain/destroy-tidb-cluster/']
---

# Destroy TiDB Clusters in Kubernetes

This document describes how to deploy TiDB clusters in Kubernetes.

To destroy a TiDB cluster in Kubernetes, run the following commands:


```shell
helm delete <release-name> --purge
```

The above commands only removes the runining Pod with the data still retained. If you want the data to be deleted as well, you can use the following commands:

> **Warning:**
>
> The following commands deletes your data completely. Please be cautious.


```shell
kubectl delete pvc -n <namespace> -l app.kubernetes.io/instance=<release-name>,app.kubernetes.io/managed-by=tidb-operator
```


```shell
kubectl get pv -l app.kubernetes.io/namespace=<namespace>,app.kubernetes.io/managed-by=tidb-operator,app.kubernetes.io/instance=<release-name> -o name | xargs -I {} kubectl patch {} -p '{"spec":{"persistentVolumeReclaimPolicy":"Delete"}}'
```
