---
title: spark学习之旅(一)
date: 2019-03-18 08:20:57
id: 20190318082057
categories: spark
tags: 
    - spark
    - 机器学习
---
<!-- more -->

# 1.简介

Apache Spark是一个围绕速度、易用性和复杂分析构建的大数据处理框架。最初在2009年由加州大学伯克利分校的AMBLab开发，并于2010年成为Apache的开源项目之一。
# 2.Hadoop和Spark
hadoop中,MapReduce是一路计算的优秀解决方案，不过对于需要多路计算和算法的用例来说，并非很高效。数据处理流程中的每一步都需要一个Map阶段和一个Reduce阶段，而且如果要利用这一解决方案，需要将所有用例都转换成MapReduce模式。再下一步开始之前上一步的作业输出数据必须要存储到分布式文件系统中。如果想要完成比较复杂的动作，就必须将一系列的MapReduce作业串联起来然后顺序执行这些作业。

而spark则允许程序开发者使用有向无环图（DAG）开发复杂的多部数据管道。而且还支持跨有向无环图的内存数据共享，以便不同的作业可以共同处理一个数据。

我们应该将Spark看作是Hadoop MapReduce的一个替代品，而不是Hadoop的替代品。其意图并非是替代Hadoop，而是为了提供一个管理不同的大数据用例和需求的全面且统一的解决方案。
# 3.Spark特性
Spark通过在数据处理过程中成本更低的洗牌（Shuffle）方式，将Map Reduce提升到一个更高的层次。利用内存数据存储和接近实时的处理能力，Spark比其他的大数据处理技术的性能要快很多倍。

Spark还支持大数据查询的延迟计算，这可以帮助优化大数据处理流程中的处理步骤。Spark还提供高级的API以提升开发者的生产力，除此之外还为大数据解决方案提供一致的体系架构模型。

Spark将中间结果保存在内存中而不是将其写入磁盘，当需要多次处理同一数据集时，这一点特别实用。Spark的设计初衷就是既可以在内存中又可以在磁盘上工作的执行引擎。当内存中的数据不适用时，Spark操作符就会执行外部操作。Spark可以用于处理大于集群内容容量总和的数据集。

Spark会尝试在内存中存储尽可能多的数据然后将其写入磁盘。它可以将某个数据集的一部分存入内存而剩下部分存入磁盘。开发者需要根据数据和用例评估对内存的需要。Spark的性能有事得益于这种内存中的数据存储

Spark的其他特性包括：
- 支持比Map和Reduce更多的函数。
- 优化仍以操作算子图（operator graphs）。
- 可以帮助优化整体数据处理流程的大数据查询的延迟计算。
- 提供简明、一致的Scala、Java和Python API。
- 提供交互式Scala和Python Shell。目前暂不支持Java。

Spark是用Scala程序设计语言编写而成，运行于Java虚拟机（JVM）环境之上。目前支持如下程序设计语言编写Spark应用。
- Scala
- Java
- Python
- Clojure
- R

# 4.Spark生态系统
除了Spark核心API之外，Spark生态系统中还包括其他附加库，可以在大数据分析和机器学习领域提供更多的功能。

这些库包括：
- Spark Streaming：
  - Spark Streaming基于微批量方式的计算和处理，可以用于处理实时的流数据。他使用DStream，简单来说就是一个弹性分布式数据集（RDD）系列，处理实时数据。
- Spark SQL:
  - Spark SQL可以通过JDBC API将Spark数据集暴露出去，而且还可以用传统的BI和可视化工具在Spark数据上执行类似SQL的查询。用户开可以用Spark SQL对不同格式的数据（如JSON，Parquet以及数据库等）执行ETL，将其转化，然后暴露给特定的查询。
- Spark MLlib：
  - ML李白是一个可扩展的Spark机器学习库，由通用的学习算法和工具组成，包括二元分类、线性回归、聚类、协同过滤、梯度下降以及底层优化原语。
  - Spark GraphX：
    - GraphX是用于图计算和并行图计算的新的（alpha）Spark API。通过引入弹性分布式的属性图（Resilient Distributed Property Graph），一种顶点和边都带有属性的有向多重图，扩展了Spark RDD。为了支持图计算，GraphX暴露了一个基础操作符集合（如subgraph、joinVertices和aggregateMessages）和一个经过优化的Pregel API变体。此外，GraphX还包括一个持续增长的用于简化图分析任务的图算法和构建器集合。

除了这些库以外，还有一些其他的库，如BlinkDB和Tachyon。

# 5.Spark体系架构
Spark体系架构包括如下三个主要组件：
- 数据存储
- API
- 管理框架

## 数据存储
Spark用HDFS文件系统存储数据。他可以存储任何兼容于Hadoop的数据源，包括HDFS、HBase、Cassandra等。
## API
利用API，应用开发者可以用标准的API接口创建基于Spark的应用。Spark提供Scala、Python、Java三种程序设计语言的API。

下面是三种语言Spark API的网站链接：
- [Scala API](http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.package)
- [Java](http://spark.apache.org/docs/latest/api/java/index.html)
- [Python](http://spark.apache.org/docs/latest/api/python/index.html)

## 资源管理：
Spark既可以部署在一个单独的服务器也可以部署在像Mesos或YARN这样的分布式计算框架之上。
# 6.弹性分布式数据集（RDD）
弹性分布式数据集（基于Matei的研究论文）或RDD是Spark框架中的核心概念。可以将RDD视作数据库中的一张表。其中可以保存任何类型的数据。Spark将数据存储在不同分区上的RDD之中。

RDD可以帮助重新安排计算并优化数据处理过程。此外，他还具有容错性，因为RDD知道如何重新创建和重新计算数据集。RDD是不可变的。你可以用变换（Transformation）修改RDD，但是这个变换所返沪的是一个全新的RDD，而原有的RDD任然保持不变。

RDD支持两种类型的操作：
- 变换（Transformation）
- 行动（Action)

### 变换
变换的返回值是一个新的RDD集合，而不是单个值。调用一个变换方法，不会有任何求值计算，他只获取一个RDD作为参数，然后返回一个新的EDD。
### 行动
行动操作计算并返回一个新的值。当在一个RDD对象上调用行动函数时，会在这一时刻计算全部的数据处理查询并返回结果值。

行动操作包括：reduce、collect、count、first、take、countByKey以及foreach。