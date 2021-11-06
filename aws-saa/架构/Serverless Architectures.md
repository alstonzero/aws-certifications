# Serverless Architectures

## Mobile application: MyTodoList

### MyTodoList的requirements

- 公开一个具有HTTPS端点的REST API (Expose as REST API with HTTPS)
- Serverless architecture
- user可以直接和他们在s3中的文件夹进行交互(Users should be able to directly interact with their own folder in S3)
- Users should authenticate through a managed serverless service
- The users can write and read to-dos, but they mostly read them
- The database should scale, and have some high read throughput

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

![](https://raw.githubusercontent.com/alstonzero/aws-certifications/main/aws-saa/%E6%9E%B6%E6%9E%84/pic/serverless%20MyBlog.com.png)

- Serving static content, globally：静态内容储存在S3 bucket中，使用Amazon CloudFront全球范围公开。User将会和CloudFront上的边缘站点交互，从S3中直接获取数据。如何实现securely（安全）？

  - 使用OAI(Origin Access Identity)从CloudFront到S3的源访问身份。在S3上添加bucket policy，只授权OAI，即CloudFront能read而其他不可以。因此user不能直接从S3获取数据，而必须经过CloudFront。

- Adding a public serverless REST API：我们将有一个REST HTTPS cloud talking to Amazon API Gateway，invoke(调用)Lambda Function。也许从DynamoDB查询或读取数据。由于经常read而不是write，因此使用DAX作为缓存层。

  - Leveraging DynamoDB Global Tables：如果要面向全球，可以利用 DynamoDB 全局数据库减少世界部分地区的延迟。

- User Welcome email flow：在DynamoDB中启用变化流(enable streams of changes)。因此创建DynamoDB流，DynamoDB 流将调用 Lambda 函数。Lambda 函数会很特别，它将有一个 IAM 角色，能够使用 Amazon SES。

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


