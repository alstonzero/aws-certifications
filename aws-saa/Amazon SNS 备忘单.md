# 亚马逊 SNS 备忘单

Amazon Simple Notification Service (Amazon SNS) 是一项 Web 服务，可让您轻松地从云端设置set-up、操作(operate)和发送(send)通知。

Amazon SNS 用于构建和集成松散耦合(loosely-coupled)的分布式应用程序(distributed applications)。

提供即时的、基于推送的交付(push-based delivery)（无轮询no polling）。

使用简单的 API 并与应用程序轻松集成。

通过多种传输协议提供灵活的消息传递。

以低廉的现收现付模式提供，无需预付费用。

基于 Web 的 AWS 管理控制台提供简单的点击式界面。

数据类型为 JSON。

SNS 支持广泛的需求，包括事件通知、监控应用程序、工作流系统、时间敏感信息更新、移动应用程序以及任何其他生成或使用通知的应用程序(event notification, monitoring applications, workflow systems, time-sensitive information updates, mobile applications, and any other application that generates or consumes notifications.)。

### SNS可以与许多AWS服务集成

- 许多AWS服务能直接发送data到SNS进行通知
- CloudWatch(for alarm)
- Auto Scaling Groups notifications
- Amazon S3(on bucket events)
- CloudFormation(upon state changes=>failed to build,etc)

### SNS订阅者(SNS的接收对象)：

- HTTP。
- HTTPS。
- Email。
- Email-JSON。
- SQS。
- Application。
- Lambda。

### SNS 支持通过多种传输协议的通知：

- HTTP/HTTPS – 订阅者指定一个 URL 作为订阅注册的一部分。
- Email/Email-JSON – 消息作为email（text-based or JSON-object）发送到注册地址。
- SQS – 用户可以指定一个 SQS 标准队列(standard queue)作为端点。
- SMS – 消息作为 SMS 文本消息发送到注册的电话号码。

主题名称限制为 256 个字符。

SNS 支持对经过身份验证的调用进行 CloudTrail 审计(CloudTrail auditing)。

SNS 为其接收的所有消息（跨多个可用区）提供持久存储。

用户为每 100 万次 Amazon SNS 请求支付 0.50 美元，通过 HTTP 每 100,000 次通知传递支付 0.06 美元，通过电子邮件每 100,000 次通知传递支付 2.00 美元。

下表描述了相关服务及其典型用例：

![AWS 应用程序集成服务](https://cdn-digicloud.pressidium.com/wp-content/uploads/2019/10/AWS-Application-Integration-Services-1024x685.jpg)