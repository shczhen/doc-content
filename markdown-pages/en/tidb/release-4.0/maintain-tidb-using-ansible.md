---
title: TiDB Ansible Common Operations
summary: Learn some common operations when using TiDB Ansible to administer a TiDB cluster.
aliases: ['/docs/stable/maintain-tidb-using-ansible/','/docs/v4.0/maintain-tidb-using-ansible/','/docs/stable/how-to/deploy/orchestrated/ansible-operations/','/docs/dev/maintain-tidb-using-ansible/','/docs/dev/how-to/deploy/orchestrated/ansible-operations/','/tidb/dev/maintain-tidb-using-ansible/','/tidb/stable/maintain-tidb-using-ansible/']
---

# TiDB Ansible Common Operations

> **Warning:**
>
> For production environments, it is recommended that you [maintain TiDB using TiUP](/maintain-tidb-using-tiup.md). Since v4.0, PingCAP no longer provides support for maintaining TiDB using TiDB Ansible (deprecated). If you really need to use it for maintenance, be aware of any risk.

This guide describes the common operations when you administer a TiDB cluster using TiDB Ansible.

## Start a cluster

```bash
$ ansible-playbook start.yml
```

This operation starts all the components in the entire TiDB cluster in order, which include PD, TiDB, TiKV, and the monitoring components.

## Stop a cluster

```bash
$ ansible-playbook stop.yml
```

This operation stops all the components in the entire TiDB cluster in order, which include PD, TiDB, TiKV, and the monitoring components.

## Clean up cluster data

```
$ ansible-playbook unsafe_cleanup_data.yml
```

This operation stops the TiDB, Pump, TiKV and PD services, and cleans up the data directory of Pump, TiKV and PD.

## Destroy a cluster

```
$ ansible-playbook unsafe_cleanup.yml
```

This operation stops the cluster and cleans up the data directory.

> **Note:**
>
> If the deployment directory is a mount point, an error will be reported, but implementation results remain unaffected, so you can ignore it.
