本篇为翻译，原文：[Hadoop vs Spark vs Flink – Big Data Frameworks Comparison](https://data-flair.training/blogs/hadoop-vs-spark-vs-flink/)

## 目标

在这篇对比Hadoop、Spark、Flink教程中，我们将学习Apache Hadoop、Spark、Flink的功能对比。这3种大数据技术迅速占领了IT市场，并在其中担任了不同的重要角色。你会了解到因为Hadoop的局限性而诞生Spark，以及Spark的局限性而诞生Flink。在这里，你会详细了解到Spark、Flink与Hadoop的区别。

那么，让我们开始对比Hadoop、Spark、 Flink。

## 对比Hadoop、Spark、 Flink

在学习对比Hadoop、Spark、Flink的区别之前，我们先来复习一下这3种技术的基础知识。

- [Apache Flink教程——大数据的4G时代](http://data-flair.training/blogs/apache-flink-comprehesive-guide-tutorial-for-beginners/)
- [Apache Spark教程——大数据的3G时代](http://data-flair.training/blogs/apache-spark-introduction-spark-comprehensive-tutorial/)
- [大数据Hadoop教程](http://data-flair.training/blogs/hadoop-introduction-comprehensive-tutorial-guide-beginners/)

那么现在我们就来展开Hadoop、Spark、Flink的功能对比之旅。

### 1. 数据处理

- **Hadoop:** Apache Hadoop是为批处理而构建。它把输入中的大数据集，一次性全部拿出来进行处理并产生结果。批处理在处理大数据量的数据时非常有效。由于数据的大小和系统的计算能力，输出会有一定的延迟。
- **Spark:** Apache Spark也是[Hadoop生态系统](http://data-flair.training/blogs/hadoop-ecosystem-components/)的一部分，它本质上也是一个批处理系统，但它也支持流处理。
- **Flink:** Apache Flink为流处理和批处理提供了一个单一的运行时。

### 2. 流引擎

- **Hadoop:** Map-reduce是一个面向批处理工具。它在输入时，一次性获取大量的数据集，对其进行处理并产生结果。
- **Spark:** [Apache Spark Streaming](http://data-flair.training/blogs/apache-spark-streaming-comprehensive-guide/)以``micro-batches``处理数据流。每个批次包含了在批次期间到达的事件的集合。但对于我们需要处理大量实时数据流并提供实时结果的用例来说，这还不够。
- **Flink:** Apache Flink是真正的流处理引擎。它使用流：流、SQL、``micro-batches``和批处理。``Batch``是有限的流式数据集。

### 3. 数据流

- **Hadoop:** [MapReduce](http://data-flair.training/blogs/hadoop-mapreduce-introduction-tutorial-comprehensive-guide/)计算数据流没有任何循环。它是一个阶段链。在每个阶段，你使用上一个阶段的输出向前推进，并为下一个阶段产生输入。
- **Spark:** 虽然机器学习算法是一个循环数据流，但Spark表示为[有向无环图](http://data-flair.training/blogs/directed-acyclic-graph-dag-in-apache-spark/)直接非循环图。
- **Flink:** Flink采取的方法与其他算法不同。它在运行时支持可控的循环依赖图。这有助于以一种非常有效的方式表示机器学习算法。

### 4. 计算模型

- **Hadoop:** MapReduce采用了面向批处理模式。批量是在休息时处理数据。它一次取大量的数据，处理后再写到输出。
- **Spark:**  Spark采用了``micro-batches``模式。``micro-batches``本质上是一种 "先收集后处理"的计算模型。
- **Flink:** Flink采用了连续流。基于运算的流模型。连续流运算在数据到达时就会对数据进行处理，在收集数据和处理数据时没有任何延迟。

### 5. 性能测试

- **Hadoop:** Apache Hadoop只支持批处理。它不处理流数据，因此在Hadoop与Spark与Flink的比较中，性能会比较慢。
- **Spark:** 虽然Apache Spark有很好的社区背景，目前也算是最成熟的社区。但是它的流处理效率比Apache Flink要差很多，因为它采用的是``micro-batches``处理。
- **Flink:** 与其他数据处理系统相比，Apache Flink的性能非常好。Apache Flink使用了原生的闭环迭代运算符，当我们比较Hadoop、Spark、Flink时，它的机器学习和图处理速度更快。

### 6. 内存管理

- **Hadoop:** 它提供了可配置的内存管理。你可以动态或静态地进行。
- **Spark:** 提供了可配置的内存管理。最新发布的Spark 1.6版本已经朝着自动化内存管理的方向发展。
- **Flink:** 提供了自动内存管理。它有自己的内存管理系统，独立于Java的垃圾回收器。

### 7. 容错能力

- **Hadoop:** MapReduce具有高度的容错性。在Hadoop中出现任何故障时，不需要从头开始重新启动应用程序。
- **Spark:** Apache Spark Streaming可以恢复丢失的工作，无需额外的代码或配置，它可以提供精确的一次语义。[阅读更多关于Spark容错的信息](http://data-flair.training/blogs/apache-spark-streaming-fault-tolerance/)
- **Flink:** Apache Flink所遵循的容错机制是基于Chandy-Lamport分布式快照。该机制是轻量级的，这导致在保持高吞吐率的同时，还能提供强大的一致性保证。

### 8. 可扩展性

- **Hadoop:** MapReduce具有令人难以置信的可扩展性潜力，已经在数万个节点的生产中使用。
- **Spark:** 它具有高度的可扩展性，我们可以在集群中不断增加N个节点数量。一个已知的大型[sSpark集群](http://data-flair.training/blogs/install-apache-spark-multi-node-cluster/)是8000个节点。
- **Flink:** Apache Flink也是高度可扩展性的，我们可以在集群中不断增加N个节点数量 一个已知大型[Flink集群](http://data-flair.training/blogs/install-run-deploy-flink-multi-node-cluster/)是成千上万个节点。

### 9. 迭代处理

- **Hadoop:** 不支持迭代处理。
- **Spark:** 不支持迭代处理。它对数据进行分批迭代处理。在Spark中，每次迭代都要单独安排和执行。
- **Flink:** 它通过使用其流式架构迭代数据。Flink可以被指示只处理数据中实际发生变化的部分，从而大大提高了作业的性能。

### 10. 语言支持

- **Hadoop:** 主要支持Java，其他支持的语言有c、c++、ruby、groovy、Perl、Python。
- **Spark:** 主要支持Java、[Scala](https://data-flair.training/blogs/scala-programming/)、Python和[R](http://data-flair.training/blogs/r-programming-tutorial/)。它支持Java、Scala、Python和R，Spark是用Scala实现的。它提供了其他语言的API，如Java、Python和R。
- **Flink:** 它支持Java、Scala、Python和R。它确实也提供了Scala API。

### 11. 优化

- **Hadoop:** 在MapReduce中，作业需要手动优化。[有几种方法可以优化MapReduce作业](http://data-flair.training/blogs/mapreduce-job-optimization-performance-tuning-techniques/)。正确配置你的集群，使用combiner，使用LZO压缩，适当调整MapReduce Task的数量，使用最合适的、最紧凑的可写类型的数据。
- **Spark:** 在Apache Spark中，作业必须手动优化。有一个新的可扩展的优化器[Catalyst](http://data-flair.training/blogs/spark-sql-optimization-catalyst-optimizer/)，基于Scala中的函数式编程构造，是一个新的可扩展优化器。Catalyst的可扩展设计有两个目的。第一，方便添加新的优化技术。第二，使外部开发者能够扩展优化器Catalyst。
- **Flink:** Apache Flink自带的优化器与实际的编程接口是独立的。Flink优化器的工作原理类似于关系型数据库优化器，但将这些优化应用于Flink程序，而不是SQL查询。

### 12. 延迟

- **Hadoop:** Hadoop的MapReduce框架相对来说比较慢，因为Hadoop的MapReduce框架是为了支持不同的格式、结构和海量的数据而设计的。这也是为什么Hadoop的延迟比Spark和Flink都要高的原因。
- **Spark:** Apache Spark是又一个批处理系统，但相对来说，它的速度要比Hadoop MapReduce快一些，因为它通过[RDD](http://data-flair.training/blogs/apache-spark-rdd-tutorial/)将大部分输入数据缓存在内存中，并将中间数据本身保存在内存中，最终在完成后或需要时将数据写入磁盘。
- **Flink:** Apache Flink的数据流运行时，只要在配置上下点功夫，Apache Flink的数据流运行时就能实现低延迟和高吞吐量。

### 13. 处理速度

- **Hadoop:** MapReduce的处理速度比Spark和Flink慢。出现这种慢只是因为基于MapReduce的执行性质，它产生大量的中间数据，节点之间交换的数据很多，因此造成了巨大的磁盘IO延迟。此外，它还需要在磁盘中持久化大量的数据，以便在不同阶段之间进行同步，从而支持Job故障后的恢复。另外，MapReduce中没有办法将所有的数据子集缓存在内存中。
- **Spark:** Apache Spark的处理速度比MapReduce快，因为它通过RDD将大部分的输入数据缓存在内存中，并将中间数据本身保存在内存中，最终在完成后或需要时将数据写入磁盘。Spark的处理速度是MapReduce的100倍，这就说明了Spark比Hadoop MapReduce更好。
- **Flink:** 它的处理速度比Spark快，因为它的流式架构。Flink通过指示只处理实际发生变化的部分数据，提高了作业的性能。

### 14. 可视化

- **Hadoop:** 在Hadoop中，数据可视化工具是``zoomdata``，它可以直接连接到[HDFS](http://data-flair.training/blogs/comprehensive-hdfs-guide-introduction-architecture-data-read-write-tutorial/)，也可以在Impala、[Hive](http://data-flair.training/blogs/apache-hive-tutorial-introductory-guide/)、[Spark SQL](http://data-flair.training/blogs/spark-sql-tutorial/)、Presto等SQL-on-Hadoop技术上直接连接到HDFS。
- **Spark:** 它提供了一个提交和执行作业的Web界面，在这个界面上可以可视化地显示出最终的执行计划。Flink和Spark都集成在Apache zeppelin上，它提供了[数据分析](http://data-flair.training/blogs/data-analytics-comprehensive-guide/)、摄取，以及发现、可视化和协作等功能。
- **Flink:** 它也提供了提交和执行作业的Web界面。由此产生的执行计划可以在这个界面上可视化出来。

### 15. 恢复

- **Hadoop:** MapReduce对系统故障或错误有天然的弹性，它是高度容错的系统。
- **Spark:** Apache Spark的RDD允许通过重新计算有向无环图来恢复故障节点上的分区，同时还支持与Hadoop更类似的恢复方式，通过检查点的方式来减少RDD的依赖性。
- **Flink:** 支持checkpointing机制，它支持将程序存储在数据源和data sink（窗口的状态，以及用户定义的状态，在失败后恢复流式作业的状态）。

### 16. 安全性

- **Hadoop:** 它支持Kerberos身份验证，这在管理上有些痛苦。但是，第三方厂商已经使企业能够利用活动目录Kerberos和LDAP进行身份验证。
- **Spark:** Apache Spark的安全性有点生疏，目前只支持通过共享密钥（密码验证）进行身份验证，所以安全性有点差。Spark能享受到的安全优势是，如果你在HDFS上运行Spark，它可以使用HDFS ACL和文件级权限。此外，Spark可以在[YARN](http://data-flair.training/blogs/hadoop-yarn-tutorial/)上运行，使用Kerberos认证。
- **Flink:** Flink中通过Hadoop/Kerberos基础架构支持用户认证。如果在YARN上运行Flink，Flink会获取提交程序的用户的Kerberos令牌，并在YARN、HDFS和[HBase](http://data-flair.training/blogs/hbase-tutorial-beginners-guide/)上用这个令牌进行身份验证。

### 17. 成本问题

- **Hadoop:** MapReduce通常可以在比一些替代品更便宜的硬件上运行，因为它不会试图将所有的东西都存储在内存中。
- **Spark:** 由于Spark需要大量的RAM来运行内存，在集群中增加RAM，会逐渐增加其成本。
- **Flink:** Apache Flink也需要大量的RAM来运行在内存中，所以它的成本会逐渐增加。

### 18. 兼容性

- **Hadoop:** Apache Hadoop MapReduce和Apache Spark相互兼容，Spark通过JDBC和ODBC共享MapReduce的所有数据源、文件格式和商业智能工具的兼容性。
- **Spark:** Apache Spark和Hadoop是相互兼容的。它可以通过YARN或Spark的独立模式在Hadoop集群中运行，它可以处理HDFS、HBase、Cassandra、Hive和任何Hadoop的InputFormat中的数据。
- **Flink:** Apache Flink是一个可扩展的数据分析框架，完全兼容Hadoop。它提供了一个Hadoop兼容包，可以将针对Hadoop的MapReduce接口实现的函数进行封装，并将其嵌入到Flink程序中。

### 19. 抽象化

- **Hadoop:** 在MapReduce中，我们没有任何类型的抽象。
- **Spark:** 在Spark中，对于批处理，我们有Spark的RDD抽象，对于流，我们有Spark的RDD抽象，而DStream则是内部RDD本身的流。
- **Flink:** 在Flink中，对于批处理，我们有Dataset抽象，对于流应用，我们有Dataset抽象，而流应用有DataStreams。

### 20. 简单易行

- **Hadoop:** MapReduce开发者需要对每个操作进行手写代码，这使得工作难度很大。
- **Spark:** 它很容易编程，因为它有大量的高级运算器。
- **Flink:** 它也有高级运算符。

### 21. 互动模式

- **Hadoop:** MapReduce没有交互式模式。
- **Spark:** Apache Spark有一个交互式shell，可以学习如何充分利用Apache Spark。这是一个用Scala编写的Spark应用，提供了一个自动完成的命令行环境，在这里你可以运行临时查询，熟悉Spark的功能。
- **Flink:** 它自带一个集成的交互式Scala Shell。它既可以在本地设置中使用，也可以在集群设置中使用。

### 22. 实时分析

- **Hadoop:** MapReduce在实时数据处理方面失败，因为它的设计是为了对海量数据进行批量处理。
- **Spark:** 它可以处理实时数据，即来自于实时事件流的数据，即以每秒数百万个事件的速度处理来自实时事件流的数据。
- **Flink:** 主要用于实时数据分析，即以每秒数百万的速度处理来自实时事件流的数据。主要用于实时数据分析，虽然它也提供快速的批处理数据。

### 23. 调度器

- **Hadoop:** Hadoop中的调度器成为可插拔的组件。针对多用户工作负载有两个调度器。Fair Scheduler和Capacity Scheduler。要调度复杂的流，MapReduce需要一个像Oozie这样的外部作业调度器。
- **Spark:** 由于内存内计算，Spark充当了自己的流调度器。
- **Flink:** Flink可以使用YARN Scheduler，但Flink也有自己的Scheduler。

### 24. SQL支持

- **Hadoop:** 使用户能够使用Apache Hive运行SQL查询。
- **Spark:** 使用户能够使用Spark-SQL运行SQL查询。Spark提供了类似于Hive的查询语言和类似于DSL的Dataframe，用于查询结构化数据。
- **Flink:** 在Flink中，Table API是一种类似于SQL的表达式语言，支持像DSL一样的数据框架，目前还在测试阶段。有计划添加SQL接口，但不知道什么时候会落地到框架中。

### 25. 缓存

- **Hadoop:** MapReduce无法将数据缓存在内存中，以备未来需要。
- **Spark:** 它可以将数据缓存在内存中，以便进一步迭代，从而提高其性能。
- **Flink:** 可以将数据缓存在内存中，用于进一步的迭代，提升其性能。它可以在内存中缓存数据，用于进一步的迭代，从而提升其性能。

### 26. 硬件要求

- **Hadoop:** MapReduce在商品硬件上运行得很好。
- **Spark:** Apache Spark需要中高级硬件。因为Spark会在内存中缓存数据，以便进一步迭代，从而提高性能。
- **Flink:** Apache Flink也需要中高级硬件。Flink也可以将数据缓存在内存中，用于进一步的迭代，从而提升其性能。

### 27. 机器学习

- **Hadoop:** 需要机器学习工具，比如Apache Mahout。
- **Spark:** 需要机器学习工具，比如Apache Mahout。它有自己的一套机器学习MLlib。在内存缓存和其他实现细节内，它是真正强大的实现ML算法的平台。
- **Flink:** 它有FlinkML，这是Flink的机器学习库。它在运行时支持可控的循环依赖图。这使得它们能够以一种非常有效的方式表示ML算法，而不是DAG表示。

### 28. 代码行

- **Hadoop:** Hadoop 2.0有1,20000行代码。行数越多，产生的bug越多，执行程序也就越多，需要花费大量的时间。
- **Spark:** Apache Spark：Apache Spark的开发量只有20000行代码。代码的行数比Hadoop少，所以执行程序所需的时间更短。所以执行程序所需的时间较少。
- **Flink:** Flink是用scala和java开发的，所以代码的行数比Hadoop少。所以执行程序所需的时间也会越少。

### 29. 高可用性

高可用性指的是系统或组件长时间运行的系统或组件。

- **Hadoop:** 可在高可用模式下进行配置。
- **Spark:** 可在高可用模式下配置。
- **Flink:** 可在高可用模式下配置。

### 30. 连接Amazon S3

亚马逊简单存储服务（Amazon Simple Storage Service，简称Amazon S3）是一种具有简单的Web服务接口的对象存储，可以从网络上的任何地方存储和检索任何数量的数据。 

- **Hadoop:** 支持。
- **Spark:** 支持。
- **Flink:** 支持。

### 31. 部署

- **Hadoop:** 在独立模式下，Hadoop被配置为以单节点、非分布式模式运行。在伪分布式模式下，Hadoop以伪分布式模式运行。因此，不同的是，在伪分布式模式下，每个Hadoop守护进程都在一个单独的Java进程中运行。而在本地模式下，每个Hadoop守护进程作为一个单独的Java进程运行。在完全分布式模式下，所有的守护进程都在独立的节点上执行，形成一个多节点集群。
- **Spark:** 它还提供了一个简单的独立部署模式，可以在Mesos或YARN集群管理器上运行。它可以手动启动，通过手动启动master和worker，或者使用我们提供的启动脚本。也可以在单机上运行这些守护进程进行测试。
- **Flink:** 它还提供了独立部署模式，可以在YARN集群管理器上运行。

### 32. 处理BackPressure

BackPressure是指当缓冲区满了，无法接收更多的数据时，I/O交换机的数据堆积。在数据的瓶颈消除或缓冲区被清空之前，不再传输更多的数据包。

- **Hadoop:** 通过手动配置来处理。
- **Spark:** 通过手动配置来处理。
- **Flink:** 通过系统架构隐式处理。

### 33. 消除重复

- **Hadoop:** Hadoop中不存在重复消除。
- **Spark:** 在Hadoop中没有消除重复。Spark也会对每条记录进行一次处理，因此消除了重复。
- **Flink:** Apache Flink对每一条记录都是一次处理，因此消除了重复。流应用可以在计算过程中保持自定义状态。Flink的checkpointing机制确保了在出现故障的情况下，状态的语义完全一致。

### 34. Windows标准

一个数据流需要分组成许多逻辑流，每个逻辑流上都可以应用一个窗口操作符。

- **Hadoop:** 它不支持数据流，所以不需要窗口准则。
- **Spark:** 它有基于时间的窗口标准。
- **Flink:** 它有基于记录或任何用户自定义的Flink窗口标准。

### 35. Apache License

Apache License, Version 2.0 (ALv2)是由Apache软件基金会(ASF)编写的一个自由软件许可。Apache 许可证要求保留版权声明和免责声明。

- **Hadoop:** Apache License 2.
- **Spark:** Apache License 2.
- **Flink:** Apache License 2.

所以，这就是前三大数据技术Hadoop、Spark和Flink的对比。

也可以看：

- [对比Apache Spark和Apache Spark Streaming](http://data-flair.training/blogs/apache-storm-vs-apache-spark-streaming-comparison-guide/)
- [对比Apache Yarn和Apache Mesos](http://data-flair.training/blogs/comparison-between-apache-mesos-vs-hadoop-yarn/)