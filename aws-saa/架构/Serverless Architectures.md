# Serverless Architectures

## Mobile application: MyTodoList

### MyTodoList的requirements

- 公开一个具有HTTPS端点的REST API (Expose as REST API with HTTPS)
- Serverless architecture
- user可以直接和他们在s3中的文件夹进行交互(Users should be able to directly interact with their own folder in S3)
- Users should authenticate through a managed serverless service
- The users can write and read to-dos, but they mostly read them
- The database should scale, and have some high read throughput

### 实现步骤

![](https://raw.githubusercontent.com/alstonzero/aws-certifications/main/aws-saa/%E6%9E%B6%E6%9E%84/pic/serverless%20Mobile%20application.png)



- 由于是HTTPS，因此使用Amazon API Gateway

- API Gateway 调用Lambda Function，这允许我们扩展并使用无服务器基础设施。

- Amazon Lambda 要能够从数据库中存储并读取 to-dos，需要一个扩展性非常好的数据库

  而serverless database是 DynamoDB。因此使用DynamoDB作为后端。

- 身份验证使用Amazon Congnito。Mobile Client连接和并向 Cognito进行身份验证，然后API Gateway将使用 Cognito 验证身份验证。

- 如何从Mobile Client访问S3？

  - Mobile Client向 Amazon Cognito 进行身份验证， Cognito能为我们生成临时credentials，并使用 AWS STS 并返回这些credentials到Mobile Client。这些凭据允许我们的Mobile Client在 Amazon S3 中存储和检索文件(有限制策略)。（错误答案：在Mobile Client上存储AWS user credentials）

- 如何提高read throughout（读取吞吐量）并降低成本？

  - 使用DAX作为缓存层(caching layer)。在 DynamoDB 之前，使用 DynamoDB DAX作为缓存层。因此 DynamoDB 不需要那么多读取容量单位(read capacity units)，也许会更好地扩展，也许会降低成本，因为so many reads are cached。

- 缓存responses，在API Gateway进行 response的caching。

## Serverless hosted website: MyBlog.com

###  MyBlog.com的requirements

- This website should scale globally 
- Blogs are rarely written, but often read(经常读很少写)
- Some of the website is purely static files, the rest is a dynamic REST API（部分网站是纯静态文件，其余是动态REST API）
- Caching must be implement where possible（必须在可能的情况下实施缓存）
- Any new users that subscribes should receive a welcome email（任何订阅的新用户都应该收到一封welcome email）
- Any photo uploaded to the blog should have a thumbnail generated（上传到blog的任何照片都应生成缩略图）

### 实现步骤

![](https://raw.githubusercontent.com/alstonzero/aws-certifications/main/aws-saa/%E6%9E%B6%E6%9E%84/pic/serverless%20MyBlog.com.png)

- Serving static content, globally：静态内容储存在S3 bucket中，使用Amazon CloudFront全球范围公开。User将会和CloudFront上的边缘站点交互，从S3中直接获取数据。如何实现securely（安全）？

  - 使用OAI(Origin Access Identity)从CloudFront到S3的源访问身份。在S3上添加bucket policy，只授权OAI，即CloudFront能read而其他不可以。因此user不能直接从S3获取数据，而必须经过CloudFront。

- Adding a public serverless REST API：我们将有一个REST HTTPS cloud talking to Amazon API Gateway，invoke(调用)Lambda Function。也许从DynamoDB查询或读取数据。由于经常read而不是write，因此使用DAX作为缓存层。

  - Leveraging DynamoDB Global Tables：如果要面向全球，可以利用 DynamoDB 全局数据库减少世界部分地区的延迟。(在创建DynamoDB Global Table之前要启用DynamoDB Streams，DynamoDB Streams 使 DynamoDB 能够获取changelog，使用该changelog就能够在其他 AWS 区域的副本表(replica tables)之间复制数据)

- ![](https://raw.githubusercontent.com/alstonzero/aws-certifications/main/aws-saa/%E6%9E%B6%E6%9E%84/pic/user%20welcom%20email%20flow.png)

  User Welcome email flow：在DynamoDB中启用变化流(enable streams of changes)。因此创建DynamoDB流，DynamoDB 流将调用 Lambda 函数。Lambda 函数会很特别，它将有一个 IAM 角色，能够使用 Amazon SES。

- Thumbnail（缩略图） Generation flow：我们想要创建缩略图，user可能会直接上传到S3 存储桶。或者 CloudFront distribution中再次使用 OAI，在这种情况下，我们的客户会将照片上传到 CloudFront，CloudFront 会将它们转发到 Amazon S3 存储桶，这称为 S3 传输加速(S3 transfer acceleration)。然后我们要做的是，每当一个文件被添加到 S3它会触发一个 Lambda 函数Lambda 将创建一个缩略图并将该缩略图放入 S3 存储桶中（可能是不同的存储桶）。Amazon S3 还具有 SQS 和 SNS 的触发器。（因此 Amazon S3 可以调用 Lambda、SQS 或 SNS，）

### AWS Hosted Website Summary

- 使用 CloudFront 和 S3 分发静态内容
- REST API 是无服务器的，这里不需要 Cognito，因为它是public的
- 利用 Global DynamoDB 表为全球数据提供服务
- （我们本可以使用 Aurora 全球数据库）
- 启用了 DynamoDB streams来触发 Lambda 函数
- lambda 函数具有可以使用 SES 的 IAM 角色
-  SES（简单电子邮件服务）用于以无服务器方式发送电子邮件
-  S3 可以触发 SQS / SNS / Lambda 来通知事件

## Micro Services architecture

### Requirements

- 切换到微服务架构
- 许多服务使用REST API直接相互交互
- 每个微服务的每个架构可能在形式和形状上有所不同
- 需要一个微服务架构，能拥有一个更精简的每个服务的开发生命周期

![](https://github.com/alstonzero/aws-certifications/blob/main/aws-saa/%E6%9E%B6%E6%9E%84/pic/serverless%20Micro%20Services%20Environment.png?raw=true)

- 第一个微服务：Users通过HTTPS访问第一个微服务。它有一个ELB，ELB访问ECS(用于在 AWS 上编写 docker 容器)，然后ECS访问DynamoDB。
- 而微服务通常会有一个DNS名称或者URL，例如service1.example.com，因此使用到route53进行DNS查询，获取alias record（别名记录），然后可以与service进行交互。
- 第二个微服务：一个经典的serverless架构，Lambda 函数也许调用第一个服务的ELB来获取一些它需要的信息。(后端使用ElastiCache只是为了证明ElasticCache可以作为Lambda的后端）
- 第三个微服务：也是用ELB，但它不是serverless的它使用的是 Amazon EC2 ASG和 Amazon RDS 数据库。EC2也许会调用第二个服务的API Gateway。

### Discussions on Micro Services

- 您可以按照自己的意愿自由设计每个微服务
-  同步模式：API Gateway、负载均衡器
- 异步模式：SQS、Kinesis、SNS、Lambda 触发器 (S3)
- 微服务的挑战：
  - 创建每个新微服务的重复开销，
  - 优化服务器密度/利用率的问题
  - 同时运行多个微服务的多个版本的复杂性
  - 与许多独立服务集成的客户端代码需求激增。
- 无服务器模式解决了一些挑战：
  - API Gateway、Lambda 自动扩展并且按使用量付费
  - 可以轻松克隆 API，重现环境
  - 通过 API Gateway的 Swagger 集成生成客户端 SDK

## Distributing paid content

### requirements

- 在线销售视频，用户必须付费才能购买视频
- 每个视频都可以被许多不同的客户购买
- 只把视频分发给高级用户
- 需要有一个高级用户数据库
- 发送给高级用户的链接应该是短暂的
- 应用程序是全球性的
- 完全serverless

![](https://raw.githubusercontent.com/alstonzero/aws-certifications/main/aws-saa/%E6%9E%B6%E6%9E%84/pic/serverless%20Distribute%20Content%20only%20to%20premium%20users.png)

- Start simple, premium user service：REST HTTPS访问Amazon API Gateway，调用Lambda函数，访问DynamoDB，read,update,delete a table of users，这张表中记录着该user是否付费的信息。
- 使用Amazon Congnito 进行身份验证
- 使用S3放置视频，由于需要全球分发和安全分发，因此使用CloudFront Global distribution，并使用带有bucket policy的OAI与S3通信。
- 如何允许只有高级user能与 CloudFront 交谈？
  - 使用CloudFront的签名URL(signed URL)的功能。
  - 首先，创建第二个Amazon API Gateway它将verify  client的身份验证。
  - client 请求API Gateway想要一个Signed URL，API Gateway会调用一个Lambda函数，来创建一个Signed URL。
  - 而为了创建这个Signed URL，Lambda函数首先通过查看DynamoDB来验证调用该API的user是否为一个高级user。如果该user是高级用户，然后Lambda将通过AWS SDK使用 CloudFronts API，以生成一个Signed URL，也许Signed URL 会有一个五分钟到期。
  - 当完成时，Lambda 将值返回给 API Gateway，API Gateway把Signed URL 返回给Client。现在，Client可以使用Signed URL访问 CloudFront 全球分布。

### Premium User Video service总结

- 实现了一个完全serverless的解决方案
- Cognito用于身份验证
- DynamoDB 用于存储高级用户
- 2 个serverless application
  - 高级用户注册
  - CloudFront Signed URL 生成器
- 内容存储在S3中（serverless and scalable可扩展）
- 与带有 OAI 的 CloudFront 集成以确保安全（用户无法绕过）
- CloudFront 只能使用Signed URL 来防止未经授权的用户
- S3 Signed URL 怎么样？它们对于全球访问（global access）来说效率不高。



## Software updates offloading

### Requirements

- 有一个运行在 EC2 上的应用程序，用于分发软件，不定时更新
- 当新的软件更新发布时，我们会收到很多请求，而内容通过网络大量分发。这是非常昂贵的(It’s very costly)
- 不想改变应用程序，但想优化成本和CPU，该怎么办？

![](https://raw.githubusercontent.com/alstonzero/aws-certifications/main/aws-saa/%E6%9E%B6%E6%9E%84/pic/serverless%20Software%20updates%20offloading.png)

- ELB 加 ASG 开发应用程序在多个AZ中运行，假设这些软件更新被放入亚马逊 EFS中，如何更全球化地扩展，降低 CPU 使用率并降低成本？——只需要在前面放 CloudFront 。

### Why CloudFront？

- 架构没有变化
- 将在边缘缓存软件更新文件(Will cache software update files at the edge)
- 软件更新文件不是动态的，是静态的（永远不会改变）
- 尽管 EC2 实例不是serverless的，但 CloudFront 是serverless的，并将为我们扩展（scale）
- 这意味着，我们的 ASG 不需要太多扩展，因此能在 EC2 中节省大量资金。
- 还将节省可用性、网络带宽成本等。
- **（Remember）CloudFront是使现有应用程序更具可扩展性和成本更低的简单方法**（Easy way to make an existing application more scalable and cheaper!）如果是用来缓存静态内容。



## Big Data Ingestion Pipeline

### Requirements

- ingestion pipeline to be fully serverless（完全serverless的方式集成pipeline）

- 能实时收集数据
- 能转换数据
- 能使用SQL查询转换后的数据
- 使用查询创建的报告储存在S3中
- 将数据加载到warehouse(仓库)中并创建dashborad(仪表盘)

![](https://raw.githubusercontent.com/alstonzero/aws-certifications/main/aws-saa/%E6%9E%B6%E6%9E%84/pic/serverless%20Big%20Data%20Ingestion%20Pipeline.png)

- IoT设备是data的生产者(producer)，(在Amazon Cloud中，IoT Core可以帮助你管理这些IoT设备)，这些设备实时发送data到IoT Core。IoT Core直接进入Kinesis data Streams。Kinesis data Streams允许实时传输大数据，并很快进入这个 Kinesis 服务。
- Kinesis talk to Kinesis Firehose ，而Kinesis Firehose允许我们，(例如，每隔一分钟)，将数据放入和卸载(put and offload)到 Amazon S3 存储桶中，这将是一个摄取桶(Ingestion Bucket)。
- 使用直接link到Kinesis DataFirehose的Lambda函数，来clean或者快速转换数据(transform data)。
- 摄取桶(Ingestion Bucket)可以用来触发一个SQS队列，SQS队列可以触发Lambda函数，Lambda触发Athena来查询，而Athena query将提取(pull)来自摄取桶的data并将执行 SQL 查询。
- Athena的查询报告将存储在一个Report Bucket上，也许是另外一 个bucket。
- 还可以使用QuickSight直接将report可视化，到 S3 存储桶中，加载data进入适当的data warehouse数据仓库进行分析，例如RedShift(注意：RedShift不是serverless的)，而 Redshift data warehouse也可以作为 QuickSights 的切入点来提供服务。
- 

### Big Data Ingestion Pipeline discussion

- IoT Core 允许您从 IoT 设备收集数据
- Kinesis 非常适合实时数据收集
- Firehose 将数据近乎实时地（1 分钟）传送到 S3
- Lambda 可以帮助 Firehose 进行数据转换
- Amazon S3 可以触发对 SQS 的通知
- Lambda 可以订阅 SQS（我们可以将 S3 连接器连接到 Lambda）
- Athena 是serverless的 SQL 服务，结果存储在 S3 中
- reporting bucket包含分析的数据，可供报告工具，例如 AWS QuickSight、Redshift 等所使用