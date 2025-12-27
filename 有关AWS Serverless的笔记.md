### 有关AWS Serverless的笔记

所谓的serverless是个什么意思。其实就是不需要你管理服务器。通常来说，如果我们部署一个程序，服务进入服务器，让其投入使用，我们需要维护其在服务器的状态，而且也要定期维护服务器硬件等等设施。而serverless服务可以让你可以达成在云上挂载你的服务，而不需要管理服务器相关的事情。



以下是有关serverless的详细解释：

Serverless（无服务器架构）是一种云计算模型，其核心思想是让开发者**专注于编写业务代码**，而无需管理底层服务器等基础设施。云服务提供商会自动分配和调度运行应用所需的资源。

### 核心特点：

1. **无服务器管理**
   - 开发者无需预置或维护服务器（如虚拟机、容器）
   - 云平台自动处理服务器的配置、维护、扩展
2. **按使用付费**
   - 计费基于实际执行时间/资源消耗（如每100ms计费）
   - 空闲时不产生费用（区别于传统云主机的持续付费）
3. **事件驱动**
   - 函数（Function）由特定事件触发执行（如HTTP请求、文件上传、数据库变更）
   - 常见触发器示例：API网关、对象存储事件、消息队列
4. **自动弹性伸缩**
   - 从零扩展到数千并发实例完全自动化
   - 突发流量时自动扩容，流量下降后缩容至零

------

### 主流技术实现：

- **FaaS（Function as a Service）**
  函数即服务，如：
  ▶️ AWS Lambda
  ▶️ 阿里云函数计算
  ▶️ 腾讯云SCF
  ▶️ 谷歌Cloud Functions
- **BaaS（Backend as a Service）**
  后端即服务，如：
  ▶️ Firebase（数据库/认证）
  ▶️ Auth0（身份管理）
  ▶️ Supabase（开源BaaS）

------

### 典型应用场景：

1. **API后端服务**
   - 通过API Gateway触发函数处理HTTP请求
   - 示例：用户注册、订单处理接口
2. **数据处理流水线**
   - 文件上传到对象存储后触发处理
   - 案例：图片压缩、视频转码
3. **定时任务**
   - 按Cron表达式执行周期性任务
   - 如：每日数据报表生成
4. **聊天机器人/Webhook**
   - 响应第三方服务的回调事件
   - 例如Slack消息处理、支付回调

------

### 优势 vs 挑战：

**✅ 优势**

- 降低运维复杂度（无需打补丁/监控服务器）
- 成本优化（仅为实际运行付费）
- 毫秒级弹性扩展能力

**⚠️ 挑战**

- 冷启动延迟（首次调用可能需要初始化）
- 调试困难（本地环境与云端差异）
- 状态管理限制（需配合外部存储服务）

------

### 演进趋势：

1. **容器化融合**
   AWS Fargate、Google Cloud Run等将容器与Serverless结合
2. **长时任务支持**
   突破传统函数15分钟执行限制（如AWS Lambda现支持15分钟）
3. **本地开发体验提升**
   工具链改进（如Serverless Framework、SAM Local）

通过Serverless架构，开发者能更专注于业务逻辑创新，加速产品迭代速度。根据Flexera 2023云报告，目前已有40%的企业在生产环境中采用Serverless技术。



有关Lambda:

**AWS Lambda** 是一个**无服务器计算服务**，你只需要上传代码，AWS 就会为你自动处理服务器的分配、扩容、执行、并发处理等问题。

你只需：

- 写代码（比如 Python、Node.js、Java 等）
- 设置触发条件（比如 S3 上传、API 请求、SQS 消息、定时器等）
- 剩下的全交给 AWS。



## 🚀 特点总结：

| 特点                          | 说明                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| ✅ 无需服务器                  | 无需预先部署服务器，按需启动，按调用计费                     |
| 🔄 自动扩展                    | 高并发时会自动创建多个实例处理                               |
| 💸 精准计费                    | 按 **调用次数 + 执行时间**（毫秒）收费                       |
| 💥 触发机制广泛                | 支持 S3、API Gateway、SNS、SQS、DynamoDB、EventBridge 等触发器 |
| 💻 多种语言                    | 支持 Python、Node.js、Java、.NET、Go、Ruby 等                |
| 🪢 可以和其他 AWS 服务无缝整合 | 常用于构建事件驱动架构（EDA）或微服务                        |



有关Lambda 函数的SAM模板：这个模板当你创建了Lambda函数后便会产生。

这个 AWS SAM（Serverless Application Model）模板定义了：

- 一个由 SQS 触发的 Lambda 函数

- 一个 SQS 队列

- 所需的权限与相关设置

当你改写该模板内的内容时，这个Lambda函数相关的东西会被改变，取决于你改了什么。



| 你修改了什么            | 结果（在部署后）                                             |
| ----------------------- | ------------------------------------------------------------ |
| `MemorySize`, `Timeout` | Lambda 的内存或执行时间会更新                                |
| `Handler`, `Runtime`    | Lambda 的运行环境或入口函数会更新                            |
| `CodeUri`               | 如果配合 `sam build && sam deploy`，新的代码包会上传并替换旧的代码 |
| `Events`                | 会创建、修改或删除触发器（例如 SQS 事件源）                  |
| `Policies`              | 会更新 Lambda 的 IAM 权限（影响它能访问什么 AWS 资源）       |

------

### 🚀 如何让修改生效？

你需要 **重新部署**：

```
bashCopyEditsam build     # 构建项目（打包代码）
sam deploy    # 部署到 AWS（将改动同步上去）
```

如果你用了 `--guided` 参数：

```
bash


CopyEdit
sam deploy --guided
```

系统会引导你选择：

- Stack 名字
- 部署区域
- 是否保存配置
- 是否允许自动创建 IAM 角色等

------

### ⚠️ 注意：

- 如果你只是改了 `template.yaml` 但没运行 `sam deploy`，**Lambda 函数在 AWS 上是不会变的**。
- 改了 `CodeUri` 指向的代码文件夹，但没 build/deploy，也**不会更新代码**。
- 如果你使用 **AWS 控制台直接修改 Lambda 函数**，这些改动**不会同步回 `template.yaml`**，下一次 `sam deploy` 会覆盖掉它！

------

### 🧪 示例：修改内存大小

你将模板中的内存从 128 改成 512：

```
yaml


CopyEdit
MemorySize: 512
```

然后执行：

```
bashCopyEditsam build
sam deploy
```

部署成功后，AWS 控制台里的 Lambda 会显示更新为 512MB 内存。





AWSTemplateFormatVersion: '2010-09-09'  # CloudFormation 标准版本头
Transform: AWS::Serverless-2016-10-31   # 使用 SAM 转换器，支持更简单的语法
Description: 一个描述 Lambda 函数的 SAM 模板

Resources:
  ###### 定义 Lambda 函数资源
  scratchfromSQS:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: .  # 函数代码目录（当前目录），部署时会被打包成 zip
      Description: ''  # 可选的描述，这里留空
      MemorySize: 128  # 分配 128MB 内存
      Timeout: 3  # 最长执行时间为 3 秒
      Handler: lambda_function.lambda_handler  # Python 的函数入口（Lambd_function文件内的叫做lambda_handler 的function）
      Runtime: python3.13  # 使用 Python 3.13 运行时
      Architectures:
        - x86_64  # 使用 64 位 x86 架构
            EphemeralStorage:
                Size: 512  # 分配 512MB 临时存储（/tmp 文件夹）

      # 事件调用配置
      EventInvokeConfig:
        MaximumEventAgeInSeconds: 21600  # 允许事件保留最长 6 小时
        MaximumRetryAttempts: 2  # 最多重试 2 次失败事件
    
      PackageType: Zip  # 打包方式为 Zip（标准）
    
      # Lambda 所需权限配置（使用 IAM 策略）
      Policies:
        - Statement:
            - Effect: Allow
              Action:
                - kinesis:*  # 允许所有 Kinesis 操作（权限范围较宽）
              Resource: '*'  # 所有资源（建议在生产环境中限制范围）
            - Action:
                - sqs:*  # 允许所有 SQS 操作
              Effect: Allow
              Resource: '*'  # 同样所有资源，建议缩小范围到特定队列
            - Effect: Allow
              Action:
                - logs:CreateLogGroup  # 允许创建日志组
              Resource: arn:aws:logs:ap-northeast-1:975049994847:*  # 指定区域和账户 ID
            - Effect: Allow
              Action:
                - logs:CreateLogStream
                - logs:PutLogEvents  # 允许创建日志流和写入日志
              Resource:
                - arn:aws:logs:ap-northeast-1:9757:log-group:/aws/lambda/scratch-from-SQS:*  # 可能缺失完整账户 ID，请确认
    
      RecursiveLoop: Terminate  # 防止无限递归调用（避免死循环）
      SnapStart:
        ApplyOn: None  # 不启用 SnapStart（冷启动优化功能）
    
      # 定义事件源（即 SQS 队列触发器）
      Events:
        SQS1:
          Type: SQS
          Properties:
            Queue:
              Fn::GetAtt:
                - SQSQueue1  # 引用下方定义的队列
                - Arn
            BatchSize: 10  # 每次最多处理 10 条消息（SQS 触发器上限）
    
      RuntimeManagementConfig:
        UpdateRuntimeOn: Auto  # 自动更新运行时的小版本和补丁

定义触发 Lambda 的 SQS 队列

  SQSQueue1:
    Type: AWS::SQS::Queue
    Properties:
      QueueName: SQSQueue1  # 设置队列名称
      SqsManagedSseEnabled: true  # 启用 SQS 托管的服务端加密（SSE）



Lambda的极限：

1.对于执行Lambda函数来说：其可用RAM最大为10GB，最小为128MB，CPU是随着RAM增长而增长的。

2.最大执行事件为900s，也就是15分钟。

3.部署的最大包大小：50MB（压缩的情况），250MB（不压缩的情况）

4.环境变量：所有程序执行所用到的环境变量不能超过4KB

5.并发：一个Lambda function默认支持每个区域（region）最高1000个并发（同时运行1000个相同的Lambda function），可以增加。而如果某一时刻，并发超过了1000，比如1200，开始的1000个会顺利执行，剩余的200个Lambda function的执行会被拒绝或延迟。在Lambda中这种现象被称为Throttling. 具体来说，如果Lambda function，或与其直接连接的其他服务与Lambda function每秒交互的次数超过的当前设置的并发量或者你自己设置的最大并发量，则会出发ThrottleError -429

针对并发还有一点需要注意的事情：最高1000的并发是对于你一个账号的一个区域的所有Lambda function。所以如果你其中一个Lambda function的并发超过了1000，那么你账号下此区域的所有其它Lambda function在被访问时都会报429 error。

对于此种时间的解决方法是：1.预热function instance让其可以快速处理事务，而不是先冷启动，再处理事务（使用ASG）。2.对重要的Lambda function单独设置并发上限。以免其影响其他的Lambda function



6.最大网络吞吐量：10GB，根据RAM大小和VPC的设定

7.冷启动：10秒 这里的冷启动是指，当发来的消息数量超过现有的Lambda function线程数时，需要增加一些新的来应对突如其来的大量数据，所以，需要冷启动（从零启动）新线程。



在以下情况不要使用Lambda function：

1.你的程序处理一次事务需要15分钟以上的时间

2.你的程序需要底层OS的支撑，或需要后台多线程运行

3.你需要GPU做演算或需要使用特殊的驱动器

4.需要长期应对大量的数据或文件

5.你需要高度稳定和高速网络，或你需要固定的IP



关于Lambda的同步调用（Synchronous Invocation）或异步调用（Asynchronous Invocation）

同步调用大多产生于API，SDK的调用，直接的invoke。在同步调用的情况下，如果出现目前的并发超过了系统设置的并发上限，则会产生429错误，但同步调用不会处理此错误，所以你必须手动处理。而异步调用虽然也会出现429错误，但当出现错误时，其会重试两次。而且异步调用支持SNS或SQS类型的Dead Letter Queue (DLQ) 。当两次尝试都不成功时，根据设置，系统可以向SQS或SNS发出此次event，或者把其丢弃。同步调用不支持。在同步调用里，如果是API调用所产生的429错误，会显示too many requests。

同步请求报文例子：

response = lambda_client.invoke(
    FunctionName="MyFunction",
    InvocationType="RequestResponse",  # sync
    Payload=json.dumps(my_event)
)



异步请求报文例子：

response = lambda_client.invoke(
    FunctionName="MyFunction",
    InvocationType="Event",  # async
    Payload=json.dumps(my_event)
)



异步Lambda和DLQ联动例子：

 ┌─────────────┐
 │ Event source│ ← SNS / S3 / EventBridge
 └────┬────────┘
      │
      ▼
┌──────────────┐
│   Lambda     │ ← async invoke
└────┬─────────┘
     │
     ├── If success → ✅ Done
     │
     ├── If fail → Retry 1
     │
     ├── If fail → Retry 2
     │
     └── If fail → ❌ Send to DLQ (SQS or SNS)

DLQ 信息内容：

{
  "event": { ... },             // The full original event that Lambda received
  "timestamp": "2025-04-11T08:12:00Z",
  "functionName": "my-lambda-function",
  "requestId": "4ba8-xxx-yyy-zzz"
}

有关Lambda Snapstart

正常来说，当你call你的Lambda function。其会先初始化，然后启动并处理事务，最后关闭。

所以会有一个初始化的过程，这个初始化的过程会导致其他服务或外部用户使用Lambda function会有一定延迟。有一个办法可以减缓延迟。就是启用snapstart。启用之后，你需要发布一版新的function 版本。然后系统会运行一次。在程序初始化完成后，会截取一分快照。此快照包含程序环境和RAM的状态。所以当下一次该function被调用时，系统会自动使用此快照来跳过初始化阶段。



CloudFront Function and Lambda@Edge

CloundFront Function实际上是部署在各地边缘位置的CDN服务器上，用于对用户请求做特殊处理的function。其由毫秒级别的处理延迟，且每秒可以支持百万级别的访问量。其负责对客户端与CDN服务器之间的交互做客制化处理。在请求通过CDN传递到源服务器或从源服务器的回应通过CDN到达客户端之前，可以使用CloudFront Function对此进行处理。此功能为cloudFront的原生功能。无需其他组件便可使用。但最大function的代码大小为10KB

请注意cloudFront Function只支持javascript.

而Lambda@Edge和CloudFront十分相似。其是由Node.js或python编写的。其处理并发能力相对cloudFront来说弱一点，最高到1000个请求每秒。且其处理时间比CloudFront function执行时间要长。最大需要5到10秒。



### **核心区别**

| **维度**         | **CloudFront Functions**                              | **Lambda@Edge**                                              |
| :--------------- | :---------------------------------------------------- | :----------------------------------------------------------- |
| **定位**         | 轻量级边缘计算（简单逻辑）                            | 功能强大的边缘计算（复杂逻辑）                               |
| **执行位置**     | 全球边缘节点（更靠近用户）                            | 区域性边缘节点（相对靠近源站）                               |
| **执行阶段**     | 仅支持 **Viewer Request/Response**（用户端请求/响应） | 支持所有阶段： - Viewer Request/Response - Origin Request/Response |
| **编程语言**     | 仅支持 JavaScript（ES5，部分ES6+特性）                | 支持 Node.js、Python、Java、Go 等（完整运行时）              |
| **执行时间限制** | 最大 **1毫秒**（超严格）                              | 最大 **5秒**（Viewer阶段）或 **30秒**（Origin阶段）          |
| **内存限制**     | 约 **2MB**                                            | 最高 **10GB**（取决于配置）                                  |
| **网络访问**     | 不支持（无法调用外部API或访问VPC）                    | 支持（可访问互联网、VPC、AWS服务）                           |
| **文件系统访问** | 不支持                                                | 支持（临时存储，/tmp目录）                                   |
| **成本**         | 极低（按请求次数计费，每百万次约 $0.10）              | 较高（按执行时间和内存消耗计费）                             |
| **适用场景**     | 简单请求/响应处理（毫秒级延迟敏感场景）               | 复杂逻辑处理（如鉴权、动态内容生成、HTTP修改）               |

------

### **适用场景示例**

#### **1. CloudFront Functions**

- **修改HTTP头部**：
  添加安全头（如 `X-Content-Type-Options`）或自定义头。
- **URL重写或重定向**：
  根据设备类型重定向到移动版页面。
- **简单的A/B测试**：
  基于Cookie或Query参数路由请求。
- **请求过滤**：
  阻止特定User-Agent的请求。
- **缓存键优化**：
  标准化Query参数以提升缓存命中率。

#### **2. Lambda@Edge**

- **动态内容生成**：
  根据用户地理位置返回不同页面。
- **身份验证/鉴权**：
  验证JWT Token，拦截未授权请求。
- **图像处理**：
  实时调整图片尺寸（需调用S3或图像处理库）。
- **源站路由**：
  根据请求头将流量分发到不同后端服务器。
- **自定义错误页面**：
  拦截4xx/5xx错误，返回友好页面。
- **日志增强**：
  在请求中添加自定义字段并记录到CloudWatch。

------

### **选择建议**

- **选择 CloudFront Functions**：
  - 需要极低延迟（毫秒级）。
  - 仅需简单逻辑（如修改头、URL重定向）。
  - 成本敏感，无需复杂计算或外部调用。
- **选择 Lambda@Edge**：
  - 需要复杂逻辑（如鉴权、动态内容生成）。
  - 需访问外部服务（如数据库、API）。
  - 需要处理Origin阶段的逻辑（如修改源站请求）。
  - 需要更长的执行时间或更大内存。



请注意，无论是cloudFront Function还是Lambda@Edge，都无法连接到你个人的VPC网络里，他们都分布于全球的边缘地点。所以如果你不把Lambda function部署到你个人的VPC中，那么此Lambda function将无法和其他任意你已经部署的私有程序交互（任何部署在你个人的VPC网络里的服务，如EC2 instance，RDS，ENI等等）。

对于所有的AWS服务来说，在绝大部分的情况下，如果一个AWS服务不是部署在你个人的VPC网络中，那么他将无法访问到你私人VPC网络内部署的任何资源和服务。除非你对其访问的权限和许可进行了特殊的设置。或你将你的资源设为可公共访问，或创建一个VPC endpoint。



有关Lambda function和其他AWS service联动的注意点：如果你需要Lambda function和RDS相连，而你的RDS在你个人的VPC中，而Lambda function不在VPC中，此时，可以使用ENI构建一个接入点，让Lambda function可以访问到RDS。

如果Lambda function直接连接你的RDS数据库，那么，在处理大量请求的情况下，会导致其建立过多的database connection。从而浪费性能资源。在这种情况下，使用RDS Proxy代替与RDS的直接连接。







有关cloudFront Function和Lambda@Edge在执行阶段的解析：

[User Browser] <---> [CloudFront] <---> [Origin Server (e.g., S3, EC2)]

把这个流程分成两个阶段，一个是用户的浏览器和CDN服务器交互的阶段，另一个是CDN服务器和源服务器进行交互的阶段。每个阶段都有对应的请求和回应过程，而这四个时间点便是cloudFront Function和Lambda@Edge被触发的时间点。

第一阶段的两个是Viewer request,Viewer Response.第二阶段的两个为Origin Request,Origin Response.

view request: 指用户向CDN发送请求，被CDN服务器收到的瞬间。cloudFront function在此时可以对收到的请求做出修改或处理。如重定向，IP白名单检查，修改请求头部内容等。

Origin request：指CDN向后方的源服务器发送请求的时刻，在发送请求前，可以为元请求增加头部，进行授权等等。

Origin response：指源服务器对请求做出回应，回应送到CDN服务器的时候，此时可以进行对回应来源的检查和修改（URL）

view response: 指CDN服务器向客户端发送回应的时候，在发送前，可以增加头部，cookie，和对回应内容做最终修改。



##### 有关DynamoDB：

> **Amazon DynamoDB** 是 AWS 提供的一个**完全托管的 NoSQL 数据库服务**，支持**键值（Key-Value）和文档型数据结构**，并可在任何规模下提供**个位数毫秒级的延迟性能**。

它专为**高性能、可扩展性和低运维**而设计。

------

## 🔧 **核心概念**

### 1. **表（Table）**

- 是数据的主要容器。
- 类似关系型数据库的表，但**无固定结构**（schema-less）。
- 需要定义：
  - **主键**（分区键或分区+排序键）
  - 可选的 **全局/本地二级索引**

### 2. **项目（Item）**

- 相当于一行数据。
- 每个 item 必须包含主键。
- 其他属性可自由扩展，不必统一。

### 3. **属性（Attribute）**

- 相当于一列数据。
- 类型支持：字符串、数字、布尔值、列表、映射（Map）等。

------

## 🔑 **主键类型**

| 类型                | 描述                                 | 示例                                      |
| ------------------- | ------------------------------------ | ----------------------------------------- |
| **仅分区键**        | 唯一标识每条数据                     | `user_id = 123`                           |
| **分区键 + 排序键** | 组合主键，允许一个分区键对应多条数据 | `user_id = 123 且 timestamp = 2025-04-15` |

------

## ⚡ **性能特性**

- **毫秒级读写延迟**
- 自动扩展，可处理**每秒百万级请求**
- 支持 **预配置模式** 或 **按需模式**
- **多可用区容灾与高可用**

------

## 🧠 **关键功能**

| 功能                          | 描述                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| 🔄 **自动扩展**                | 根据访问流量自动调整吞吐量                                   |
| 🔁 **Streams（数据流）**       | 实时捕捉数据变更（可触发 Lambda、用于同步或分析）            |
| 📜 **TTL（生存时间）**         | 可设定数据过期自动删除                                       |
| 🔒 **细粒度访问控制**          | 与 IAM 完整集成                                              |
| 🧮 **事务（Transactions）**    | 支持 ACID 的多项事务操作                                     |
| 🗺️ **全局表（Global Tables）** | 支持多地区同时读写（多活架构）                               |
| ⚡ **DAX（DynamoDB 加速器）**  | 内存级缓存，实现微秒级读取，需要对应大量的读工作的话，推荐使用 |
| 🧾 **二级索引**                | 提高查询灵活性（GSI 和 LSI）                                 |

------

## 📈 **计费模式**

| 模式           | 描述                                            |
| -------------- | ----------------------------------------------- |
| **按需模式**   | 按调用次数付费，适合不确定访问量场景            |
| **预配置模式** | 提前设定读写容量单位（RCU/WCU），大规模下更经济 |
| ⚠️ **存储**     | 按每月存储容量（GB/月）计费                     |
| ⚡ **DAX**      | 按每小时节点数计费                              |

------

## 🔍 **读写操作类型**

| 操作类型                 | 是否支持强一致性 | 延迟  | 说明                 |
| ------------------------ | ---------------- | ----- | -------------------- |
| **GetItem / Query**      | ✅ 可选强一致     | ~毫秒 | 可选强一致或最终一致 |
| **PutItem / UpdateItem** | ✅ 原子操作       | ~毫秒 | 可设置条件更新       |
| **Scan**                 | ❌ 仅支持最终一致 | 较慢  | 谨慎用于大表全表扫描 |

------

## 🧰 **适用场景**

✅ 适合使用 DynamoDB 的场景：

- 需要**超低延迟高并发读写**
- 数据结构为**键值对或文档型**
- 希望使用**自动扩容、免运维、高可用**架构
- 需要**实时分析或触发器机制**（通过 Stream + Lambda）
- 希望构建**Serverless 应用**

❌ 不适合的场景：

- 需要**复杂查询或多表关联**
- 数据为**强关系型结构**
- 访问模式**高度动态且无索引支持**

------

## 🔗 **典型应用场景**

| 场景               | 描述                                            |
| ------------------ | ----------------------------------------------- |
| 🌐 **用户资料存储** | 根据用户 ID 快速读取                            |
| 🚚 **IoT 设备数据** | 存储时间序列数据                                |
| 🎮 **游戏会话信息** | 实时积分榜、玩家库存数据                        |
| 📦 **电商购物车**   | 存储用户购物记录                                |
| 🧠 **分析数据管道** | 使用 Stream 结合 Kinesis 或 Lambda 做数据流处理 |

------

## 💡 **示例数据项（JSON格式）**

```
jsonCopyEdit{
  "user_id": "123",
  "username": "tanhaiei",
  "joined_at": "2024-01-01",
  "settings": {
    "theme": "dark",
    "notifications": true
  }
}
```



请注意：

1.DynamoDB里，每张表都要在创建时确定主键（由Partition key和Sort key组成）

2.理论上，每张表可以有无数的item（即一行数据）。且每一行数据的参数可以不近相同。意思是你有一行数据，除了主键外，还有一个日期和年龄。再插入一行数据，除了主键相同外，可以插入别的参数，如地址，邮编等。老数据不会因为新数据中有别的参数而收到影响。

DynamoDB Global Table:一个让DynamoDB的表可以在多个区域下，以较低延迟的方式交互的功能。其本质是个备份复制功能。这个功能可以让你的DynamoDB的表在多个AWS区域内被自动同步。且每个区域的表都可被读写。但此功能需要DynamoDB Stream为基础。如果你的程序同时将数据写入不同区域的DynamoDB表中，那么同步时，对于同一份数据，以时间戳最新的为准。





DynamoDB Stream: 这是一个DynamoDB和其他AWS服务联动下实现的功能。在DynamoDB的设置中，可以启用DynamoDB Stream。然后对什么类型的data需要记录进行设置。然后需要设置一个Lambda function来读取记录。这些记录会被存放在DynamoDB内的一个buffer里，此份记录只会存在24小时，所以你不手动读取它的话，24小时后，此纪录便会消失。所以你需要将其读取，转换成json或txt文件，或者将其log到cloudwatch中，或将记录输入到另一个数据库中。

![image-20250416105922746](C:\Users\msduser\Desktop\学习笔记\assets\image-20250416105922746.png)



请注意，DynamoDB只能触发Lamdba function，其不能触发其他AWS服务，所以，如果有如API等需要触发或交互的话，建议用Lambda function作为中介。让Lambda function完成与其他AWS service的交互。



LSM 树（Log-Structured Merge-Tree）与自动分片（Auto-Sharding）的结合是**现代分布式数据库（如 DynamoDB、Cassandra、HBase）实现高吞吐、高扩展性的核心架构**。其设计思想是通过分层存储和分布式分片来平衡写入性能、查询效率与扩展性。以下是深度技术解析：

------

### 一、LSM 树的核心机制

LSM 树通过 **"顺序写入 + 后台合并"** 实现写入优化，其核心组件和工作流程如下：

#### 1. **层级结构**

- **MemTable**：内存中的有序数据结构（通常用跳表或 B+ 树实现），负责接收实时写入。
- **Immutable MemTable**：MemTable 写满后冻结为不可变状态，准备写入磁盘。
- **SSTable（Sorted String Table）**：磁盘上的有序数据文件，按层级（L0, L1, ..., Ln）组织，层级越高数据越旧。
- **WAL（Write-Ahead Log）**：持久化日志，用于崩溃恢复。

#### 2. **写入流程**

plaintext

复制

```
写入请求 → 写入 WAL → 写入 MemTable → 返回成功
(若 MemTable 写满) → 转为 Immutable MemTable → 异步刷入 L0 SSTable
```

#### 3. **读取流程**

plaintext

复制

```
读取请求 → 先查 MemTable → 未命中则逐层扫描 SSTable（L0 → L1 → ... → Ln）
→ 合并所有找到的数据版本 → 返回最新结果
```

#### 4. **Compaction（合并）**

- **任务**：将多个小 SSTable 合并为更大的 SSTable，并清除过期/重复数据。
- **策略**：
  - **Size-Tiered**（Cassandra）：合并相似大小的 SSTable。
  - **Leveled**（RocksDB）：每层 SSTable 大小固定，逐层合并。
- **影响**：减少读取 I/O 放大，但占用 CPU 和 I/O 资源。

------

### 二、自动分片（Auto-Sharding）的核心机制

自动分片通过 **动态分配数据到多个物理节点** 实现水平扩展，关键设计包括：

#### 1. **分片策略**

- **Hash-Based Sharding**（如 DynamoDB）：
  - 计算分区键的哈希值，映射到固定范围（如 0~2^128）。
  - 优点：数据分布均匀，避免热点。
  - 缺点：范围查询需跨分片。
- **Range-Based Sharding**（如 TiDB）：
  - 按主键范围划分（如 user_id 1-1000 分片A，1001-2000 分片B）。
  - 优点：支持高效范围查询。
  - 缺点：需处理数据倾斜（热点区间）。

#### 2. **分片元数据管理**

- **中心化协调器**（如 MongoDB Config Server）：
  - 存储分片映射表（Shard Key → Shard Node）。
  - 优点：简单易实现。
  - 缺点：单点瓶颈。
- **去中心化 Gossip 协议**（如 Cassandra）：
  - 节点间通过 Gossip 协议同步分片信息。
  - 优点：无单点故障。
  - 缺点：一致性较弱。

#### 3. **动态分片调整**

- **分片分裂（Split）**：当单个分片数据量超过阈值（如 10GB），自动拆分为两个子分片。
- **分片合并（Merge）**：相邻分片数据量过小时合并，减少元数据开销。
- **数据迁移（Rebalance）**：新节点加入时，自动迁移部分分片到新节点。

------

### 三、LSM 树 + 自动分片的协同设计

#### 1. **写入路径优化**

- **分片内并行**：每个分片独立维护自己的 LSM 树层级，写入时仅需锁定当前分片。
- **批量写入**：分片将多个写入请求合并为批量操作，减少 Compaction 频率。

#### 2. **查询路径优化**

- **分区键路由**：查询时先根据分区键定位分片，避免全集群扫描。
- **分片内 Bloom Filter**：每个 SSTable 附带布隆过滤器，快速判断 Key 是否存在。

#### 3. **Compaction 与分片联调**

- **分片级 Compaction**：每个分片独立执行 Compaction，避免跨节点协调。
- **优先级调度**：热点分片的 Compaction 任务优先执行，减少查询延迟。

#### 4. **故障恢复**

- **多副本 + LSM WAL**：每个分片有多个副本，故障时通过 WAL 和 SSTable 快速重建。
- **一致性协议**：使用 Raft/Paxos 同步分片副本的写入操作。

------

### 四、性能特性对比（LSM+分片 vs B+树+分片）

| 指标         | LSM + 自动分片                  | B+树 + 分片                |
| :----------- | :------------------------------ | :------------------------- |
| **写入吞吐** | ⭐⭐⭐⭐⭐（顺序写 + 批处理）        | ⭐⭐（随机写导致页分裂）     |
| **读取延迟** | ⭐⭐（需合并多级 SSTable）        | ⭐⭐⭐⭐（B+树高度固定）       |
| **扩展性**   | ⭐⭐⭐⭐⭐（动态分片 + 无锁设计）    | ⭐⭐⭐（分片锁冲突增加）      |
| **存储放大** | 较高（多版本数据 + Compaction） | 较低                       |
| **适用场景** | 写密集、OLAP、日志存储          | 读密集、OLTP、强一致性需求 |

------

### 五、典型应用案例

#### 1. **DynamoDB**

- **LSM 优化**：通过自适应 Compaction 减少写入放大。
- **分片策略**：哈希分片 + 动态调整吞吐容量（RCU/WCU）。

#### 2. **Cassandra**

- **LSM 实现**：Size-Tiered Compaction + 可调合并阈值。
- **分片策略**：一致性哈希（Virtual Nodes） + Gossip 协议。

#### 3. **TiDB（存储引擎 TiKV）**

- **LSM 变种**：RocksDB 引擎 + Leveled Compaction。
- **分片策略**：Range-Based 分片 + Raft 多副本。

------

### 六、挑战与解决方案

| 挑战                          | 解决方案                                                    |
| :---------------------------- | :---------------------------------------------------------- |
| **Compaction 引发的写停顿**   | 限流 Compaction（如 RocksDB 的 `rate_limiter`）             |
| **热点分片（Hot Partition）** | 动态分片分裂 + 请求限流（如 DynamoDB 的 Burst Capacity）    |
| **跨分片事务**                | 2PC（两阶段提交） + 乐观锁（如 Google Spanner 的 TrueTime） |
| **数据倾斜**                  | 复合分区键（如 `user_id + timestamp`） + 动态负载均衡       |

------

### 七、总结

**LSM 树 + 自动分片** 的组合是分布式存储系统的黄金标准架构：

- **写入优势**：LSM 的顺序写和批处理特性，天然适合高吞吐场景。
- **扩展性优势**：自动分片实现近乎无限的水平扩展。
- **代价**：需在查询延迟、存储放大和 Compaction 开销之间权衡。

对于现代分布式系统（如 IoT 数据采集、实时分析、广告点击流），此架构能有效平衡性能与成本，是 NoSQL 和 NewSQL 数据库的基石设计。





API Gateway:

AWS旗下的API管理服务。在此你可以创建，使用和管理API.API gateway可以与Lambda function，EC2 instance，DynamoDB，S3 bucket,其他http 端点对接。支持IAM验证,API key,JWT tokens ,外部用户可以使用Cognito。用户可以自行调整每秒API可以处理的请求数。

在API Gateway里，可以选择使用http API,REST API,或者Websocket API。

在创建Web socket API的时候，会有选择路由（route）的环节，有3个路由可供选择。

1.$connect：其作用点为当有客户端第一次连接到你的api时，通常时连接一个Lambda function进行连接的注册（把连接ID保存到DynamoDB），或者对用户身分进行验证。又或者使用log记录此次事件。



{
  "requestContext": {
    "eventType": "CONNECT",   《-----------
    "connectionId": "abc123",
    ...
  }
}

2.$disconnect：在客户端断开连接时触发，通常用于清除用户cache或session，log记录连接的断开。

{
  "requestContext": {
    "eventType": "DISCONNECT",    《---------
    "connectionId": "abc123",
    ...
  }
}

3.$sendMessage:当用户发送一下类型的信息时才触发。其主要用于将信息广播到其他已经连接的用户上，或者用于呼叫其他服务，或用于处理和转发消息

{
  "action": "sendMessage",  《-------
  "message": "Hello!"
}

API Gateway sees `"action": "sendMessage"` → triggers the Lambda linked to route `sendMessage`.



另外，当使用Http API或者REST API的时候，会有endpoint Type的选择，这个选择在web socket API里没有。因为web socket API是默认只在一个AWS区域运行。也没有私密endpoint或边缘优化型的选择。因为WebsocketAPI是一种持续性，长久存活的连接。这种连接不适合与CDN联动，而边缘位置的使用和优化和CDN相关。

而剩下两种API，REST API，Http API都可以在Regional（默认的），Edge-Optimized（使用cloudFront加速全球性的信息传达），private(只有在个人的VPC内的服务才能连接)



关于Cognito:

AWS Cognito 是 **Amazon Web Services（AWS）** 提供的一项托管服务，主要用于为应用程序（如 Web、移动端或物联网设备）管理用户身份验证、授权和用户目录。它的核心功能是简化用户身份管理，同时提供安全的身份验证机制，并与 AWS 其他服务（如 API Gateway、Lambda、DynamoDB 等）无缝集成。

------

### **核心功能**

1. **用户身份验证（Authentication）**
   - 支持多种登录方式：用户名密码、社交账号（如 Google、Facebook、Apple）、企业身份提供商（SAML/OIDC）等。
   - 提供预构建的登录界面（Hosted UI），也可自定义。
   - 支持多因素认证（MFA）和密码策略管理。
2. **用户目录（User Pools）**
   - 存储用户信息（如用户名、密码、属性等），类似一个轻量级用户数据库。
   - 支持用户注册、登录、密码重置、账户验证等功能。
3. **联合身份（Identity Pools）**
   - 允许用户通过临时凭证（如 AWS STS）直接访问其他 AWS 服务（如 S3、DynamoDB）。
   - 支持将不同身份源（如 User Pool、社交账号、企业 IDP）的用户映射到 AWS 角色。
4. **安全与合规**
   - 自动加密用户数据，支持符合 GDPR、HIPAA 等合规要求。
   - 防止恶意攻击（如账号盗用、暴力破解）。
5. **扩展性**
   - 自动扩展以应对高并发请求，无需手动管理基础设施。

------

### **适用场景**

- **移动/Web 应用**：快速为应用添加用户注册、登录功能。
- **多平台应用**：统一管理用户在多个平台（iOS、Android、Web）的身份。
- **企业应用**：集成企业内部的 Active Directory 或 SAML 身份提供商。
- **物联网（IoT）**：管理设备或用户的身份，并控制其对 AWS 资源的访问。

------

### **主要优势**

- **全托管服务**：无需维护服务器或数据库，AWS 负责扩展和安全性。
- **低成本**：按实际用户数和使用量计费，无前期成本。
- **灵活集成**：与 AWS 服务（如 Lambda、API Gateway）深度集成，也支持第三方服务。
- **标准化协议**：支持 OAuth 2.0、OpenID Connect（OIDC）、SAML 等协议。



有关user pool和identity pool的区别：

首先，User Pools主要是用户目录，用于处理用户的注册、登录、管理用户属性，以及身份验证。它类似于一个用户数据库，存储用户的信息，比如用户名、密码、邮箱等，并且支持社交登录和企业身份提供商集成。而Identity Pools的作用是提供临时的AWS凭证，让用户能够访问AWS资源，比如S3或DynamoDB。这可能意味着User Pools处理的是身份验证，而Identity Pools处理的是授权和资源访问。



有关使用场景、功能上的不同，以及此两者如何协同工作：

User Pools作为身份提供者，而Identity Pools则联合不同的身份提供者（包括User Pools、社交登录等）来授予AWS资源的访问权限。比如用户通过User Pools登录后，Identity Pools可以为其生成临时凭证，从而允许该用户访问特定的S3存储桶。



何时单独使用User Pools，何时需要结合Identity Pools：

如果应用只需要管理用户登录，而不需要访问AWS资源，可能只用User Pools就够了。但如果用户需要访问AWS服务，比如上传文件到S3，就需要通过Identity Pools来授权。



两者配置步骤的不同：

User Pools需要设置用户属性、登录方式等，而Identity Pools需要配置身份提供商和IAM角色，以确定不同用户群体能访问的资源。可能还需要提到，User Pools返回的是JWT令牌，而Identity Pools返回的是AWS凭证（访问密钥、秘密密钥、会话令牌）。



有没有可能用户会混淆两者的令牌类型？比如，User Pools的JWT用于应用本身的身份验证，而Identity Pools的凭证用于AWS服务调用。这时候需要明确两者的不同用途。

安全性方面，User Pools负责用户认证，包括MFA、密码策略，而Identity Pools负责控制用户能访问哪些AWS资源，通过IAM角色和策略。所以两者的安全责任不同，一个是认证，一个是授权。Identity Pools可以联合多个身份源，比如同时支持User Pools和Facebook登录，然后将这些不同来源的用户映射到不同的IAM角色，实现细粒度的访问控制。而User Pools本身只管理自己的用户目录。

总结起来，User Pools像是用户的门禁卡，验证身份；Identity Pools像是权限卡，决定能进入哪些房间（AWS资源）。



如果没有User Pools，可以直接用其他身份提供商（如Google）直接连到Identity Pools：

是的，Identity Pools可以独立于User Pools使用，直接联合其他身份提供商。而User Pools则是一个独立管理的用户目录。
