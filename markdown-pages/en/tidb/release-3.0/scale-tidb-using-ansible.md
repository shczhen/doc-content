---
title: Scale the TiDB Cluster Using TiDB Ansible
summary: Use TiDB Ansible to increase/decrease the capacity of a TiDB/TiKV/PD node.
aliases: ['/docs/v3.0/scale-tidb-using-ansible/','/docs/v3.0/how-to/scale/with-ansible/','/docs/op-guide/ansible-deployment-scale/','/docs/dev/how-to/maintain/scale/with-ansible/']
---

# Scale the TiDB Cluster Using TiDB Ansible

The capacity of a TiDB cluster can be increased or decreased without affecting the online services.

> **Warning:**
>
> In decreasing the capacity, if your cluster has a mixed deployment of other services, do not perform the following procedures. The following examples assume that the removed nodes have no mixed deployment of other services.

Assume that the topology is as follows:

| Name | Host IP | Services |
| ---- | ------- | -------- |
| node1 | 172.16.10.1 | PD1 |
| node2 | 172.16.10.2 | PD2 |
| node3 | 172.16.10.3 | PD3, Monitor |
| node4 | 172.16.10.4 | TiDB1 |
| node5 | 172.16.10.5 | TiDB2 |
| node6 | 172.16.10.6 | TiKV1 |
| node7 | 172.16.10.7 | TiKV2 |
| node8 | 172.16.10.8 | TiKV3 |
| node9 | 172.16.10.9 | TiKV4 |

## Increase the capacity of a TiDB/TiKV node

For example, if you want to add two TiDB nodes (node101, node102) with the IP addresses `172.16.10.101` and `172.16.10.102`, take the following steps:

1. Edit the `inventory.ini` file and the `hosts.ini` file, and append the node information.

    - Edit the `inventory.ini` file:

        ```ini
        [tidb_servers]
        172.16.10.4
        172.16.10.5
        172.16.10.101
        172.16.10.102

        [pd_servers]
        172.16.10.1
        172.16.10.2
        172.16.10.3

        [tikv_servers]
        172.16.10.6
        172.16.10.7
        172.16.10.8
        172.16.10.9

        [monitored_servers]
        172.16.10.1
        172.16.10.2
        172.16.10.3
        172.16.10.4
        172.16.10.5
        172.16.10.6
        172.16.10.7
        172.16.10.8
        172.16.10.9
        172.16.10.101
        172.16.10.102

        [monitoring_servers]
        172.16.10.3

        [grafana_servers]
        172.16.10.3
        ```

        Now the topology is as follows:

        | Name | Host IP | Services |
        | ---- | ------- | -------- |
        | node1 | 172.16.10.1 | PD1 |
        | node2 | 172.16.10.2 | PD2 |
        | node3 | 172.16.10.3 | PD3, Monitor |
        | node4 | 172.16.10.4 | TiDB1 |
        | node5 | 172.16.10.5 | TiDB2 |
        | **node101** | **172.16.10.101**|**TiDB3** |
        | **node102** | **172.16.10.102**|**TiDB4** |
        | node6 | 172.16.10.6 | TiKV1 |
        | node7 | 172.16.10.7 | TiKV2 |
        | node8 | 172.16.10.8 | TiKV3 |
        | node9 | 172.16.10.9 | TiKV4 |

    - Edit the `hosts.ini` file:

        ```ini
        [servers]
        172.16.10.1
        172.16.10.2
        172.16.10.3
        172.16.10.4
        172.16.10.5
        172.16.10.6
        172.16.10.7
        172.16.10.8
        172.16.10.9
        172.16.10.101
        172.16.10.102
        [all:vars]
        username = tidb
        ntp_server = pool.ntp.org
        ```

2. Initialize the newly added node.

    1. Configure the SSH mutual trust and sudo rules of the deployment machine on the central control machine:

        
        ```bash
        ansible-playbook -i hosts.ini create_users.yml -l 172.16.10.101,172.16.10.102 -u root -k
        ```

    2. Install the NTP service on the deployment target machine:

        
        ```bash
        ansible-playbook -i hosts.ini deploy_ntp.yml -u tidb -b
        ```

    3. Initialize the node on the deployment target machine:

        
        ```bash
        ansible-playbook bootstrap.yml -l 172.16.10.101,172.16.10.102
        ```

    > **Note:**
    >
    > If an alias is configured in the `inventory.ini` file, for example, `node101 ansible_host=172.16.10.101`, use `-l` to specify the alias when executing `ansible-playbook`. For example, `ansible-playbook bootstrap.yml -l node101,node102`. This also applies to the following steps.

3. Deploy the newly added node:

    
    ```bash
    ansible-playbook deploy.yml -l 172.16.10.101,172.16.10.102
    ```

4. Start the newly added node:

    
    ```bash
    ansible-playbook start.yml -l 172.16.10.101,172.16.10.102
    ```

5. Update the Prometheus configuration and restart the cluster:

    
    ```bash
    ansible-playbook rolling_update_monitor.yml --tags=prometheus
    ```

6. Monitor the status of the entire cluster and the newly added node by opening a browser to access the monitoring platform: `http://172.16.10.3:3000`.

You can use the same procedure to add a TiKV node. But to add a PD node, some configuration files need to be manually updated.

## Increase the capacity of a PD node

For example, if you want to add a PD node (node103) with the IP address `172.16.10.103`, take the following steps:

1. Edit the `inventory.ini` file and append the node information to the end of the `[pd_servers]` group:

    ```ini
    [tidb_servers]
    172.16.10.4
    172.16.10.5

    [pd_servers]
    172.16.10.1
    172.16.10.2
    172.16.10.3
    172.16.10.103

    [tikv_servers]
    172.16.10.6
    172.16.10.7
    172.16.10.8
    172.16.10.9

    [monitored_servers]
    172.16.10.4
    172.16.10.5
    172.16.10.1
    172.16.10.2
    172.16.10.3
    172.16.10.103
    172.16.10.6
    172.16.10.7
    172.16.10.8
    172.16.10.9

    [monitoring_servers]
    172.16.10.3

    [grafana_servers]
    172.16.10.3
    ```

    Now the topology is as follows:

    | Name | Host IP | Services |
    | ---- | ------- | -------- |
    | node1 | 172.16.10.1 | PD1 |
    | node2 | 172.16.10.2 | PD2 |
    | node3 | 172.16.10.3 | PD3, Monitor |
    | **node103** | **172.16.10.103** | **PD4** |
    | node4 | 172.16.10.4 | TiDB1 |
    | node5 | 172.16.10.5 | TiDB2 |
    | node6 | 172.16.10.6 | TiKV1 |
    | node7 | 172.16.10.7 | TiKV2 |
    | node8 | 172.16.10.8 | TiKV3 |
    | node9 | 172.16.10.9 | TiKV4 |

2. Initialize the newly added node:

    
    ```bash
    ansible-playbook bootstrap.yml -l 172.16.10.103
    ```

3. Deploy the newly added node:

    
    ```bash
    ansible-playbook deploy.yml -l 172.16.10.103
    ```

4. Login the newly added PD node and edit the starting script:

    
    ```bash
    {deploy_dir}/scripts/run_pd.sh
    ```

    1. Remove the `--initial-cluster="xxxx" \` configuration.

        > **Note:**
        >
        > You cannot add the `#` character at the beginning of the line. Otherwise, the following configuration cannot take effect.

    2. Add `--join="http://172.16.10.1:2379" \`. The IP address (`172.16.10.1`) can be any of the existing PD IP address in the cluster.

    3. Start the PD service in the newly added PD node:

        
        ```bash
        {deploy_dir}/scripts/start_pd.sh
        ```

        > **Note:**
        >
        > Before start, you need to ensure that the `health` status of the newly added PD node is "true", using [PD Control](/pd-control.md). Otherwise, the PD service might fail to start and an error message `["join meet error"] [error="etcdserver: unhealthy cluster"]` is returned in the log.

    4. Use `pd-ctl` to check whether the new node is added successfully:

        
        ```bash
        ./pd-ctl -u "http://172.16.10.1:2379"
        ```

        > **Note:**
        >
        > `pd-ctl` is a command used to check the number of PD nodes.

5. Start the monitoring service:

    
    ```bash
    ansible-playbook start.yml -l 172.16.10.103
    ```

    > **Note:**
    >
    > If you use an alias (inventory_name), use the `-l` option to specify the alias.

6. Update the cluster configuration:

    
    ```bash
    ansible-playbook deploy.yml
    ```

7. Restart Prometheus, and enable the monitoring of PD nodes used for increasing the capacity:

    
    ```bash
    ansible-playbook stop.yml --tags=prometheus
    ansible-playbook start.yml --tags=prometheus
    ```

8. Monitor the status of the entire cluster and the newly added node by opening a browser to access the monitoring platform: `http://172.16.10.3:3000`.

> **Note:**
>
> The PD Client in TiKV caches the list of PD nodes. Currently, the list is updated only if the PD leader is switched or the TiKV server is restarted to load the latest configuration. To avoid TiKV caching an outdated list, there should be at least two existing PD members in the PD cluster after increasing or decreasing the capacity of a PD node. If this condition is not met, transfer the PD leader manually to update the list of PD nodes.

## Decrease the capacity of a TiDB node

For example, if you want to remove a TiDB node (node5) with the IP address `172.16.10.5`, take the following steps:

1. Stop all services on node5:

    
    ```bash
    ansible-playbook stop.yml -l 172.16.10.5
    ```

2. Edit the `inventory.ini` file and remove the node information:

    ```ini
    [tidb_servers]
    172.16.10.4
    #172.16.10.5  # the removed node

    [pd_servers]
    172.16.10.1
    172.16.10.2
    172.16.10.3

    [tikv_servers]
    172.16.10.6
    172.16.10.7
    172.16.10.8
    172.16.10.9

    [monitored_servers]
    172.16.10.4
    #172.16.10.5  # the removed node
    172.16.10.1
    172.16.10.2
    172.16.10.3
    172.16.10.6
    172.16.10.7
    172.16.10.8
    172.16.10.9

    [monitoring_servers]
    172.16.10.3

    [grafana_servers]
    172.16.10.3
    ```

    Now the topology is as follows:

    | Name | Host IP | Services |
    | ---- | ------- | -------- |
    | node1 | 172.16.10.1 | PD1 |
    | node2 | 172.16.10.2 | PD2 |
    | node3 | 172.16.10.3 | PD3, Monitor |
    | node4 | 172.16.10.4 | TiDB1 |
    | **node5** | **172.16.10.5** | **TiDB2 removed** |
    | node6 | 172.16.10.6 | TiKV1 |
    | node7 | 172.16.10.7 | TiKV2 |
    | node8 | 172.16.10.8 | TiKV3 |
    | node9 | 172.16.10.9 | TiKV4 |

3. Update the Prometheus configuration and restart the cluster:

    
    ```bash
    ansible-playbook rolling_update_monitor.yml --tags=prometheus
    ```

4. Monitor the status of the entire cluster by opening a browser to access the monitoring platform: `http://172.16.10.3:3000`.

## Decrease the capacity of a TiKV node

For example, if you want to remove a TiKV node (node9) with the IP address `172.16.10.9`, take the following steps:

1. Remove the node from the cluster using `pd-ctl`:

    1. View the store ID of node9:

        
        ```bash
        ./pd-ctl -u "http://172.16.10.1:2379" -d store
        ```

    2. Remove node9 from the cluster, assuming that the store ID is 10:

        
        ```bash
        ./pd-ctl -u "http://172.16.10.1:2379" -d store delete 10
        ```

2. Use `pd-ctl` to check whether the node is successfully removed:

    
    ```bash
    ./pd-ctl -u "http://172.16.10.1:2379" -d store 10
    ```

    > **Note:**
    >
    > It takes some time to remove the node. If the status of the node you remove becomes Tombstone, then this node is successfully removed.

3. After the node is successfully removed, stop the services on node9:

    
    ```bash
    ansible-playbook stop.yml -l 172.16.10.9
    ```

4. Edit the `inventory.ini` file and remove the node information:

    ```ini
    [tidb_servers]
    172.16.10.4
    172.16.10.5

    [pd_servers]
    172.16.10.1
    172.16.10.2
    172.16.10.3

    [tikv_servers]
    172.16.10.6
    172.16.10.7
    172.16.10.8
    #172.16.10.9  # the removed node

    [monitored_servers]
    172.16.10.4
    172.16.10.5
    172.16.10.1
    172.16.10.2
    172.16.10.3
    172.16.10.6
    172.16.10.7
    172.16.10.8
    #172.16.10.9  # the removed node

    [monitoring_servers]
    172.16.10.3

    [grafana_servers]
    172.16.10.3
    ```

    Now the topology is as follows:

    | Name | Host IP | Services |
    | ---- | ------- | -------- |
    | node1 | 172.16.10.1 | PD1 |
    | node2 | 172.16.10.2 | PD2 |
    | node3 | 172.16.10.3 | PD3, Monitor |
    | node4 | 172.16.10.4 | TiDB1 |
    | node5 | 172.16.10.5 | TiDB2 |
    | node6 | 172.16.10.6 | TiKV1 |
    | node7 | 172.16.10.7 | TiKV2 |
    | node8 | 172.16.10.8 | TiKV3 |
    | **node9** | **172.16.10.9** | **TiKV4 removed** |

5. Update the Prometheus configuration and restart the cluster:

    
    ```bash
    ansible-playbook rolling_update_monitor.yml --tags=prometheus
    ```

6. Monitor the status of the entire cluster by opening a browser to access the monitoring platform: `http://172.16.10.3:3000`.

## Decrease the capacity of a PD node

For example, if you want to remove a PD node (node2) with the IP address `172.16.10.2`, take the following steps:

1. Remove the node from the cluster using `pd-ctl`:

    1. View the name of node2:

        
        ```bash
        ./pd-ctl -u "http://172.16.10.1:2379" -d member
        ```

    2. Remove node2 from the cluster, assuming that the name is pd2:

        
        ```bash
        ./pd-ctl -u "http://172.16.10.1:2379" -d member delete name pd2
        ```

2. Use Grafana or `pd-ctl` to check whether the node is successfully removed:

    
    ```bash
    ./pd-ctl -u "http://172.16.10.1:2379" -d member
    ```

3. After the node is successfully removed, stop the services on node2:

    
    ```bash
    ansible-playbook stop.yml -l 172.16.10.2
    ```

    > **Note:**
    >
    > In this example, you can only stop the PD service on node2. If there are any other services deployed with the IP address `172.16.10.2`, use the `-t` option to specify the service (such as `-t tidb`).

4. Edit the `inventory.ini` file and remove the node information:

    ```ini
    [tidb_servers]
    172.16.10.4
    172.16.10.5

    [pd_servers]
    172.16.10.1
    #172.16.10.2  # the removed node
    172.16.10.3

    [tikv_servers]
    172.16.10.6
    172.16.10.7
    172.16.10.8
    172.16.10.9

    [monitored_servers]
    172.16.10.4
    172.16.10.5
    172.16.10.1
    #172.16.10.2  # the removed node
    172.16.10.3
    172.16.10.6
    172.16.10.7
    172.16.10.8
    172.16.10.9

    [monitoring_servers]
    172.16.10.3

    [grafana_servers]
    172.16.10.3
    ```

    Now the topology is as follows:

    | Name | Host IP | Services |
    | ---- | ------- | -------- |
    | node1 | 172.16.10.1 | PD1 |
    | **node2** | **172.16.10.2** | **PD2 removed** |
    | node3 | 172.16.10.3 | PD3, Monitor |
    | node4 | 172.16.10.4 | TiDB1 |
    | node5 | 172.16.10.5 | TiDB2 |
    | node6 | 172.16.10.6 | TiKV1 |
    | node7 | 172.16.10.7 | TiKV2 |
    | node8 | 172.16.10.8 | TiKV3 |
    | node9 | 172.16.10.9 | TiKV4 |

5. Update the cluster configuration:

    
    ```bash
    ansible-playbook deploy.yml
    ```

6. Restart Prometheus, and disable the monitoring of PD nodes used for increasing the capacity:

    
    ```bash
    ansible-playbook stop.yml --tags=prometheus
    ansible-playbook start.yml --tags=prometheus
    ```

7. To monitor the status of the entire cluster, open a browser to access the monitoring platform: `http://172.16.10.3:3000`.

> **Note:**
>
> The PD Client in TiKV caches the list of PD nodes. Currently, the list is updated only if the PD leader is switched or the TiKV server is restarted to load the latest configuration. To avoid TiKV caching an outdated list, there should be at least two existing PD members in the PD cluster after increasing or decreasing the capacity of a PD node. If this condition is not met, transfer the PD leader manually to update the list of PD nodes.
