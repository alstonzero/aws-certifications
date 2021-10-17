# Amazon SWF 备忘单

Amazon Simple Workflow Service (SWF) 是一种 Web 服务，可以轻松地跨分布式应用程序组件(distributed application components)协调工作。

创建分布式异步系统(distributed asynchronous systems)作为工作流(workflows)。

支持顺序(sequential)和并行(parallel)处理。

跟踪您通过 API 交互和更新(interact and update)的工作流的状态。

最适合人工支持的工作流程，如订单履行系统或程序请求。

AWS 建议对于新应用程序，客户可以考虑使用 Step Functions 而不是 SWF。

SWF 支持一系列用例的应用程序，包括媒体处理、Web 应用程序后端、业务流程工作流和分析管道(media processing, web application back-ends, business process workflows, and analytics pipelines)，可以设计为任务的协调。

注册是您为每种不同类型的工作流和活动执行的一次性步骤(one-time step)。

SWF 的工作流执行完成时间最长为 1 年。

SWF 使用面向任务(task-oriented)的 API。

SWF 可确保任务分配一次且永不重复。

SWF 跟踪应用程序中的所有任务和事件。

域(domain)是应用程序资源（例如工作流、活动和执行(workflows, activities, and executions)）的逻辑容器。

工作线程是与 Amazon SWF 交互以获取任务、处理接收到的任务并返回结果的程序。

决策器(decide)是一个程序，它根据应用程序逻辑控制任务的协调，即它们的排序、并发和调度。

SWF 应用程序包括以下逻辑组件：

- 域(Domains)。
- 工作流程(Workflows)。
- 活动(Activities)。
- 任务列表(Task Lists)。
- 工人(Workers)。
- 工作流执行(Workflow Execution)。

下表描述了相关服务及其典型用例：

![AWS 应用程序集成服务](https://cdn-digicloud.pressidium.com/wp-content/uploads/2019/10/AWS-Application-Integration-Services-1024x685.jpg)