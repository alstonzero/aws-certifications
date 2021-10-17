# Amazon RedShift Cheat Sheet

Amazon Redshift 是一个快速、完全托管的数据仓库(data warehouse)，并把使用标准 SQL 和现有商业智能(Business Intelligence) (BI) 工具分析所有数据变得简单且经济高效。

集群 PB 级数据仓库。

RedShift 是一个基于 SQL 的数据仓库，用于**分析**应用程序。

RedShift 是一种在线分析处理(Online Analytics Processing) (OLAP) 类型的数据库。

RedShift 用于针对 PB 级结构化数据运行复杂的分析查询，使用复杂的查询优化、高性能本地磁盘上的列式存储以及大规模并行查询执行。

RedShift 是**处理**大量商业智能数据的理想选择。

与其他一些本地数据仓库平台相比，极具成本效益(cost-effective)。

PostgreSQL 与 JDBC 和 ODBC 驱动程序兼容；与大多数开箱即用的商业智能工具兼容。

具有针对复杂查询优化的并行处理和列式数据存储。

提供通过 RedShift Spectrum 直接从 S3 上的数据文件进行查询的选项。

RedShift 比传统 SQL 数据库快 10 倍。

RedShift 可以存储大量数据，但无法实时摄取大量数据。

### RedShift 使用列式数据存储：

- 数据**按列而不是行**顺序存储。
- **基于列的数据库**是数据仓库和分析的理想选择。
- 需要更少的 I/O，这大大提高了性能。

### RedShift 提供高级压缩：

- 数据按列顺序存储，这样可以获得更好的性能和更少的存储空间。
- RedShift 会自动选择压缩方案。

RedShift 提供了良好的查询性能和压缩。

RedShift 通过跨所有节点分发数据和查询来提供大规模并行处理(Massively Parallel Processing)(MPP)。

RedShift 使用 EC2 实例，因此您需要选择实例类型/大小以垂直扩展计算，但您也可以通过向集群添加更多节点来水平扩展。

您不能以用户身份直接访问您的 AWS RedShift 集群节点，但您可以通过应用程序访问。

HDD 和 SSD 存储选项。

单个节点的大小为 160GB，并且可以创建高达 PB 或更多的集群。

多节点包括：

### 领导节点(Leader node)：

- 管理客户端连接并接收查询。
- 简单的 SQL 端点(Simple SQL end-point)。
- 存储元数据(Stores metadata)。
- 优化查询计划(Optimizes query plan)。
- 协调查询执行(Coordinates query execution)。

### 计算节点(Compute nodes)：

- 存储数据并执行查询和计算。
- 本地列式存储(Local columnar storage)。
- 所有查询、加载、备份、恢复、调整大小的并行/分布式执行。
- 最多 128 个计算节点。

Amazon RedShift Spectrum 是 Amazon Redshift 的一项功能，可让您对 Amazon S3 中的 EB 级非结构化数据运行查询，而无需加载或 ETL。

## 可用性和耐用性

RedShift 使用复制和连续备份来增强可用性和持久性，并且可以从组件和节点故障中自动恢复。

仅在一个可用区可用，但您可以将快照还原到另一个可用区。

或者，您可以通过将数据加载到来自同一组 Amazon S3 输入文件的不同可用区中的两个 Amazon Redshift 数据仓库集群来在多个可用区中运行数据仓库集群。

Redshift 在您的数据仓库集群中复制您的数据，并持续将您的数据备份到 Amazon S3。

### RedShift 始终保留您数据的三个副本：

- 原本的(The original)。
- 计算节点上的副本(A replica on compute nodes)（在集群内）。
- S3 上的备份副本。

### RedShift 提供连续/增量备份：

- 群集内的多个副本。
- 对 S3 的连续和增量备份。
- 跨区域的连续和增量备份。
- 流式恢复(Streaming restore)。

### RedShift 为以下故障提供容错：

- 磁盘故障(Disk failures)。
- 节点故障(Nodes failures)。
- 网络故障(Network failures)。
- AZ/区域级灾难(AZ/region level disasters)。

对于节点故障，数据仓库集群将无法用于查询和更新，直到供应替换节点并将其添加到数据库中。

### RedShift 的高可用性：

- 目前，RedShift **不支持多可用区部署**。
- 最好的 HA 选项是使用支持数据复制和节点恢复的多节点集群。
- 单节点 RedShift 集群不支持数据复制，如果驱动器出现故障，您必须从 S3 上的快照进行恢复。

RedShift 可以将您的快照异步复制到另一个区域的 S3 以进行灾难恢复。

单节点集群不支持数据复制（在故障情况下，您需要从快照恢复）。

扩展需要几分钟的不可用时间（通常在维护窗口期间）。

在扩展操作期间，RedShift 将数据从现有数据仓库集群中的计算节点并行移动到新集群中的计算节点。

默认情况下，Amazon Redshift 将备份保留 1 天。您可以将其配置为长达 35 天。

如果您删除集群，您可以选择拍摄并保留最终快照。

删除集群时，不会自动删除手动备份。

## 安全

您可以从 S3 加载加密数据。

支持客户端应用程序和 Redshift 数据仓库集群之间传输中的 SSL 加密。

用于网络隔离的 VPC。

静态数据加密 (AES 256)。

审计日志记录和 AWS CloudTrail 集成。

RedShift 负责密钥管理，您也可以通过 HSM 或 KMS 管理自己的密钥。

## 收费

按计算节点小时数收费，每小时 1 个单位（仅计算节点，而非领导节点）。

备份存储 – S3 上的存储。

数据传输 – 区域内 RedShift 和 S3 之间的数据传输不收费，但对于其他情况，您可能需要付费。