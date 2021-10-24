# Amazon SQS 备忘单

### 官网简介

Amazon Simple Queue Service (SQS) 是一种完全托管的消息队列服务，可让您分离和扩展微服务、分布式系统和无服务器应用程序。SQS 消除了与管理和运营消息型中间件相关的复杂性和开销，并使开发人员能够专注于重要工作。借助 SQS，您可以在软件组件之间发送、存储和接收任何规模的消息，而不会丢失消息，并且无需其他服务即可保持可用。使用 AWS 控制台、命令行界面或您选择的 SDK 和三个简单的命令，在几分钟内即可开始使用 SQS。

SQS 提供两种消息队列类型。标准队列提供最高吞吐量、最大努力排序和至少一次传送。SQS FIFO 队列旨在确保按照消息的发送顺序对消息进行严格一次处理。

### 基本概念

实现了application之间的异步(asynchronous)通信。

Amazon Simple Queue Service (Amazon SQS) 是一项 Web 服务，可让您访问存储着等待被处理的消息的消息队列(message queue)。

SQS 提供了一个可靠的、高度可扩展的托管队列，用于存储在计算机之间传输的消息。

SQS 用于分布式/解耦(distributed/decoupled)应用程序。

SQS 可与 RedShift、DynamoDB、EC2、ECS、RDS、S3 和 Lambda 一起使用。

SQS 使用面向消息(message-oriented)的 API。

SQS 使用pull based的（轮询）而不是push based的。

消息大小为 256KB。

消息可以在队列中保留 1 分钟到 14 天（默认为 4 天）。

### 可见性超时

可见性超时(visibility timeout)是消息在阅读器获取消息后在队列中不可见(invisible)的时间量。为防止其他用户再次处理消息，Amazon SQS 会将**可见性超时**，Amazon SQS 阻止其他用户接收并处理消息的一段时间。

如果在可见性超时(visibility timeout)内处理job，消息将被删除。

如果在可见性超时(visibility timeout)内未处理job，消息将再次可见（可以传递两次）。

Amazon SQS 消息的最大可见性超时(visibility timeout)为 12 小时。

一条 Amazon SQS 消息最多可以包含 10 个元数据属性。

下表比较了更适合 Amazon Kinesis Data Streams 和 Amazon SQS 的解决方案要求：

![亚马逊 Kinesis 与 SQS](https://cdn-digicloud.pressidium.com/wp-content/uploads/2019/12/Amazon-Kinesis-vs-SQS-1024x378.jpg)

## 轮询(Polling)

SQS 使用短轮询(short polling)和长轮询(long polling)。

### 短轮询(short polling)：

- 不等待消息出现在队列中。
- 它仅查询可用服务器的子集以获取消息（基于加权随机执行）。
- 短轮询是**默认设置**。
- ReceiveMessageWaitTime 设置为 0。
- 使用更多的请求，这意味着更高的成本。

### 长轮询(Long polling)：

- 使用更少的请求并降低成本。
- 通过查询所有服务器来消除错误的空响应。
- 在发送响应之前，SQS 会等待直到队列中有可用消息。
- 请求至少包含一条可用消息，最多可达 ReceiveMessage 操作中指定的最大消息数。
- 如果您的应用程序需要立即响应以接收消息调用，则不应使用。
- ReceiveMessageWaitTime 设置为非零值（最多 20 秒）。
- 每百万个请求的费用与短轮询相同。

## 队列

队列名称在一个区域内必须是唯一的。

队列可以是**标准的(standard)**或**先进先出 (FIFO)** 的。

### 标准队列(Standard queues)

- 提供了一种松散的 FIFO 功能，试图保留消息的顺序。

- 由于标准队列(Standard queues)被设计为使用高度分布式架构(highly distributed architecture)进行大规模可扩展，因此**无法保证按照消息发送的确切顺序接收消息**。

- 标准队列(Standard queues)提供**至少一次传递(at-least-once delivery)**，这意味着每条消息至少传递一次。

### FIFO（先进先出）队列

- **保留**消息发送和接收的**确切顺序**。

- FIFO 队列目前在**有限的地区可用**。

- 如果使用 FIFO 队列，则不必在消息中放置排序信息。

- FIFO 队列提供一次性处理(exactly-once processing)，这意味着每条消息都被传递一次并保持可用，直到消费者处理它并删除它。

![img](https://cdn-digicloud.pressidium.com/wp-content/uploads/2018/06/SQS-Queues-1024x535.jpg)

## 限制

传输中的消息(In-flight messages)是消费者已获取但尚未从队列中删除的消息。

标准队列的每个队列限制为 120,000 条传输中的消息(In-flight messages)。

FIFO 队列的每个队列限制为 20,000 条传输中的消息(In-flight messages)。

队列名称最多可包含 80 个字符。

消息默认保留 4 天，最长可达 14 天。

FIFO 队列在批处理时支持每秒 3000 条消息，否则每秒支持 300 条消息。

**最大消息大小为 256KB**。

## 可扩展性和耐用性(Scalability and Durability)

您可以有多个具有不同优先级(priorities)的队列。

缩放(Scaling)是通过创建更多队列来执行的。

SQS 将所有消息队列和消息存储在具有多个冗余可用区的单个高可用性 AWS 区域中。

## 安全

您可以使用 **IAM 策略**来控制谁可以读/写消息。

**身份验证**可用于保护队列中的消息（谁可以发送和接收）。

SQS 支持 HTTPS 并支持 TLS 版本 1.0、1.1、1.2。

SQS 符合 PCI DSS 1 级标准且符合 HIPAA 要求。

服务器端加密Server-side encryption (SSE) 允许您在加密队列 (AWS KMS) 中传输敏感数据：

- 一旦 SQS 收到消息，SSE 就会对其进行加密。
- 消息以加密形式存储，并且 SQS 仅在将消息发送给授权使用者时对其进行解密。
- 使用 AES 256 位加密。
- 并非在所有地区都可用。
- 标准和 FIFO 队列。
- 消息正文已加密。
- 以下内容未加密：
  - 队列元数据Queue metadata。
  - 消息元数据Message metadata。
  - 每个队列的指标Per-queue metrics。

## 监控

CloudWatch 与 SQS 集成，您可以查看和监控队列指标(metrics)。

CloudWatch 指标每 5 分钟自动收集一次。

如果队列包含任何消息或任何 API 操作访问它，CloudWatch 会将其视为活动队列长达 6 小时。

CloudWatch 免费（无详细监控）。

CloudTrail 捕获来自 SQS 的 API 调用并将日志记录到指定的 S3 存储桶。

下表描述了相关服务及其典型用例：

![服务 作用 示例用例 简单通知服务 (SNS) 从云端设置、操作和发送通知 在触发 CloudWatch 警报时发送电子邮件通知 Step Functions 使用可视化工作流程对 AWS 服务组件进行开箱即用的协调 订单处理工作流 简单工作流服务 (SWF) 需要支持外部流程或专门的执行逻辑 人工支持的工作流，如订单履行系统或程序请求 AWS 建议客户为新应用程序考虑 Step Functions 而不是 SWF 简单队列服务 (SQS) 消息传递队列;  存储和转发模式 构建分布式/解耦应用程序 Amazon MQ 基于 Apache MQ 的托管消息代理 从现有消息代理迁移到 AWS 的简单、低麻烦的路径](https://cdn-digicloud.pressidium.com/wp-content/uploads/2019/10/AWS-Application-Integration-Services-1024x685.jpg)