### Serverless应用实例



当你需要建立一个无服务器的应用架构时，有以下点需要注意：

1.因为此应用架构中没有管理服务器的存在（你就当服务器没了），所以，都是某些微服务或程序来处理相关业务。那你的程序需要分散，细小化（指每个微服务只负责一项独立的任务，要避免巨型函数的出现）。

2.因为无服务器，那么业务处理程序并不保留执行前后的状态，也不管理会话，所以，你需要分配资源来记录和对状态的变化做出回应。且你需要额外使用服务来追踪事件的状态。并且需要使用云储存来避免本地储存。

3.需要使用**事件驱动**（这里边的分布式异步交架构）设计，来解耦，所以需要引入消息队列作为缓冲。服务A发送事件到消息队列，服务B从消息队列里取走事件，然后处理事件，再将结果转到下个消息队列，让其他服务处理，直至全流程地完成某一事件的处理流程。需要确保两个服务之间是异步的，解耦的，A的处理操作不会影响到B的处理操作。



例子1：MyToDoList（手机APP）

对于此app有如下要求：

1.使用Https的REST API和后台沟通。2.无服务器架构。3.用户可以直接访问到自己在S3下的文件夹（自己的存储空间） 4.当用户登录或注册时，需要通过serverless服务获得验证。5.用户对自己的存储空间有读写权力，但大部分时间都是读为主。6.使用的数据库需要可以自动扩展，以应对突然的高并发。

架构如下：

![image-20250417131522214](C:\Users\msduser\Desktop\学习笔记\assets\image-20250417131522214.png)

各个部件详解：1.需要使用HTTPS的REST api，所以，选择API gateway来管理API。2.使用Cognito的identity pool来生成临时的用户授权，来让用户可以连接到S3，并通过权限设置来让用户有权使用自己的文件夹。3.API gateway指向Lambda function。4.Lambda function 用于访问后台数据库，因为用户的访问以读为主，则使用DAX作为cache，存储一些常被访问的数据。然后DAX和DynamoDB连接，作为，LAmbda function和DynamoDB之间的中介，以解耦。



例子2：MyBlog.com

对于此app有如下要求：1.做个个人博客网站，这个网站需要在全球范围内都可以被访问到。2.很少写博客，但经常被读。3，绝大部分内容是静态的，剩下部分是动态，通过REST API访问。4.必须使用Cache。5.当有新用户订阅我的博客，需要给它发一个用于欢迎的邮件。

6.任何图片被更新到博客时，需要有log生成。

![image-20250417141725579](C:\Users\msduser\Desktop\学习笔记\assets\image-20250417141725579-1744867047452-1.png)

详解：1.使用cloudFront来让网站可以在全球范围内被访问到。2.对于静态内容，可以使用S3，部署静态html网页，然后使用Origin Access Control来确认访问来自cloudFront，且只有cloudFront可以访问到S3。3.对于网页的动态部分，使用API gateway来管理REST API，然后和后端的Lambda function 连接。4.Lambda function，与DynamoDB的DAX连接，使用DAX为缓存，然后让DAX和DYnamoDB连接。

5.需要发送欢迎Email时，可以使用另一个Lambda function和DynamoDB stream连接，当用户点击订阅时，Lambda检测到数据库DynamoDB的变化，而和SES（simple Email Service）连接，发送邮件（需要IAM 权限）



微服务架构实例：

![image-20250417145102164](C:\Users\msduser\Desktop\学习笔记\assets\image-20250417145102164.png)

对于及时性有较高要求的微服务，可以使用API Gateway加上Load Balancer来实现低延时的通信。

对于稳定性有较高要求的微服务，可以使用SQS。KInesis Stream，SNS等来实现服务与服务之间的流量管理和解耦。以提高系统整体的稳定性。





在高并发场景下快速且准确地追踪事件状态，需要结合分布式系统的特点设计一套高效的追踪机制。以下是一套可落地的方案，涵盖工具选择、设计原则和性能优化策略：

------

### **一、核心挑战与设计目标**

- **高并发压力**：每秒数千至百万级请求，传统日志检索无法实时响应。
- **分布式链路复杂**：事件可能跨多个函数/服务（如API网关→Lambda→SQS→另一个Lambda→数据库）。
- **状态一致性**：确保追踪数据不丢失、不重复，且低延迟可查询。

------

### **二、关键技术与工具链**

#### **1. 分布式追踪系统（核心基础设施）**

- **AWS X-Ray**
  原生集成AWS服务，自动捕获Lambda、API Gateway等组件的请求链路，生成服务地图（Service Map）。
  **优势**：低代码侵入，自动聚合跨服务调用。
  **局限**：深度定制能力较弱，跨云支持有限。

- **OpenTelemetry（OTel）**
  开源标准化方案，支持多语言和多后端（如Jaeger、Zipkin、Datadog）。
  **典型部署**：

  bash

  复制

  ```
  # Lambda函数中集成OTel SDK
  import { trace } from '@opentelemetry/api';
  const tracer = trace.getTracer('order-service');
  
  async function processOrder(event) {
    const span = tracer.startSpan('process_order');
    try {
      // 业务逻辑...
      span.setAttribute('order_id', event.orderId);
    } finally {
      span.end();
    }
  }
  ```

  **数据流**：Lambda→OTel Collector→存储后端（如Jaeger）。

- **第三方SaaS工具**
  Datadog APM、New Relic、Sentry（侧重错误追踪）：提供开箱即用的可视化与分析能力。

#### **2. 唯一事件标识（Trace ID）**

- **生成规则**：在请求入口（如API Gateway）生成全局唯一的`X-Trace-ID`（如UUID v4或Snowflake ID），并透传至所有下游服务。

  python

  复制

  ```
  # API Gateway请求头示例
  headers = {
      'X-Trace-ID': 'a1b2c3d4-5678-90ef-1234-567890abcdef',
      'X-Span-ID': 'root-span'
  }
  ```

- **透传策略**：

  - HTTP调用：通过请求头传递。
  - 消息队列（如SQS/Kafka）：将Trace ID写入消息属性。
  - 数据库操作：在ORM层注入Trace ID作为元数据。

#### **3. 异步日志聚合**

- **架构设计**：

  mermaid

  复制

  ```
  graph LR
    A[Lambda函数] -->|写入结构化日志| B[CloudWatch Logs]
    B --> C[Log Subscription Filter]
    C -->|流式传输| D[Kinesis Firehose]
    D --> E[Elasticsearch/S3]
    E --> F[Kibana/Grafana可视化]
  ```

- **日志规范**：
  使用JSON格式结构化日志，包含必填字段：

  json

  复制

  ```
  {
    "timestamp": "2023-09-20T12:34:56Z",
    "trace_id": "a1b2c3d4...",
    "service": "payment-service",
    "level": "INFO",
    "message": "Payment processed",
    "context": {
      "order_id": "12345",
      "user_id": "67890"
    }
  }
  ```

#### **4. 状态存储引擎**

- **实时查询需求**：

  | 场景                           | 推荐存储                            | 示例                                |
  | :----------------------------- | :---------------------------------- | :---------------------------------- |
  | 高频事件状态跟踪（如订单支付） | Redis（缓存） + DynamoDB（持久化）  | 支付状态先写Redis，异步落盘DynamoDB |
  | 长期历史追踪                   | 时序数据库（InfluxDB、TimescaleDB） | 存储带时间戳的追踪指标              |
  | 全文检索                       | Elasticsearch                       | 根据Trace ID快速检索全链路日志      |

------

### **三、高并发优化策略**

#### **1. 数据分片与分区**

- **Trace ID作为分片键**：
  在存储层（如DynamoDB）按Trace ID哈希分片，避免热点问题。
- **时间分区**：
  在Elasticsearch中按小时/天建立索引（如`logs-2023-09-20`），结合ILM（Index Lifecycle Management）自动滚动删除旧数据。

#### **2. 流式处理替代批量写入**

- **Kinesis Data Streams/Flink实时处理**：
  日志数据通过流处理引擎实时聚合，替代传统的批量ETL。
  **典型场景**：实时统计错误率、延迟百分位数（P99）。

#### **3. 降级与限流**

- **日志采样（Sampling）**：
  在极端流量下，动态调整采样率（如仅记录10%请求的完整日志）。

  yaml

  复制

  ```
  # OpenTelemetry采样配置示例
  sampler: ParentBased(root=TraceIdRatioBased(0.1))
  ```

- **背压（Backpressure）机制**：
  当日志消费者（如Elasticsearch）过载时，Kinesis自动降低拉取速度，避免击穿下游。

------

### **四、实战案例：电商订单系统**

#### **场景描述**

- 每秒处理5000+订单，需追踪订单从创建→支付→库存扣减→物流的完整链路。

#### **实现步骤**

1. **入口层**：
   API Gateway为每个请求生成`X-Trace-ID`，透传给订单服务Lambda。
2. **异步消息**：
   支付成功后，通过SQS通知库存服务，消息携带Trace ID。
3. **数据库操作**：
   在DynamoDB订单表中增加`trace_id`字段，便于事后关联。
4. **可视化**：
   通过X-Ray服务地图发现瓶颈（如库存服务延迟过高）。

#### **性能数据**

- 追踪数据延迟：从事件发生到可查询 < 2秒（通过Kinesis+Elasticsearch实现）。
- 存储成本降低：采样率动态调整后，日志存储量减少60%。

------

### **五、常见陷阱与解决方案**

| 问题               | 根因                       | 解决方案                                    |
| :----------------- | :------------------------- | :------------------------------------------ |
| 日志丢失           | 突发流量击穿日志收集管道   | 使用Kinesis缓冲 + 消费者自动扩缩容          |
| 跨服务Trace ID断裂 | 未正确透传HTTP头或消息属性 | 在中间件层统一处理透传逻辑                  |
| 存储成本失控       | 全量日志无期限保存         | 配置生命周期策略（如S3转归档存储/自动删除） |

------

### **六、工具推荐**

- **开源方案**：OpenTelemetry + Jaeger + Prometheus/Grafana（成本低，需自运维）。
- **云原生方案**：AWS X-Ray + CloudWatch Logs Insights + QuickSight（集成度高，按需付费）。
- **企业级方案**：Datadog APM（全托管，支持多云，起价$15/主机/月）。

------

通过以上方案，可在保证低延迟的前提下，实现每秒数万级事件的精准追踪。**关键成功要素**：

1. **标准化**：所有服务强制透传Trace ID。
2. **异步化**：日志收集与业务逻辑解耦。
3. **分层存储**：热数据（Redis）+ 温数据（ES）+ 冷数据（S3 Glacier）。

