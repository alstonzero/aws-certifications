# AWS CloudTrail CheetSheet

AWS CloudTrail 是一种 Web 服务，用于记录在您的账户上进行的活动

可以创建 CloudTrail 跟踪，将日志文件传送到 Amazon S3 存储桶。

CloudWatch 与 CloudTrail：

![CloudWatch 与 CloudTrail](https://cdn-digicloud.pressidium.com/wp-content/uploads/2019/03/CloudWatch-vs-CloudTrail-1024x481.jpg)

CloudTrail 是关于记录并保存您的 AWS 账户的 API 调用历史记录。

通过记录对您的帐户执行的操作，提供对用户活动(user activity)的可见性。

API 历史记录支持安全分析(security analysis)、资源更改跟踪(resource change tracking)和合规性审计(compliance auditing)。

### Logs API calls made via：

- AWS 管理控制台(AWS Management Console)。
- AWS 开发工具包(AWS SDKs)。
- 命令行工具(Command line tools)。
- 更高级别的 AWS 服务（例如 CloudFormation）。

### CloudTrail 记录来自大多数 AWS 服务的账户活动和服务事件，并记录以下记录：

- API 调用者的身份。
- API 调用的时间。
- API 调用方的source IP 地址。
- 请求参数。
- AWS 服务返回的响应元素。

CloudTrail 是每个AWS 账户。

可以按区域启用路径，也可以将路径应用于所有区域。

### Trails 可以配置为记录数据事件(log data events)和管理事件：

- **数据事件(Data events)：**这些事件提供对在资源上或资源内执行的资源操作的洞察。这些也称为数据平面操作。
- **管理事件(Management events)：**管理事件让您深入了解对 AWS 账户中的资源执行的管理操作。这些也称为控制平面操作。管理事件还可以包括您账户中发生的非 API 事件。

### 加密

CloudTrail 日志文件使用 S3 服务器端加密**(S3 Server Side Encryption) (SSE)** 进行加密。

您还可以使用 **SSE KMS** 启用加密以提高安全性。

单个 KMS 密钥可用于加密应用于所有区域的跟踪的日志文件。

### 您可以使用 S3 存储桶整合来自多个账户的日志：

1. 在付费账户中开启 CloudTrail。
2. 创建一个允许跨账户访问的存储桶策略。
3. 在其他账户中打开 CloudTrail 并使用付款账户中的存储桶。

您可以将 CloudTrail 与 CloudWatch Logs 集成，以将 CloudTrail 捕获的数据事件传送到 CloudWatch Logs 日志流。

CloudTrail 日志文件完整性验证功能允许您确定 CloudTrail 日志文件在 CloudTrail 将其传送到指定的 Amazon S3 存储桶后是否未更改、删除或修改。