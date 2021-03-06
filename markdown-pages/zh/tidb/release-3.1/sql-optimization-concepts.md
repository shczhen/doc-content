---
title: SQL 优化流程简介
aliases: ['/docs-cn/v3.1/sql-optimization-concepts/','/docs-cn/v3.1/reference/performance/sql-optimizer-overview/']
---

# SQL 优化流程简介

在 TiDB 中，SQL 优化过程分为逻辑优化和物理优化两个阶段。

## 逻辑优化简介

逻辑优化是基于规则的优化，对输入的逻辑执行计划按顺序应用一些优化规则，从而使整个逻辑执行计划变得更好。这些优化规则包括：

- 列裁剪
- 投影消除
- 关联子查询去关联
- Max/Min 消除
- 谓词下推
- 分区裁剪
- TopN 和 Limit 下推

## 物理优化简介

物理优化是基于代价的优化，为上一阶段产生的逻辑执行计划制定物理执行计划。这一阶段中，优化器会为逻辑执行计划中的每个算子选择具体的物理实现。逻辑算子的不同物理实现有着不同的时间复杂度、资源消耗和物理属性等。在这个过程中，优化器会根据数据的统计信息来确定不同物理实现的代价，并选择整体代价最小的物理执行计划。

逻辑执行计划是一个树形结构，每个节点对应 SQL 中的一个逻辑算子。同样的，物理执行计划也是一个树形结构，每个节点对应 SQL 中的一个物理算子。逻辑算子只描述这个算子的功能，而物理算子则描述了完成这个功能的具体算法。对于同一个逻辑算子，可能有多个物理算子实现，比如 `LogicalAggregate`，它的实现可以是采用哈希算法的 `HashAggregate`，也可以是流式的 `StreamAggregate`。不同的物理算子具有不同的物理属性，也对其子节点有着不同的物理属性的要求。物理属性包括数据的顺序和分布等。TiDB 中现在只考虑了数据的顺序。
