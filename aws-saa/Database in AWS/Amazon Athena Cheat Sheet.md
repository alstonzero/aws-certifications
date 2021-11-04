# Amazon Athena Cheat Sheet

## 一般的

Amazon Athena 是一种**交互式查询服务，可以使用标准 SQL 轻松查询S3 中的数据**。

Athena 是无服务器的，因此无需管理基础架构，**只需为运行的查询付费**。

Athena 易于使用 ——只需指向 Amazon S3 中的数据，定义架构，然后使用标准 SQL 开始查询。

Amazon Athena 使用具有完整标准 SQL 支持的 Presto engine，并处理各种标准数据格式，包括 CSV、JSON、ORC、Apache Parquet 和 Avro。

虽然 Amazon Athena 非常适合快速、即席查询(ad-hoc querying)并与 Amazon QuickSight 集成以实现轻松可视化，但它还可以处理复杂的分析，包括大型联接、窗口函数和数组。

Amazon Athena 使用托管数据目录来存储有关您为存储在 Amazon S3 中的数据创建的数据库和表的信息和架构。

使用 Amazon Athena，您无需担心管理或调整集群以获得快速性能。

Athena 针对 Amazon S3 的快速性能进行了优化。

Athena 自动并行执行查询，因此即使在大型数据集上，您也能在几秒钟内获得查询结果。

大多数结果会在几秒钟内交付。

使用 Athena，无需复杂的 ETL 作业来准备数据以进行分析。

这使得任何具有 SQL 技能的人都可以轻松快速地分析大规模数据集。

Athena 与[AWS Glue](https://aws.amazon.com/glue/) 数据目录开箱即[用地](https://aws.amazon.com/glue/)集成 ，允许您跨各种服务创建统一的元数据存储库、抓取数据源以发现架构并使用新的和修改过的表和分区定义填充您的目录，并维护架构版本控制。

您还可以使用 Glue 的完全托管 ETL 功能来转换数据或将其转换为列格式，以优化成本并提高性能。

## 用例

Amazon Athena 等查询服务、Amazon Redshift 等数据仓库以及 Amazon EMR 等复杂的数据处理框架都可以满足不同的需求和用例。

Amazon Redshift 为企业报告和商业智能工作负载提供最快的查询性能，尤其是那些涉及具有多个联接和子查询的极其复杂的 SQL 的工作负载。

与本地部署相比，Amazon EMR 使运行高度分布式处理框架（例如 Hadoop、Spark 和 Presto）变得简单且经济高效。Amazon EMR 非常灵活——您可以运行自定义应用程序和代码，并定义特定的计算、内存、存储和应用程序参数来优化您的分析需求。

Amazon Athena 提供了最简单的方法来运行 S3 中的数据的即席查询，而无需设置或管理任何服务器。

下表显示了使用一些 AWS 查询和分析服务的主要用例和情况：

![AWS 分析和查询服务](https://cdn-digicloud.pressidium.com/wp-content/uploads/2019/10/Analytics-and-Query-Services-1024x507.jpg)

## 价钱

使用 Amazon Athena，您只需为您运行的查询付费。

根据每个查询扫描的数据量向您收费。

通过压缩、分区或将数据转换为列格式，您可以获得显着的成本节约和性能提升，因为这些操作中的每一个都减少了 Athena 需要扫描以执行查询的数据量。