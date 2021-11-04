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