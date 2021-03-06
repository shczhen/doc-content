---
title: Tips for troubleshooting TiDB in Kubernetes
summary: Learn the commonly used tips for troubleshooting TiDB in Kubernetes.
aliases: ['/tidb-in-kubernetes/stable/troubleshoot','/docs/tidb-in-kubernetes/stable/troubleshoot/','/docs/tidb-in-kubernetes/v1.1/troubleshoot/','/docs/stable/tidb-in-kubernetes/troubleshoot/']
---

# Tips for troubleshooting TiDB in Kubernetes

This document describes the commonly used tips for troubleshooting TiDB in Kubernetes.

## Use the diagnostic mode

When a Pod is in the `CrashLoopBackoff` state, the containers in the Pod exit continually. As a result, you cannot use `kubectl exec` normally, making it inconvenient to diagnose issues.

To solve this problem, TiDB Operator provides the Pod diagnostic mode for PD, TiKV, and TiDB components. In this mode, the containers in the Pod hang directly after they are started, and will not repeatedly crash. Then you can use `kubectl exec` to connect to the Pod containers for diagnosis.

To use the diagnostic mode for troubleshooting:

1. Add an annotation to the Pod to be diagnosed:

    
    ```shell
    kubectl annotate pod ${pod_name} -n ${namespace} runmode=debug
    ```

    When the container in the Pod is restarted again, it will detect this annotation and enter the diagnostic mode.

2. Wait for the Pod to enter the Running state.

    
    ```shell
    watch kubectl get pod ${pod_name} -n ${namespace}
    ```

    Here's an example of using `kubectl exec` to get into the container for diagnosis:

    
    ```shell
    kubectl exec -it ${pod_name} -n ${namespace} -- /bin/sh
    ```

3. After finishing the diagnosis and resolving the problem, delete the Pod.

    ```shell
    kubectl delete pod ${pod_name} -n ${namespace}
    ```

After the Pod is rebuilt, it automatically returns to the normal mode.
