---
title: Deploy a TiDB Cluster from Homebrew
summary: Install TiDB using the Homebrew package manager.
aliases: ['/docs/v3.0/deploy-tidb-from-homebrew/','/docs/v3.0/how-to/get-started/deploy-tidb-from-homebrew/','/docs/v3.0/how-to/get-started/local-cluster/install-from-homebrew/']
---

# Deploy a TiDB Cluster from Homebrew

TiDB on Homebrew supports a minimal installation mode of the tidb-server **without** the tikv-server or pd-server. This is useful for development environments, since you can test your application's compatibility with TiDB without needing to deploy a full TiDB platform.

This installation method is supported on macOS, Linux and Windows (via [WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10)).

> **Note:**
>
> Internally this installation uses goleveldb as the storage engine. It is much slower than TiKV, and any benchmarks will be unreliable.

## Installation steps

After first [installing Homebrew](https://brew.sh/), TiDB can be installed with:


```bash
brew tap pingcap/brew &&
brew install tidb-server
```

To start the tidb-server:


```bash
tidb-server
```

The tidb-server does not bundle any clients.  To use with the MySQL client:


```bash
brew install mysql-client &&
mysql -h 127.0.0.1 -P4000 -uroot
```

## Startup item

TiDB can start automatically on login. This is currently only supported on macOS:


```bash
brew services start tidb-server
```
