# AWS Global Accelerator

借助于 Global Accelerator，您会得到两个全球静态公有 IP 并将其作为您的应用程序的固定切入点，从而提升其可用性。在后端，增加或删除您的 AWS 应用程序终端节点(endpoints)，如 Network Load Balancers、Application Load Balancer、Network Load Balancer、EC2 实例和弹性 IP，无需做出面向用户的更改。Global Accelerator 自动将您的流量重新路由至您最近的正常运行的可用终端节点，以减少终端节点故障。

### 工作原理

- 利用 AWS 内部网络路由到您的应用
- 为您的应用创建2 个任播 (Anycast)IP
  - Unicast IP：one server holds one IP address
  - Anycast IP：all servers hold the same IP address and the client is routed to the nearest one.所有服务器都拥有相同的 IP 地址，并且客户端被路由到最近的一个

- Anycast IP 直接把发送流量到边缘站点(Edge Loactions)
- 边缘站点(Edge Loactions)再把发送流量到您的应用程序

### 功能简介

- 适用于**弹性 IP(Elastic IP)、EC2 实例、ALB、NLB、公共或私有**
- 一致的性能
  - 智能路由(Intelligent routing)：实现了最低延迟和快速区域故障转移
  - 客户端缓存没有问题（因为 IP 不会改变）
  - 使用AWS的内部网络
- 健康检查(Health Check)
  - Global Accelerator 对应用程序进行健康检查
  - 帮助您的应用程序全球化（故障转移不到 1 分钟for unhealthy）
  -  非常适合灾难恢复（归功于健康检查）
- 安全
  - 只需将 2 个外部 IP 列入白名单
  - 借助 AWS Shield 提供 DDoS 保护

## AWS Global Accelerator vs CloudFront

### 相同点

- 都使用 AWS 全球网络(AWS global network)及其在世界各地的边缘站点(edge locations)
- 都与AWS Shield 集成以提供DDoS 保护。

### 不同点

#### CloudFront

- 提高了可缓存内容(cacheable content)（例如图像和视频）的性能
- 动态内容（例如 API 加速和动态站点交付(dynamic site delivery)）
- 内容在边缘提供

#### Global Accelerator

- 提高了 TCP 或 UDP 上各种应用程序的性能
- 在边缘将数据包代理到在一个或多个AWS 区域中运行的应用程序。
- 非常**适合non-HTTP** 用例，例如游戏 (UDP)、物联网 (MQTT) 或 Voice over IP.
- 适用于需要**静态 IP(Elastic IP)** 地址的 HTTP 用例
- 适用于需要确定性、快速区域故障转移的 HTTP 用例



若问题里面有使用elastic IP，则使用Global加速器。