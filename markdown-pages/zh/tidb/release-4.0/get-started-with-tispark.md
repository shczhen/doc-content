---
title: TiSpark 快速上手
aliases: ['/docs-cn/stable/get-started-with-tispark/','/docs-cn/v4.0/get-started-with-tispark/','/docs-cn/stable/how-to/get-started/tispark/']
---

# TiSpark 快速上手

为了让大家快速体验 [TiSpark](/tispark-overview.md)，通过 TiDB Ansible 安装的 TiDB 集群中默认已集成 Spark、TiSpark jar 包及 TiSpark sample data。

## 部署信息

- Spark 默认部署在 TiDB 实例部署目录下 spark 目录中
- TiSpark jar 包默认部署在 Spark 部署目录 jars 文件夹下：`spark/jars/tispark-${name_with_version}.jar`
- TiSpark 示例数据和导入脚本可点击 [TiSpark 示例数据](http://download.pingcap.org/tispark-sample-data.tar.gz)下载。

   
   ```
   tispark-sample-data/
   ```

## 环境准备

### 在 TiDB 实例上安装 JDK

在 [Oracle JDK 官方下载页面](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-javase8-2177648.html) 下载 JDK 1.8 当前最新版，本示例中下载的版本为 `jdk-8u141-linux-x64.tar.gz`。

解压并根据您的 JDK 部署目录设置环境变量，编辑 `~/.bashrc` 文件，比如：


```bash
export JAVA_HOME=/home/pingcap/jdk1.8.0_144 &&
export PATH=$JAVA_HOME/bin:$PATH
```

验证 JDK 有效性：

```bash
java -version
```

```
java version "1.8.0_144"
Java(TM) SE Runtime Environment (build 1.8.0_144-b01)
Java HotSpot(TM) 64-Bit Server VM (build 25.144-b01, mixed mode)
```

### 导入样例数据

假设 TiDB 集群已启动，其中一台 TiDB 实例服务 IP 为 192.168.0.2，端口为 4000，用户名为 root, 密码为空。


```bash
wget http://download.pingcap.org/tispark-sample-data.tar.gz && \
tar -zxvf tispark-sample-data.tar.gz && \
cd tispark-sample-data
```

修改 `sample_data.sh` 中 TiDB 登录信息，比如：


```bash
mysql --local-infile=1 -h 192.168.0.2 -P 4000 -u root < dss.ddl
```

执行脚本


```bash
./sample_data.sh
```

> **注意：**
>
> 执行脚本的机器上需要安装 MySQL client，CentOS 用户可通过 `yum -y install mysql`来安装。

登录 TiDB 并验证数据包含 `TPCH_001` 库及以下表：


```bash
mysql -uroot -P4000 -h192.168.0.2
```


```sql
show databases;
```

```
+--------------------+
| Database           |
+--------------------+
| INFORMATION_SCHEMA |
| PERFORMANCE_SCHEMA |
| TPCH_001           |
| mysql              |
| test               |
+--------------------+
5 rows in set (0.00 sec)
```


```sql
use TPCH_001;
```

```
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
```


```sql
show tables;
```

```
+--------------------+
| Tables_in_TPCH_001 |
+--------------------+
| CUSTOMER           |
| LINEITEM           |
| NATION             |
| ORDERS             |
| PART               |
| PARTSUPP           |
| REGION             |
| SUPPLIER           |
+--------------------+
8 rows in set (0.00 sec)
```

## 使用范例

进入 spark 部署目录启动 spark-shell:


```bash
cd spark &&
bin/spark-shell
```

然后像使用原生 Spark 一样查询 TiDB 表：

```shell
scala> spark.sql("select count(*) from lineitem").show
```

结果为

```
+--------+
|count(1)|
+--------+
|   60175|
+--------+
```

下面执行另一个复杂一点的 Spark SQL:

```shell
scala> spark.sql(
      """select
        |   l_returnflag,
        |   l_linestatus,
        |   sum(l_quantity) as sum_qty,
        |   sum(l_extendedprice) as sum_base_price,
        |   sum(l_extendedprice * (1 - l_discount)) as sum_disc_price,
        |   sum(l_extendedprice * (1 - l_discount) * (1 + l_tax)) as sum_charge,
        |   avg(l_quantity) as avg_qty,
        |   avg(l_extendedprice) as avg_price,
        |   avg(l_discount) as avg_disc,
        |   count(*) as count_order
        |from
        |   lineitem
        |where
        |   l_shipdate <= date '1998-12-01' - interval '90' day
        |group by
        |   l_returnflag,
        |   l_linestatus
        |order by
        |   l_returnflag,
        |   l_linestatus
      """.stripMargin).show
```

结果为：

```
+------------+------------+---------+--------------+--------------+
|l_returnflag|l_linestatus|  sum_qty|sum_base_price|sum_disc_price|
+------------+------------+---------+--------------+--------------+
|           A|           F|380456.00|  532348211.65|505822441.4861|
|           N|           F|  8971.00|   12384801.37| 11798257.2080|
|           N|           O|742802.00| 1041502841.45|989737518.6346|
|           R|           F|381449.00|  534594445.35|507996454.4067|
+------------+------------+---------+--------------+--------------+
(续)
-----------------+---------+------------+--------+-----------+
       sum_charge|  avg_qty|   avg_price|avg_disc|count_order|
-----------------+---------+------------+--------+-----------+
 526165934.000839|25.575155|35785.709307|0.050081|      14876|
  12282485.056933|25.778736|35588.509684|0.047759|        348|
1029418531.523350|25.454988|35691.129209|0.049931|      29181|
 528524219.358903|25.597168|35874.006533|0.049828|      14902|
-----------------+---------+------------+--------+-----------+
```

更多样例请参考 [`pingcap/tispark-test`](https://github.com/pingcap/tispark-test/tree/master/tpch/sparksql)
