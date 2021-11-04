# Kinesis 

## Kinesis Overview

- Amazon Kinesis makes it easy to **collect, process, and analyze** streaming data in **real-time**（收集、处理和分析实时流数据）
- 用于处理各种数据流的服务集合。例如：Application logs, Metrics, Website clickstreams, 
  IoT telemetry data…
- 数据在“分片(shards)”中处理——每个分片(shards)每秒能够摄取 1000 条记录。
- 默认限制为 500 个分片(shards)，但您可以请求增加到无限分片。
- 记录由分区键(partition key)、序列号(sequence number)和data blob（最多 1 MB）组成。
- 瞬态数据存储(Transient data store) – 默认保留 24 小时，但可配置为最多保留 7 天。
- 有四种类型的 Kinesis 服务
  -  Kinesis Data Streams: capture, process, and store data streams(捕捉，处理和存储数据流)
  -  Kinesis Data Firehose: load data streams into AWS data stores(载入数据流到AWS数据存储)
  -  Kinesis Data Analytics: analyze data streams with SQL or Apache Flink(用SQL或Apache Flink分析数据流)
  - Kinesis Video Streams: capture, process, and store video streams(捕捉，处理和存储video流)

## Kinesis Data Streams

- Kinesis Data Streams 使您能够构建自定义应用程序来**处理或分析流数据(process or analyze streaming data)**以满足特殊需求。

- Kinesis Data Streams 支持**实时处理**流式大数据(real-time processing of streaming big data)。

- Kinesis Data Streams 可用于快速将数据从数据生成器(data producers)中移出，然后持续处理数据。

- Kinesis Data Streams**存储数据**以供应用程序稍后处理（与直接将数据传输到 AWS 服务的 Firehose 的主要区别）。

### 常见用例包括：

- 加速日志和数据提要的摄入(Accelerated log and data feed intake)。
- 实时指标和报告(Real-time metrics and reporting)。
- 实时数据分析(Real-time data analytics)。
- 复杂的流处理(Complex stream processing)。

### Kinesis Data Streams 的高级架构。

- 生产者Producers不断地将数据推送到 Kinesis Data Streams。
  - Kinesis Streams API.
  - Kinesis Producer Library (KPL).
  - Kinesis Agent.
- 消费者Consumers实时处理数据。
- 消费者可以使用 Amazon DynamoDB、Amazon Redshift 、Amazon S3、Kinesis Data Firehose、Kinesis Data Analytics 等 AWS 服务存储他们的结果。
- Kinesis Streams application是在 EC2 实例上运行的使用者。
- 分片(Shards)是流中唯一标识的组(uniquely identified groups)或数据记录(data record)。
  - 一个分片(Shard)提供1MB/s数据输入和2MB/s数据输出的容量。
  - 每个分片(Shard)每秒最多可支持 1000 个 PUT 记录
  - A stream is composed of(由...组成) one or more shards
  - 当数据速率增加(data rate increase)时，添加更多分片以增加流的大小
  - 当数据速率降低(data rate decreases)时删除分片。
- 记录(records)是存储在 Kinesis Stream 中的数据单元(data units)。
  - 由序列号(sequence number)、分区键(partition key)和data blob 组成
  - 一条record中data blob的最大size为1MB
  - 分区键(partition key)用于按流中的分片(shard)对数据进行分组。

![Amazon Kinesis 数据流架构](https://cdn-digicloud.pressidium.com/wp-content/uploads/2018/07/Kinesis-Data-Streams-Architecture-1024x455.jpg)

- Kinesis Streams 使用 KMS master keys进行加密.

  - 要读取或写入加密流，producer和consumer application必须有权访问master key。

- Kinesis Data Streams 跨三个可用区同步(synchronously)复制。

## Kinesis Data Firehose

![1635303350518](C:\Users\alston\AppData\Roaming\Typora\typora-user-images\1635303350518.png)

- Kinesis Data Firehose 是将流数据加载到数据存储(data store)和分析工具(analytics tools)中的最简单方法。
- 使用 Kinesis Data Firehose，无需编写application或管理资源manage resource。无分片完全自动化(No shards, totally automated.)
- Kinesis Data Firehose能捕获、转换和加载流数据(Captures, transforms, and loads streaming data.)。
  - Firehose 支持多种数据格式、转换、压缩。可以在加载数据之前对数据进行批处理、压缩和加密（batch, compress, and encrypt ）。
  - 支持使用AWS Lambda自定义数据转换
- Kinesis Data Streams 可用作 Kinesis Data Firehose 的源。
  - 如果使用 Kinesis Streams 作为数据源，则可以使用服务器端加密（Server-side-encryption）。
- **near real-time**近乎实时
  - non full batches的最小延迟是60s
  - 或一次最少32MB的数据
- 能发送failed或all data到backup S3 bucket。
- Firehose目的地包括
  - Amazon S3.
  - Amazon Redshift.
  - Amazon Elasticsearch Service.

![1635305799609](C:\Users\alston\AppData\Roaming\Typora\typora-user-images\1635305799609.png)

## Kinesis Data Analytics