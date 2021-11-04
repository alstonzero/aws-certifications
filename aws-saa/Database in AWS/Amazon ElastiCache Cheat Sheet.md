## Amazon ElastiCache Cheat Sheet

[原文](https://digitalcloud.training/certification-training/aws-solutions-architect-associate/database/amazon-elasticache/)

类似于 RDS,but for cache。可以想象为cache版的RDS。

两种流行的**内存中数据存储(in-memory data stores)**的完全托管实现 - `Redis` 和 `Memcached`。

ElastiCache 是一项 Web 服务，可让您轻松在云中部署和运行符合 `Memcached` 或 `Redis` 协议的服务器节点。

ElastiCache 提供的内存缓存(in-memory caching)可用于显着改善许多读取密集型应用程序(read-heavy application)工作负载或计算密集型(compute-intensive)工作负载的延迟(latency)和吞吐量(throughput)。

最适合基于在线分析处理 (OLAP) 事务的数据库负载的场景(scenarios)。

内存、写入和读取的按钮可扩展性。

内存键/值存储(In-memory key/value store)——不是传统意义上的持久化。

按节点大小和使用小时数计费。

Elasticache EC2 节点无法从 Internet 访问，也无法被其他 VPC 中的 EC2 实例访问。

缓存信息可能包括 I/O 密集型(I/O-intensive)数据库查询的结果或计算密集型计算(computationally-intensive calculations)的结果。

Elasticache也可以是按需(on-demand)或预留(reserved)实例（但不是 Spot 实例）。

Elasticache 可用于存储会话状态。

一个节点是一块固定大小的安全、网络连接的 RAM 块，是最小的构建块。

每个节点运行一个符合 Memcached 或 Redis 协议的服务实例，并拥有自己的 DNS 名称和端口。

失败的节点会被自动替换。

对 Elasticache 节点的访问由 VPC 安全组和子网组控制（部署在 VPC 中时）。

子网组是为您的 Amazon ElastiCache 集群指定的子网集合。

您不能将现有的 Amazon ElastiCache 集群从 VPC 外部移动到 VPC内。

您需要为托管 EC2 实例和 Elasticache 集群的 VPC 配置 Elasticache 的子网组。

不使用 VPC 时，Amazon ElastiCache 允许您通过缓存安全组控制对集群的访问（您需要链接相应的 EC2 安全组）。

Elasticache 节点部署在集群中，可以跨越同一子网组的多个子网。

集群是使用相同缓存引擎的一个或多个节点的集合。

应用程序使用端点连接到 Elasticache 集群。

端点是节点或集群的唯一地址。

可以定义维护窗口并允许进行软件修补。

### ElastiCache 引擎有两种类型：

- **Memcached** – 最简单的模型，可以运行具有多个内核/线程的大型节点，可以被scaled in或out，可以缓存对象，例如DB。
- Redis – 复杂模型，支持加密、主/从复制(master / slave replication)、跨可用区（HA）、自动故障转移(automatic failover)和备份/恢复(backup/restore)。

## 用例

下表描述了 ElastiCache 的几个典型用例：

![Amazon ElastiCache 使用案例](https://cdn-digicloud.pressidium.com/wp-content/uploads/2019/03/Amazon-ElastiCache-Use-Cases-1024x494.jpg)

下表描述了决定是否使用 Memcached 或 Redis 引擎的需求：

![Amazon ElastiCache Memcached 或 Redis](https://cdn-digicloud.pressidium.com/wp-content/uploads/2019/03/Amazon-ElastiCache-Memcached-or-Redis-1024x507.jpg)

| Memcached                  | Redis            |
| -------------------------- | ---------------- |
| 简单，                     | 支持加密         |
| 因需求变化而向内或向外扩展 | 支持集群         |
| 运行多核CPU和多线程        | 支持复杂数据类型 |
| 缓存对象(eg.database请求)  | 支持HA           |
|                            | 发布/订阅功能    |
|                            | 地理空间指数     |
|                            | 备份和恢复       |



## 内存缓存(Memcached)

数据不是持久的(Not persistent)。

不能用作数据存储。

支持**多核或多线程**的大型节点。

通过添加和删除节点来向外扩展和向内扩展(Scales out and in)。

数据存储（RDS、Dynamo DB 等）的理想前端。

### 用例：

- 缓存数据库的内容。
- 从动态生成的网页缓存数据。
- 瞬态会话数据。
- 用于大容量 Web 应用程序中的准入控制的高频计数器。

每个区域最多 100 个节点，每个集群 1-20 个节点（软限制）。

可以与 SNS 集成以获得节点故障/恢复通知。

支持自动发现(auto-discovery)从集群中添加/删除的节点。

通过添加/删除节点来**扩展/扩展(Scales out/in)（水平）**。

通过更改节点系列/类型来**放大/缩小(Scales up/down)（垂直）**。

**不支持**多可用区故障转移或复制。

**不支持**快照。

您可以将节点放置在不同的 AZ 中。

使用 ElastiCache Memcached，每个节点代表一个数据分区，集群中的节点可以跨越可用区：

![ElastiCache 内存缓存](https://cdn-digicloud.pressidium.com/wp-content/uploads/2019/10/ElastiCache-Memcached-1024x526.jpg)

## Redis

数据是持久的。

可以用作数据存储。

**不是多线程**。

通过添加分片而不是节点来扩展。

Redis 分片是集群键空间的子集，可以包括一个主节点和零个或多个只读副本。

支持自动和手动快照（S3）。

备份包括集群数据和元数据。

您可以通过创建新的 Redis 集群并从备份中填充它来恢复数据。

支持主/从复制(master/slave replication)。

在备份期间，您无法在集群上执行 CLI 或 API 操作。

默认情况下启用自动备份（使用 Redis 删除自动删除）。

您只能通过在区域之间移动之前从 Elasticache 导出快照来在区域之间移动快照（然后可以用数据填充新集群）。

多可用区可以使用同一区域中另一个可用区中的只读副本。

### 禁用集群模式时：

- 您只能拥有一个分片(shard)。
- 一个分片(shard)可以有一个读/写主节点(read/write primary node)和 0-5 个只读副本(read only replicas)。
- 您可以将副本分布在同一区域的多个可用区上。
- 从主节点复制是异步的(asynchronous)。

禁用集群模式的 Redis 集群如下图所示：

![ElastiCache Redis 集群模式已禁用](https://cdn-digicloud.pressidium.com/wp-content/uploads/2019/10/ElastiCache-Redis-Cluster-Mode-Disabled-1024x526.jpg)

### 启用集群模式时：

- 最多可以有 15 个分片(shards)。
- 每个分片(shards)可以有一个主节点和 0-5 个只读副本。
- 拍摄快照会减慢节点的速度，最好从只读副本中获取。

启用集群模式的 Redis 集群如下图所示：

![已启用 ElastiCache Redis 集群模式](https://cdn-digicloud.pressidium.com/wp-content/uploads/2019/10/ElastiCache-Redis-Cluster-Mode-Enabled-1024x526.jpg)

### 多可用区故障转移：

- Elasticache 检测到故障。
- Elasticache 会自动提升具有最低副本滞后的副本。
- DNS 记录保持不变，但指向新主服务器的 IP。
- 其他副本开始与新的主副本同步。

您可以通过启用集群模式和多可用区故障转移来实现完全自动化、容错的 Elasticache-Redis 实施。

下表比较了 Memcached 和 Redis 引擎：

![Elasticache Memcached 和 Redis 引擎的比较](https://cdn-digicloud.pressidium.com/wp-content/uploads/2018/07/Elasticache-Memcached-vs-Redis.jpg)

## 收费

定价是按每个节点类型消耗的节点小时计算的。

消耗的部分节点小时数按完整小时数计费。

**同一可用区内**的 Amazon EC2 和 Amazon Elasticache 之间的数据传输是免费的。

## ElastiCache 的高可用性

### 内存缓存(Memcached)：

- 由于 Memcached 不支持复制，节点故障会导致数据丢失。
- 使用多个节点以最大限度地减少节点故障时的数据丢失。
- 跨可用 AZ 启动多个节点，以最大限度地减少 AZ 故障时的数据丢失。

### Redis:

- 在每个分片(shard)中使用多个节点并将节点分布在多个可用区中。
- 在复制组上启用多可用区以在主节点发生故障时允许自动故障转移。
- 安排 Redis 集群的定期备份。