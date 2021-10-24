

### Lambda限制

- Execution(执行)：
  - Memory allocation内存分配:128MB到10GB(1MB increments)
  - Maximum execution time最长执行时间:900 秒(15minutes)
  - Environment variables环境变量(4kb)
  - Disk capacity in the "function container"能拉取临时文件的容量(`/tmp`文件夹的容量)：512MB
  - Concurrency executions并发执行: 1000个(能申请增加)
- Deployment(部署):
  - Lambda function deployment size (compressed .zip): 50 MB
  - Size of uncompressed deployment (code + dependencies): 250 MB
  - Can use the /tmp directory to load other files at startup在启动时能使用`/tmp`目录去加载其他文件
  - Size of environment variables环境变量大小: 4 KB

常见问题：如果需要30GB的内存，30分钟的执行时间，需要一个3GB的大文件，此时Lambda并不是一个好选择

## DynamoDB

- 完全托管，高可用，拥有跨多个AZ的副本
- NoSQL database，不是关系型database
- 可扩展到海量工作负载，**分布式**数据库
- 每秒钟百万次请求，数万亿行，100TB存储
- 性能快速且一致(检索低延迟)
- 与IAM集成，以实现安全，授权和管理
- 使用DynamoDB Streams启用事件驱动编程
- 低成本和自动扩展能力

### DynamoDB——Basics

- DynamoDB是由**tables**组成
- 每个table都有primary key(必须在创建时决定)
- 每个table可以有无限数量的项目(=行)
- 每个项目都有**属性**(可以随着时间的推移添加——可以为空)
- 项目的最大大小为400KB
- 支持的数据类型
  - 标量类型：字符串、数字、二进制、布尔类型、Null
  - 文档类型：List、Map
  - Set类型：String Set、Number Set、Binary Set

### DynamoDB - Read/Write Capacity Mode(考试重点）

- 控制如何去管理你的table's capactity(read/write throughput)
- **Provisioned Mode**(供应模式)(默认)
  - 指定每秒的读写数量
  - 需要事先计划好capacity
  - 需要为事先供应的Read Capacity Units(RCU)和Write Capacity Units(WCU)付费(这两个是解耦的)
  - 可以为RCU和WCU添加 auto-scaling 自动扩展模式，并设置利用率
- **On-Demand Mode**(按需模式)
  - Read/Writes根据工作负载自动scale up/down
  - 不需要事先计划capacity 
  - 按需付费，但是较贵
  - 适用于**不可预计**的工作负载（eg：只有某个时段有使用，而其他时间不用，不用时不收费）

### DynamoDB——DAX

- DAX=DynamoDB Accelerator
- 完全托管，高可用，DynamoDB 的无缝**缓存**，可提供高达 10 倍的性能提升
- **通过缓存解决read congestion（读取堵塞）**
- 通过DAX写入到DynamoDB
- 微秒级的缓存读取和查询延迟
- 默认缓存的生存时间TTL是5分钟
- 集群中最多可以有 10 个节点
- 多可用区（推荐生产环境中最少使用 3 个节点）
- 安全（使用 KMS、VPC、IAM、CloudTrail...)

### DynamoDB Streams流

-  表中项目级修改（create/update/delete）是有序流
- Stream records(流记录)可以是：
  - 发送到 **Kinesis Data Streams**
  - 由 **AWS Lambda** 读取
  - 由 **Kinesis Client Library applications**读取
- 数据保留长达 24 小时
- 用例：
  - 实时对变化做出反应（向用户发送welcome电子邮件）
  - 分析
  - 插入派生表(derivative table)
  - 插入 ElasticSearch
  - 实现跨区域复制

![1633254184570](C:\Users\alston\AppData\Roaming\Typora\typora-user-images\1633254184570.png)

### DynamoDB Global Tables

![1633254481603](C:\Users\alston\AppData\Roaming\Typora\typora-user-images\1633254481603.png)

- 使 DynamoDB table可在多个region中以**低延迟**访问

- Active-Active replication（双活复制）
- 应用程序可以read和write任何区域的table
- 必须以启用 DynamoDB Streams 作为先决条件



### DynamoDB –Time To Live (TTL)生存时间

- 时间戳过期后自动删除item
- 用例：通过仅保留数据来减少存储的数据当前项目，遵守监管义务，

### DynamoDB - Indexes索引

- Global Secondary Indexes (GSI) & Local Secondary Indexes (LSI)
- 允许**查询**主键以外的属性



## AWS API Gateway

- AWS Lambda+ API Gateway:无需管理infra
- 支持WebSocket协议
- 处理API版本控制(v1、v2)
- 处理不同的环境（dev,test,prd）
- 处理安全性(身份验证和授权)
- 创建API密钥，处理请求限制
- 导入Swagger/Open API以快速定义APIs
- 转换和验证请求和响应
- 生成SDK和API规范
- 缓存API响应

### API Gateway – Integrations High Level
-  Lambda 函数
  - 调用 Lambda 函数
  - 易于公开由 AWS Lambda 支持的 REST API 
- HTTP
  - 在后端公开 HTTP 端点
  - 示例：premise的internal HTTP API、Application Load Balancer……
  - 为什么？添加速率限制、缓存、用户身份验证、API 密钥等……
- AWS Service
  - 通过API Gateway公开任何AWS API？
  - 示例：启动 AWS Step Function 工作流程，向 SQS 发布消息
  - 为什么？添加身份验证、公开部署、速率控制……

### API Gateway - Endpoint Types

- Edge-Optimized边缘优化 (default):针对全球客户
  - API 请求被路由到最近的 CloudFront 边缘位置，从而改善延迟
  - API 网关仍然**只存在于一个region**
- Regional：
  - 对于同一region的客户
  - 可以手动与 CloudFront 结合（更多地控制缓存策略和分布）
- Private：
  - 只能使用接口 VPC endpoint (ENI) 从您的 VPC 访问
  - 使用resource policy（资源策略）来定义访问

### API Gateway – Security

#### IAM Permissions（IAM 权限）

- 创建 IAM 策略授权并附加到User/Role
- API Gateway 验证调用应用程序所传递的 IAM 权限
- 很适合在您自己的基础架构内提供访问权限
- 利用“**Sig v4**”功能，其中 IAM 凭据位于标头中

每当在考试中看见"Sig v4"，就要想到API Gateway的IAM权限。

![1633438142603](C:\Users\alston\AppData\Roaming\Typora\typora-user-images\1633438142603.png)

#### Lambda Authorizer（Custom Authorizer）

- 使用 AWS Lambda 验证正在传递的header中的token
- 可以缓存身份验证结果
- 有助于使用 OAuth / SAML 等第三方类型的身份验证
- Lambda 必须为用户返回 IAM 策略，并且 IAM 政策将定义用户是否可以调用API。

![1633438133165](C:\Users\alston\AppData\Roaming\Typora\typora-user-images\1633438133165.png)

#### Cognito 用户池

- Cognito 全面管理用户生命周期
- API 网关从 AWS Cognito 自动验证身份
- 无需自定义实施
- **Cognito 仅帮助进行身份验证，而不帮助进行授权**

![1633438675348](C:\Users\alston\AppData\Roaming\Typora\typora-user-images\1633438675348.png)

#### 总结

#### IAM

- 非常适合 AWS 账户中已有的user/role
- 处理认证+授权
- 利用 **Sig v4**

#### Custom Authorizer

- 非常适合第 3 方token
- 在返回 IAM 策略方面非常灵活
- 处理身份验证 + 授权
- 按 Lambda 调用付费

#### Congnito用户池

- 您管理自己的用户池（可以由 Facebook、Google 登录等支持……）
- 无需编写任何自定义代码
- 必须在后端实施授权



