### 有关AWS监视有关的服务



Amazon CloudWatch 是 AWS 提供的监控和管理服务，用于实时跟踪资源使用情况、应用性能和操作日志。以下是其核心细节：

------

### **一、核心功能**

1. **指标（Metrics）**
   - **预定义指标**：自动收集 AWS 服务（如 EC2、S3、RDS）的性能数据（CPU 使用率、请求延迟等）。
   - **自定义指标**：通过 API 或 CloudWatch Agent 上传自定义指标（如应用内部状态）。
   - **存储与保留**：指标按 1 分钟或更高分辨率存储，默认保留 15 个月（精度随时间降低）。
2. **日志（Logs）**
   - **日志组/流**：日志按组（如应用名称）和流（如实例 ID）组织。
   - **日志收集**：支持从 EC2、Lambda、容器等来源收集日志。
   - **日志分析**：使用 **CloudWatch Logs Insights** 进行快速查询（如过滤错误日志）。
3. **警报（Alarms）**
   - 基于指标阈值（静态或动态异常检测）触发通知（SNS、Auto Scaling 操作等）。
   - 支持复合报警（多个条件组合判断）。
4. **仪表盘（Dashboards）**
   - 自定义可视化看板，实时展示关键指标和日志数据。
5. **事件与自动化（EventBridge 集成）**
   - 响应资源状态变化（如 EC2 实例终止），触发 Lambda、SNS 等操作。

------

### **二、关键组件**

- **CloudWatch Agent**：安装在 EC2/本地服务器，收集系统级指标（内存/磁盘）和日志。
- **CloudWatch Synthetics**：通过 Canary（定时脚本）监控 API 端点或网站可用性。
- **Contributor Insights**：分析日志数据中的高频模式（如高负载的微服务）。

------

### **三、典型使用场景**

- **应用监控**：跟踪 API 延迟、错误率，设置自动扩容。
- **故障排查**：通过日志分析定位服务异常原因。
- **资源优化**：识别低效实例（如 CPU 长期低使用率）。
- **安全审计**：监控登录尝试或异常 API 调用。

------

### **四、定价模型**

- **免费层**：每月 10 个自定义指标、5GB 日志摄入、100 万次 API 调用。
- **收费项**：
  - 自定义指标数量和高分辨率指标（1 分钟 vs 5 分钟）。
  - 日志存储量（按 GB/月）和 Logs Insights 查询次数。
  - 报警数量（每个报警每月约 0.10 美元）。

------

### **五、最佳实践**

1. **指标优化**：
   - 使用标准分辨率（5 分钟）降低成本，除非需要精细化监控。
   - 合并相关指标（如使用维度标签区分同一应用的不同模块）。
2. **日志管理**：
   - 设置日志保留策略（如开发环境保留 7 天，生产保留 1 年）。
   - 使用过滤器提取关键日志事件（如错误码 ERROR-500）。
3. **报警设计**：
   - 避免冗余报警（如同时监控 CPU 和负载均衡器错误）。
   - 使用动态阈值适应流量波动（如促销期间流量激增）。
4. **安全与权限**：
   - 通过 IAM 限制用户访问特定日志组或指标。
   - 加密敏感日志数据（使用 KMS 密钥）。

------

### **六、集成与扩展**

- **AWS 服务**：与 Auto Scaling（动态扩容）、Lambda（事件处理）、SNS（通知）无缝协作。
- **第三方工具**：通过 CloudWatch API 集成 Prometheus、Grafana 等工具。
- **跨账户监控**：集中监控多个 AWS 账户的资源（需配置跨账户 IAM 角色）。







Amazon CloudWatch 的指标既包含 **固定的预定义指标**，也支持 **完全自定义的指标**，具体取决于监控对象和需求。以下是详细说明：

------

### **一、固定指标（预定义指标）**

#### **1. 来源**

- **AWS 服务自动生成**：
  当使用 AWS 服务（如 EC2、S3、Lambda、RDS 等）时，CloudWatch 会自动收集并存储其核心性能指标，无需手动配置。
  - 例如：
    - EC2 的 `CPUUtilization`（CPU 使用率）、`NetworkIn`（入站流量）。
    - Lambda 的 `Invocations`（调用次数）、`Duration`（执行时间）。
    - S3 的 `BucketSizeBytes`（存储桶大小）。

#### **2. 特点**

- **无需配置**：开箱即用，AWS 服务默认推送。
- **标准化**：指标名称、维度、单位由 AWS 统一管理。
- **覆盖范围有限**：仅包含 AWS 服务的基础性能数据，无法监控应用内部状态或自定义业务逻辑。

------

### **二、自定义指标**

#### **1. 适用场景**

- **监控非 AWS 资源**：本地服务器、容器、第三方服务。
- **业务指标**：用户注册量、订单成功率、API 响应时间等。
- **细粒度监控**：应用内部状态（如缓存命中率、队列积压任务数）。

#### **2. 实现方式**

- **方法 1：通过 CloudWatch Agent 上报**

  - 适用于系统级指标（如内存、磁盘、进程）。
  - 安装 Agent 后，配置 `config.json` 文件定义收集的指标。

  json

  ```
  {
    "metrics": {
      "metrics_collected": {
        "cpu": {"measurement": ["cpu_usage_idle"]},
        "mem": {"measurement": ["mem_used_percent"]}
      }
    }
  }
  ```

- **方法 2：通过 API/SDK 手动上报**

  - 使用 `PutMetricData` API 或 AWS SDK（Python、Java 等）直接推送自定义数据。

  python

  ```
  # Python 示例：上报支付成功率
  import boto3
  client = boto3.client('cloudwatch')
  client.put_metric_data(
      Namespace='MyEcommerceApp',
      MetricData=[{
          'MetricName': 'PaymentSuccessRate',
          'Dimensions': [{'Name': 'Gateway', 'Value': 'PayPal'}],
          'Value': 99.5,
          'Unit': 'Percent'
      }]
  )
  ```

- **方法 3：集成第三方工具**

  - 通过 Telegraf、Prometheus + CloudWatch Exporter 等工具将外部监控数据导入 CloudWatch。

#### **3. 自定义指标的核心能力**

- **命名空间（Namespace）**：自定义分类（如 `MyApp/Business`）。
- **维度（Dimensions）**：通过键值对细分指标来源（如 `Environment=Prod`、`Service=Checkout`）。
- **高分辨率**：支持每秒（1s）或每分钟（1m）粒度的数据上报（需注意成本）。
- **数学表达式**：在仪表盘中动态计算复合指标（如 `ErrorRate = (Errors / Requests) * 100`）。

------

### **三、灵活性与限制**

#### **1. 灵活性**

- **完全自定义指标内容**：可监控任意业务逻辑或非 AWS 资源。
- **动态维度**：通过维度组合实现多维度分析（如按地区、用户类型细分）。
- **实时性**：支持高分辨率指标（1 秒级），适合实时告警。

#### **2. 限制与注意事项**

- **成本**：自定义指标按数量和数据点收费，高频上报可能导致费用增加。
- **高基数维度问题**：避免将唯一值（如用户 ID）作为维度，否则可能产生海量指标，推高成本（[详见 AWS 文档](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_limits.html)）。
- **数据保留**：高分辨率（1 秒级）数据仅保留 3 小时，标准分辨率（1 分钟/5 分钟）保留 15 个月。

------

### **四、实际案例**

#### **案例 1：监控电商业务指标**

- **自定义指标**：
  - `ShoppingCartAbandonmentRate`（购物车放弃率）。
  - `CheckoutLatency`（结算页面延迟）。
- **维度**：
  - `DeviceType=Mobile`（区分设备类型）。
  - `Region=EU`（按地区分析）。

#### **案例 2：混合云监控**

- 使用 CloudWatch Agent 监控本地数据中心的服务器：
  - 上报 `DiskUsage`（磁盘使用率）、`ServiceUptime`（服务可用时间）。

#### **案例 3：微服务链路追踪**

- 为每个微服务上报错误率和延迟：
  - 维度 `ServiceName=OrderService`、`Version=v2`。

------

### **五、最佳实践**

1. **合理设计命名空间和维度**
   - 命名空间按功能划分（如 `Finance`, `Infrastructure`）。
   - 维度用于过滤和分组，避免过度细分（如 `Env=Prod` 而非 `Host=Instance-01`）。
2. **控制成本**
   - 合并多个指标值到一个 `PutMetricData` 调用（减少 API 请求次数）。
   - 低频上报非关键指标（如 5 分钟粒度）。
3. **报警与自动化**
   - 为关键业务指标设置动态阈值报警（如订单量骤降 20%）。
   - 触发 Lambda 函数自动扩容或发送 Slack 通知。



##### 关于cloudwatch在其他服务内设置检测和记录的注意事项：

如果该服务可以启用cloudwatch服务，请在console启动，具体要检测什么，可以在console中选择，或者在cloudwatch console内创建。如果cloudwatch无法获取对应服务的运行信息，请优先检查cloudwatch是否有被给予合适的IAM role或权限，如果不是接入权限的问题，可以尝试在console或cloudwatch-agent里的运行log里查找问题。EC2 instance可以手动安装amazon-cloudwatch-agent，但比较麻烦，除了cloudwatch-agent，还需要一个ssm-agent。且需要手动创建cloudwatch的log group来存放对应记录，并需要手动在IAM中创建role，然后给予权限。





##### 关于cloudwatch console的讲解：



## **「添加数学表达式 (Add Math)」与「添加查询 (Add Query)」的区别**

### 🔹 添加数学表达式（Add Math）

- 用来对一个或多个指标进行 **简单计算或转换**。
- 常见写法：
  - `m1 + m2`：两个指标相加
  - `(m1 / m2) * 100`：计算百分比
  - `IF(m1 > 90, 1, 0)`：条件判断，类似开关

🔧 举例：

- 计算磁盘使用率百分比：`(used / total) * 100`
- 判断是否高 CPU 占用：`IF(CPUUtilization > 80, 1, 0)`

> `m1`, `m2` 是 CloudWatch 自动给每个指标分配的 ID，你可以自定义名称。

------

### 🔹 添加查询（Add Query）

- 可以写更复杂的 **Metric Math 表达式**，并添加更高级的 **筛选条件**。
- 一般用于跨多个维度、资源的指标筛选。

🧠 结论：

- **Add Math**：够用、简单、常用
- **Add Query**：复杂查询或高级用法时使用

------

## ✅ 2. **指标选择时的命名含义**

你可能看到下面这些内容：

```
bash


CopyEdit
i-0234ab567... | t2.micro | MyAppServer | /dev/xvda1
```

这是 AWS 在 CloudWatch 中的 **维度信息（Dimensions）**，帮助你识别每个指标属于哪个资源。

| 字段             | 含义                                 |
| ---------------- | ------------------------------------ |
| **InstanceId**   | 实例的 ID，比如 `i-0123456789abcdef` |
| **InstanceType** | 实例类型，如 `t2.micro`、`m5.large`  |
| **Name**         | 你在 EC2 中为实例设置的 Name 标签    |
| **Device**       | 磁盘设备名（如 `/dev/xvda1`）        |
| **Filesystem**   | 文件系统（如 `/`、`/var`）           |
| **MountPath**    | 挂载路径，如 `/mnt/data`             |
| **loopX**        | 虚拟设备（解释见下）                 |



------

## ✅ 3. **Linux 磁盘设备名含义（如 `xvda1`, `loop0` 等）**

这些名字来自 Linux 的设备命名，主要见于通过 CloudWatch Agent 上传的系统指标：

### 🔹 `xvda1`, `xvda16`, `nvme0n1` 等

这些是实际的磁盘分区或存储设备：

| 名称      | 含义                         |
| --------- | ---------------------------- |
| `xvda1`   | 实例主磁盘（通常挂载到 `/`） |
| `xvda16`  | 通常是启动分区或保留分区     |
| `nvme0n1` | 在新型 EC2 上常见的 NVMe SSD |



常用于以下指标中：

- `disk_used_percent`
- `diskio_write_bytes`

> 一般我们主要关注 `xvda1`、`nvme0n1` 这样的主磁盘。

------

### 🔹 `loop0`, `loop1` 等

这些是 **Loop 设备**，是虚拟磁盘，不是实际的物理存储设备。

用途包括：

- Ubuntu 中使用 Snap 安装软件时会出现
- 挂载 `.iso`、`.img` 文件
- 某些容器系统的 Overlay 文件系统

> 除非你在排查特殊问题，否则这些可以 **忽略**。

------

### 🧭 如何选择要监控的设备？

通常建议监控如下：

| 挂载点              | 原因                   |
| ------------------- | ---------------------- |
| `/`                 | 主系统分区             |
| `/mnt/data`         | 应用或数据库专用挂载点 |
| `xvda1` / `nvme0n1` | 主设备名（物理磁盘）   |



------

## ✅ 4. **Metric Name 和 Name（标签）容易混淆**

| 字段            | 含义                                                    |
| --------------- | ------------------------------------------------------- |
| **Metric Name** | 度量项的名称，如 `CPUUtilization`、`mem_used_percent`   |
| **Name**        | EC2 实例的 “Name 标签”，是你自己在 EC2 控制台设置的名称 |



#### Interface里的enX0 或 ensX、enoX 是什么意思？

这些是基于 systemd/udev 规则的可预测网络接口名称（用于现代 Linux 发行版）：

前缀                              含义
ensX 以太网设备，连接到 PCI Express 插槽
enoX 板载以太网设备
enpXsY 以太网 PCI 总线位置（例如 enp3s0）
eth0 传统命名（较旧的 Linux 系统）

它们取代了较旧的不可预测的 eth0、eth1 等，以使网络接口在重启后更加一致。



------

## ✅ 总结



| 概念               | 说明                           |
| ------------------ | ------------------------------ |
| `Add Math`         | 添加数学计算，如百分比、加减等 |
| `Add Query`        | 添加复杂表达式和筛选条件       |
| `InstanceId`       | 实例唯一 ID                    |
| `xvda1`, `nvme0n1` | 真正的磁盘设备                 |
| `loopX`            | 虚拟设备（一般忽略）           |
| `Metric Name`      | CloudWatch 指标项的真实名称    |
| `Name`             | EC2 实例的人类可读名称（标签） |







Amazon EventBridge 是 AWS 的事件总线服务，其核心价值在于 **统一事件路由** 和 **解耦事件生产与消费**。它并非简单替代其他服务，而是通过**事件驱动架构范式**解决传统集成模式的痛点。以下是深度解析：

------

### **一、EventBridge 的本质：事件中枢**

#### **1. 核心功能**

- **事件总线（Event Bus）**：
  集中接收来自 **AWS 服务**（如 CloudWatch、S3）、**SaaS 应用**（如 Datadog、Zendesk）和**自定义应用**的事件。
- **规则（Rules）**：
  基于事件内容（JSON 路径匹配）动态路由到 **100+ 目标**（Lambda、SQS、Step Functions 等）。
- **Schema Registry**：
  自动发现和存储事件结构，确保数据一致性。

#### **2. 关键特性**

- **无服务器原生**：无需管理基础设施，按事件量计费。
- **毫秒级延迟**：事件从接收到触达目标通常在 500ms 内。
- **事件转换**：实时修改事件格式适配不同目标。

------

### **二、为什么必须用 EventBridge？—— 对比传统方案**

假设需要处理 **CloudWatch 告警事件**，传统方案与 EventBridge 的对比如下：

| **能力**           | **传统方案（SNS + Lambda + SQS）**       | **EventBridge 方案**                          | **EventBridge 优势**             |
| :----------------- | :--------------------------------------- | :-------------------------------------------- | :------------------------------- |
| **事件路由**       | 手动配置 SNS 主题订阅，硬编码目标        | 声明式规则匹配，动态路由                      | 解耦生产者和消费者               |
| **多目标分发**     | 需为每个目标创建订阅，扩展时修改 SNS     | 单条规则可同时触发 5 个目标（可扩展）         | 无需修改事件源即可新增消费者     |
| **复杂事件过滤**   | 需在 Lambda 中写过滤逻辑，增加成本和延迟 | 规则级过滤（支持 JSON 路径匹配）              | 减少无效事件传递，降低下游负载   |
| **SaaS 集成**      | 需自建 API 网关或中间层对接第三方服务    | 原生支持 20+ SaaS 合作伙伴（如 PagerDuty）    | 开箱即用，避免维护自定义集成代码 |
| **事件存档与重放** | 需手动实现（如保存到 S3）                | 内置事件存档和重放功能                        | 快速故障复盘或测试新规则         |
| **跨账户事件**     | 需配置复杂的 SNS 跨账户权限              | 简单配置事件总线策略（Resource-Based Policy） | 简化多账户架构管理               |

------

### **三、EventBridge 的不可替代性**

#### **场景 1：动态响应 CloudWatch 告警**

- **需求**：

  - 当 `CPU 告警` 时触发扩容，同时 `磁盘告警` 时触发清理脚本，并通知 Slack。

- **传统方案**：

  ![deepseek_mermaid_20250530_51b0a2](C:\Users\msduser\Desktop\学习笔记\assets\deepseek_mermaid_20250530_51b0a2.png)

  <svg role="graphics-document document" viewBox="0 0 618.953125 278" class="flowchart mermaid-svg" xmlns="http://www.w3.org/2000/svg" width="100%" id="mermaid-svg-9" style="max-width: 618.953px; transform-origin: 0px 0px; user-select: none; transform: translate(47.1247px, 0px) scale(1.01838);"><g><marker orient="auto" markerHeight="8" markerWidth="8" markerUnits="userSpaceOnUse" refY="5" refX="5" viewBox="0 0 10 10" class="marker flowchart-v2" id="mermaid-svg-9_flowchart-v2-pointEnd"><path style="stroke-width: 1; stroke-dasharray: 1, 0;" class="arrowMarkerPath" d="M 0 0 L 10 5 L 0 10 z"></path></marker><marker orient="auto" markerHeight="8" markerWidth="8" markerUnits="userSpaceOnUse" refY="5" refX="4.5" viewBox="0 0 10 10" class="marker flowchart-v2" id="mermaid-svg-9_flowchart-v2-pointStart"><path style="stroke-width: 1; stroke-dasharray: 1, 0;" class="arrowMarkerPath" d="M 0 5 L 10 10 L 10 0 z"></path></marker><marker orient="auto" markerHeight="11" markerWidth="11" markerUnits="userSpaceOnUse" refY="5" refX="11" viewBox="0 0 10 10" class="marker flowchart-v2" id="mermaid-svg-9_flowchart-v2-circleEnd"><circle style="stroke-width: 1; stroke-dasharray: 1, 0;" class="arrowMarkerPath" r="5" cy="5" cx="5"></circle></marker><marker orient="auto" markerHeight="11" markerWidth="11" markerUnits="userSpaceOnUse" refY="5" refX="-1" viewBox="0 0 10 10" class="marker flowchart-v2" id="mermaid-svg-9_flowchart-v2-circleStart"><circle style="stroke-width: 1; stroke-dasharray: 1, 0;" class="arrowMarkerPath" r="5" cy="5" cx="5"></circle></marker><marker orient="auto" markerHeight="11" markerWidth="11" markerUnits="userSpaceOnUse" refY="5.2" refX="12" viewBox="0 0 11 11" class="marker cross flowchart-v2" id="mermaid-svg-9_flowchart-v2-crossEnd"><path style="stroke-width: 2; stroke-dasharray: 1, 0;" class="arrowMarkerPath" d="M 1,1 l 9,9 M 10,1 l -9,9"></path></marker><marker orient="auto" markerHeight="11" markerWidth="11" markerUnits="userSpaceOnUse" refY="5.2" refX="-1" viewBox="0 0 11 11" class="marker cross flowchart-v2" id="mermaid-svg-9_flowchart-v2-crossStart"><path style="stroke-width: 2; stroke-dasharray: 1, 0;" class="arrowMarkerPath" d="M 1,1 l 9,9 M 10,1 l -9,9"></path></marker><g class="root"><g class="clusters"></g><g class="edgePaths"><path marker-end="url(#mermaid-svg-9_flowchart-v2-pointEnd)" style="" class="edge-thickness-normal edge-pattern-solid edge-thickness-normal edge-pattern-solid flowchart-link" id="L_A_B_0" d="M198.688,139L202.854,139C207.021,139,215.354,139,223.021,139C230.688,139,237.688,139,241.188,139L244.688,139"></path><path marker-end="url(#mermaid-svg-9_flowchart-v2-pointEnd)" style="" class="edge-thickness-normal edge-pattern-solid edge-thickness-normal edge-pattern-solid flowchart-link" id="L_B_C1_0" d="M335.429,112L346.376,99.167C357.323,86.333,379.216,60.667,396.329,47.833C413.443,35,425.776,35,431.943,35L438.109,35"></path><path marker-end="url(#mermaid-svg-9_flowchart-v2-pointEnd)" style="" class="edge-thickness-normal edge-pattern-solid edge-thickness-normal edge-pattern-solid flowchart-link" id="L_B_C2_0" d="M376.109,139L380.276,139C384.443,139,392.776,139,400.443,139C408.109,139,415.109,139,418.609,139L422.109,139"></path><path marker-end="url(#mermaid-svg-9_flowchart-v2-pointEnd)" style="" class="edge-thickness-normal edge-pattern-solid edge-thickness-normal edge-pattern-solid flowchart-link" id="L_B_C3_0" d="M335.429,166L346.376,178.833C357.323,191.667,379.216,217.333,396.728,230.167C414.24,243,427.37,243,433.935,243L440.5,243"></path></g><g class="edgeLabels"><g class="edgeLabel"><g transform="translate(0, 0)" class="label"><foreignObject height="0" width="0"><div class="labelBkg" xmlns="http://www.w3.org/1999/xhtml" style="background-color: rgba(88, 88, 88, 0.5); display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="edgeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204); background-color: rgb(88, 88, 88); text-align: center;"></span></div></foreignObject></g></g><g class="edgeLabel"><g transform="translate(0, 0)" class="label"><foreignObject height="0" width="0"><div class="labelBkg" xmlns="http://www.w3.org/1999/xhtml" style="background-color: rgba(88, 88, 88, 0.5); display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="edgeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204); background-color: rgb(88, 88, 88); text-align: center;"></span></div></foreignObject></g></g><g class="edgeLabel"><g transform="translate(0, 0)" class="label"><foreignObject height="0" width="0"><div class="labelBkg" xmlns="http://www.w3.org/1999/xhtml" style="background-color: rgba(88, 88, 88, 0.5); display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="edgeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204); background-color: rgb(88, 88, 88); text-align: center;"></span></div></foreignObject></g></g><g class="edgeLabel"><g transform="translate(0, 0)" class="label"><foreignObject height="0" width="0"><div class="labelBkg" xmlns="http://www.w3.org/1999/xhtml" style="background-color: rgba(88, 88, 88, 0.5); display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="edgeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204); background-color: rgb(88, 88, 88); text-align: center;"></span></div></foreignObject></g></g></g><g class="nodes"><g transform="translate(103.34375, 139)" id="flowchart-A-0" class="node default"><rect height="54" width="190.6875" y="-27" x="-95.34375" style="" class="basic label-container"></rect><g transform="translate(-65.34375, -12)" style="" class="label"><rect></rect><foreignObject height="24" width="130.6875"><div xmlns="http://www.w3.org/1999/xhtml" style="display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="nodeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204);"><p style="margin: 0px;">CloudWatch Alarm</p></span></div></foreignObject></g></g><g transform="translate(312.3984375, 139)" id="flowchart-B-1" class="node default"><rect height="54" width="127.421875" y="-27" x="-63.7109375" style="" class="basic label-container"></rect><g transform="translate(-33.7109375, -12)" style="" class="label"><rect></rect><foreignObject height="24" width="67.421875"><div xmlns="http://www.w3.org/1999/xhtml" style="display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="nodeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204);"><p style="margin: 0px;">SNS Topic</p></span></div></foreignObject></g></g><g transform="translate(518.53125, 35)" id="flowchart-C1-3" class="node default"><rect height="54" width="152.84375" y="-27" x="-76.421875" style="" class="basic label-container"></rect><g transform="translate(-46.421875, -12)" style="" class="label"><rect></rect><foreignObject height="24" width="92.84375"><div xmlns="http://www.w3.org/1999/xhtml" style="display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="nodeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204);"><p style="margin: 0px;">Lambda 扩容</p></span></div></foreignObject></g></g><g transform="translate(518.53125, 139)" id="flowchart-C2-5" class="node default"><rect height="54" width="184.84375" y="-27" x="-92.421875" style="" class="basic label-container"></rect><g transform="translate(-62.421875, -12)" style="" class="label"><rect></rect><foreignObject height="24" width="124.84375"><div xmlns="http://www.w3.org/1999/xhtml" style="display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="nodeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204);"><p style="margin: 0px;">Lambda 清理磁盘</p></span></div></foreignObject></g></g><g transform="translate(518.53125, 243)" id="flowchart-C3-7" class="node default"><rect height="54" width="148.0625" y="-27" x="-74.03125" style="" class="basic label-container"></rect><g transform="translate(-44.03125, -12)" style="" class="label"><rect></rect><foreignObject height="24" width="88.0625"><div xmlns="http://www.w3.org/1999/xhtml" style="display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="nodeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204);"><p style="margin: 0px;">SNS 到 Slack</p></span></div></foreignObject></g></g></g></g></g></svg>

  - **问题**：新增告警类型需修改 SNS 订阅逻辑；所有告警都会触发所有目标（需 Lambda 内写过滤）。

- **EventBridge 方案**：

  ![deepseek_mermaid_20250530_4b7452](C:\Users\msduser\Desktop\学习笔记\assets\deepseek_mermaid_20250530_4b7452.png)

  

  <svg role="graphics-document document" viewBox="0 0 727.34375 278" class="flowchart mermaid-svg" xmlns="http://www.w3.org/2000/svg" width="100%" id="mermaid-svg-27" style="max-width: 727.344px; transform-origin: 0px 0px; user-select: none; transform: translate(0px, 2.55441px) scale(1);"><g><marker orient="auto" markerHeight="8" markerWidth="8" markerUnits="userSpaceOnUse" refY="5" refX="5" viewBox="0 0 10 10" class="marker flowchart-v2" id="mermaid-svg-27_flowchart-v2-pointEnd"><path style="stroke-width: 1; stroke-dasharray: 1, 0;" class="arrowMarkerPath" d="M 0 0 L 10 5 L 0 10 z"></path></marker><marker orient="auto" markerHeight="8" markerWidth="8" markerUnits="userSpaceOnUse" refY="5" refX="4.5" viewBox="0 0 10 10" class="marker flowchart-v2" id="mermaid-svg-27_flowchart-v2-pointStart"><path style="stroke-width: 1; stroke-dasharray: 1, 0;" class="arrowMarkerPath" d="M 0 5 L 10 10 L 10 0 z"></path></marker><marker orient="auto" markerHeight="11" markerWidth="11" markerUnits="userSpaceOnUse" refY="5" refX="11" viewBox="0 0 10 10" class="marker flowchart-v2" id="mermaid-svg-27_flowchart-v2-circleEnd"><circle style="stroke-width: 1; stroke-dasharray: 1, 0;" class="arrowMarkerPath" r="5" cy="5" cx="5"></circle></marker><marker orient="auto" markerHeight="11" markerWidth="11" markerUnits="userSpaceOnUse" refY="5" refX="-1" viewBox="0 0 10 10" class="marker flowchart-v2" id="mermaid-svg-27_flowchart-v2-circleStart"><circle style="stroke-width: 1; stroke-dasharray: 1, 0;" class="arrowMarkerPath" r="5" cy="5" cx="5"></circle></marker><marker orient="auto" markerHeight="11" markerWidth="11" markerUnits="userSpaceOnUse" refY="5.2" refX="12" viewBox="0 0 11 11" class="marker cross flowchart-v2" id="mermaid-svg-27_flowchart-v2-crossEnd"><path style="stroke-width: 2; stroke-dasharray: 1, 0;" class="arrowMarkerPath" d="M 1,1 l 9,9 M 10,1 l -9,9"></path></marker><marker orient="auto" markerHeight="11" markerWidth="11" markerUnits="userSpaceOnUse" refY="5.2" refX="-1" viewBox="0 0 11 11" class="marker cross flowchart-v2" id="mermaid-svg-27_flowchart-v2-crossStart"><path style="stroke-width: 2; stroke-dasharray: 1, 0;" class="arrowMarkerPath" d="M 1,1 l 9,9 M 10,1 l -9,9"></path></marker><g class="root"><g class="clusters"></g><g class="edgePaths"><path marker-end="url(#mermaid-svg-27_flowchart-v2-pointEnd)" style="" class="edge-thickness-normal edge-pattern-solid edge-thickness-normal edge-pattern-solid flowchart-link" id="L_A_B_0" d="M198.688,139L202.854,139C207.021,139,215.354,139,223.021,139C230.688,139,237.688,139,241.188,139L244.688,139"></path><path marker-end="url(#mermaid-svg-27_flowchart-v2-pointEnd)" style="" class="edge-thickness-normal edge-pattern-solid edge-thickness-normal edge-pattern-solid flowchart-link" id="L_B_C1_0" d="M362.809,112L382.418,99.167C402.026,86.333,441.244,60.667,474.525,47.833C507.807,35,535.154,35,548.827,35L562.5,35"></path><path marker-end="url(#mermaid-svg-27_flowchart-v2-pointEnd)" style="" class="edge-thickness-normal edge-pattern-solid edge-thickness-normal edge-pattern-solid flowchart-link" id="L_B_C2_0" d="M394.422,139L408.762,139C423.102,139,451.781,139,479.794,139C507.807,139,535.154,139,548.827,139L562.5,139"></path><path marker-end="url(#mermaid-svg-27_flowchart-v2-pointEnd)" style="" class="edge-thickness-normal edge-pattern-solid edge-thickness-normal edge-pattern-solid flowchart-link" id="L_B_C3_0" d="M362.809,166L382.418,178.833C402.026,191.667,441.244,217.333,479.195,230.167C517.146,243,553.831,243,572.173,243L590.516,243"></path></g><g class="edgeLabels"><g class="edgeLabel"><g transform="translate(0, 0)" class="label"><foreignObject height="0" width="0"><div class="labelBkg" xmlns="http://www.w3.org/1999/xhtml" style="background-color: rgba(88, 88, 88, 0.5); display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="edgeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204); background-color: rgb(88, 88, 88); text-align: center;"></span></div></foreignObject></g></g><g transform="translate(480.4609375, 35)" class="edgeLabel"><g transform="translate(-61.0390625, -12)" class="label"><foreignObject height="24" width="122.078125"><div class="labelBkg" xmlns="http://www.w3.org/1999/xhtml" style="background-color: rgba(88, 88, 88, 0.5); display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="edgeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204); background-color: rgb(88, 88, 88); text-align: center;"><p style="margin: 0px; background-color: rgb(88, 88, 88);">规则1：CPU 告警</p></span></div></foreignObject></g></g><g transform="translate(480.4609375, 139)" class="edgeLabel"><g transform="translate(-60.1953125, -12)" class="label"><foreignObject height="24" width="120.390625"><div class="labelBkg" xmlns="http://www.w3.org/1999/xhtml" style="background-color: rgba(88, 88, 88, 0.5); display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="edgeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204); background-color: rgb(88, 88, 88); text-align: center;"><p style="margin: 0px; background-color: rgb(88, 88, 88);">规则2：磁盘告警</p></span></div></foreignObject></g></g><g transform="translate(480.4609375, 243)" class="edgeLabel"><g transform="translate(-60.1953125, -12)" class="label"><foreignObject height="24" width="120.390625"><div class="labelBkg" xmlns="http://www.w3.org/1999/xhtml" style="background-color: rgba(88, 88, 88, 0.5); display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="edgeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204); background-color: rgb(88, 88, 88); text-align: center;"><p style="margin: 0px; background-color: rgb(88, 88, 88);">规则3：所有告警</p></span></div></foreignObject></g></g></g><g class="nodes"><g transform="translate(103.34375, 139)" id="flowchart-A-0" class="node default"><rect height="54" width="190.6875" y="-27" x="-95.34375" style="" class="basic label-container"></rect><g transform="translate(-65.34375, -12)" style="" class="label"><rect></rect><foreignObject height="24" width="130.6875"><div xmlns="http://www.w3.org/1999/xhtml" style="display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="nodeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204);"><p style="margin: 0px;">CloudWatch Alarm</p></span></div></foreignObject></g></g><g transform="translate(321.5546875, 139)" id="flowchart-B-1" class="node default"><rect height="54" width="145.734375" y="-27" x="-72.8671875" style="" class="basic label-container"></rect><g transform="translate(-42.8671875, -12)" style="" class="label"><rect></rect><foreignObject height="24" width="85.734375"><div xmlns="http://www.w3.org/1999/xhtml" style="display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="nodeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204);"><p style="margin: 0px;">EventBridge</p></span></div></foreignObject></g></g><g transform="translate(642.921875, 35)" id="flowchart-C1-3" class="node default"><rect height="54" width="152.84375" y="-27" x="-76.421875" style="" class="basic label-container"></rect><g transform="translate(-46.421875, -12)" style="" class="label"><rect></rect><foreignObject height="24" width="92.84375"><div xmlns="http://www.w3.org/1999/xhtml" style="display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="nodeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204);"><p style="margin: 0px;">Lambda 扩容</p></span></div></foreignObject></g></g><g transform="translate(642.921875, 139)" id="flowchart-C2-5" class="node default"><rect height="54" width="152.84375" y="-27" x="-76.421875" style="" class="basic label-container"></rect><g transform="translate(-46.421875, -12)" style="" class="label"><rect></rect><foreignObject height="24" width="92.84375"><div xmlns="http://www.w3.org/1999/xhtml" style="display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="nodeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204);"><p style="margin: 0px;">Lambda 清理</p></span></div></foreignObject></g></g><g transform="translate(642.921875, 243)" id="flowchart-C3-7" class="node default"><rect height="54" width="96.8125" y="-27" x="-48.40625" style="" class="basic label-container"></rect><g transform="translate(-18.40625, -12)" style="" class="label"><rect></rect><foreignObject height="24" width="36.8125"><div xmlns="http://www.w3.org/1999/xhtml" style="display: table-cell; white-space: nowrap; line-height: 1.5; max-width: 200px; text-align: center;"><span class="nodeLabel" style="fill: rgb(204, 204, 204); color: rgb(204, 204, 204);"><p style="margin: 0px;">Slack</p></span></div></foreignObject></g></g></g></g></g></svg>

  - **优势**：规则隔离，新增告警类型只需添加新规则，不影响现有逻辑。

#### **场景 2：统一混合云事件总线**

- **需求**：
  整合 AWS 事件、本地服务器事件和 Salesforce 业务事件。
- **传统方案**：
  需部署 Kafka 集群或 API 网关作为中枢，维护成本高。
- **EventBridge 方案**：
  - AWS 事件 → 默认总线
  - 本地事件 → 通过 EventBridge API 推送
  - Salesforce → 通过 EventBridge 合作伙伴集成
  - **优势**：全托管服务，免运维，按事件量付费。

------

### **四、何时不需要 EventBridge？**

以下情况可能选择其他方案：

1. **简单点对点集成**：
   单一事件触发单一目标（如 S3 上传触发 Lambda），直接使用原生集成更简单。
2. **极端成本敏感**：
   EventBridge 每百万事件 $1.0，若事件量极大且无需路由，SNS（每百万 $0.50）或 SQS 更便宜。
3. **已有消息中间件**：
   若已部署 Kafka/RabbitMQ 且团队熟悉，迁移可能不必要。

------

### **五、最佳实践：EventBridge 高级模式**

#### **1. 事件规范化（Canonical Event Format）**

json

```
// 将不同来源事件统一为标准格式
{
  "version": "1.0",
  "source": "com.yourcompany.payment",
  "detail-type": "PaymentFailed",
  "detail": { 
    "orderId": "12345",
    "reason": "InsufficientFunds"
  }
}
```

- **优势**：消费者无需解析不同源的事件结构。

#### **2. 级联事件与 Saga 模式**

![deepseek_mermaid_20250530_5695f9](C:\Users\msduser\Desktop\学习笔记\assets\deepseek_mermaid_20250530_5695f9.png)

- **优势**：通过事件驱动实现跨服务编排。

#### **3. 安全与治理**

- **加密**：使用 KMS 加密事件总线。
- **权限**：基于资源的策略控制谁可发送事件到总线。
- **审计**：通过 CloudTrail 跟踪所有事件路由操作。

------

### **六、与其他服务的关键集成**

| **服务**           | **集成能力**                       | **典型场景**               |
| :----------------- | :--------------------------------- | :------------------------- |
| **CloudWatch**     | 告警状态变化作为事件源             | 实时触发自动化运维         |
| **Step Functions** | 将事件作为状态机触发或执行结果回传 | 构建跨服务工作流           |
| **Lambda**         | 最灵活的目标，处理复杂事件逻辑     | 事件数据清洗/业务处理      |
| **SQS**            | 事件缓冲，防止目标过载             | 异步解耦高吞吐场景         |
| **SageMaker**      | 触发模型训练或推理                 | 数据就绪时自动启动 ML 管道 |

------

### **总结：为什么必须用 EventBridge？**

1. **架构解耦**：
   事件生产者无需感知消费者存在，新增功能只需订阅事件。
2. **统一事件治理**：
   集中管理所有事件的路由、转换、存档，替代分散的 SNS/SQS/Kafka 组合。
3. **生态集成**：
   原生连接 AWS 服务与 SaaS 应用，避免自定义集成代码。
4. **成本与效率**：
   按事件量付费，无预置资源，减少运维负担。

> **核心价值**：EventBridge 不是“又一个消息服务”，而是 **事件驱动架构（EDA）的中枢神经系统**。当您的系统需要响应**多种事件源**、动态路由到**多个目标**、且要求**弹性可扩展**时，它是无可替代的基石服务。



说白了，为什么需要eventbridge？因为比起SNS，Kinesis等建立起来的事件监视和处理pipline,以eventbridge为中心的pipline构建和维护更加简单，其能力上限比其他服务要高。也就是说，其不适用于简单的，需要减少运营费用的场景。



#### 实际案例

##### 假设我需要建设一个实时检测AGF运行的pipline

该系统需要达成以下任务：

1.存储AGF的task数据在DynamoDB

2.每次任务完成后通知自动仓库管理人员

3.将task数据推送至数据流，用于后续的数据分析



##### 系统架构大致如下：

[AGV System / App]
       |
       | (Put event)
       v
  [EventBridge (Custom Bus)]
       |
  ┌────┼────────────┬─────────────┐
  |    |            |             |
[Lambda]        [SNS Topic]   [Kinesis Data Stream]
(Store event)   (Notify Ops)  (Analytics Pipeline)



具体步骤：

1.创建一个自定义的event bus

bash: aws events create-event-bus --name AGVTaskBus

2.设置消息来源：

使用json文件进行设置

[
  {
    "Source": "agv.system",
    "DetailType": "TaskCompleted",
    "Detail": "{\"agvId\":\"AGV-001\", \"taskId\":\"T123\", \"status\":\"success\", \"timestamp\":\"2025-05-29T08:00:00Z\"}",
    "EventBusName": "AGVTaskBus"
  }
]

bash: aws events put-events --entries file://agv-task-event.json



### 🔹 3. 创建 Lambda 函数

**(a)** 创建一个 Lambda 函数（或使用已有函数）：

```
bashCopyEditaws lambda create-function \
  --function-name StoreTaskEvent \
  --runtime python3.12 \
  --handler lambda_function.lambda_handler \
  --role <你的 Lambda IAM 角色 ARN> \
  --zip-file fileb://lambda.zip
```

**(b)** 授权 EventBridge 调用该函数：

```
bashCopyEditaws lambda add-permission \
  --function-name StoreTaskEvent \
  --statement-id AllowEventBridgeInvoke \
  --action lambda:InvokeFunction \
  --principal events.amazonaws.com \
  --source-arn arn:aws:events:<区域>:<账户ID>:rule/AGVTaskCompletedRule
```

------

### 🔹 4. 创建事件规则（匹配你要监听的事件）

```
bashCopyEditaws events put-rule \
  --name AGVTaskCompletedRule \
  --event-bus-name AGVTaskBus \
  --event-pattern '{
    "source": ["agv.system"],
    "detail-type": ["TaskCompleted"]
  }'
```

------

### 🔹 5. 设置事件目标为 Lambda 函数

```
bashCopyEditaws events put-targets \
  --rule AGVTaskCompletedRule \
  --event-bus-name AGVTaskBus \
  --targets '[{
    "Id": "StoreLambda",
    "Arn": "arn:aws:lambda:<区域>:<账户ID>:function:StoreTaskEvent"
  }]'
```

------

### 🔹 6. （可选）添加更多目标

#### → SNS 通知

- 创建 SNS Topic
- 将其作为另一个目标绑定到规则上

#### → Kinesis 数据流

- 创建数据流
- 也可以设置为目标，数据可用于后续分析或持久化处理

------

## 📂 Lambda 函数示例（Python）

```
pythonCopyEditimport json

def lambda_handler(event, context):
    detail = event['detail']
    print(f"收到 AGV 任务事件: {detail}")
    # 可以将数据写入 DynamoDB 等数据库
    return {"status": "ok"}
```

------

## ✅ 最终你将拥有：

- 一个用于接收 AGV 事件的自定义 EventBridge 总线
- 一条只匹配任务完成事件的规则
- 一个接收事件的 Lambda 函数
- 可选的通知（SNS）或数据分析（Kinesis）目标



#### 有关AWS CloudTrail：



CloudTrail 的主要作用：

🔍 跟踪并记录您 AWS 账户中发出的每个 API 调用——无论它来自：

AWS 管理控制台

AWS CLI

SDK

其他 AWS 服务

甚至是自动化的内部系统活动

🧠 CloudTrail 会同时跟踪内部 AWS 调用和来自互联网的外部调用吗？
是的——CloudTrail 会记录所有 API 调用，无论其来源如何：

外部调用：来自互联网上访问您的 AWS 资源的用户、开发者、工具或应用程序。

内部调用：来自代表您行事的 AWS 服务（例如 Lambda 触发 S3，或 Auto Scaling 创建 EC2 实例）。

示例：

您删除一个 S3 存储桶 → CloudTrail 会记录它。

Lambda 函数自动启动一个 EC2 实例 → CloudTrail 会记录它。

IAM 策略更新 → CloudTrail 会记录它。

❗CloudTrail 的功能就这些吗？
基本上是的——CloudTrail 的任务只有一个：对 API 活动进行完整的审计日志记录。

但它还提供了基于该任务构建的一些额外强大功能：

功能描述
管理事件 记录资源的创建/更新/删除操作（默认启用）
数据事件 记录对象级别的活动（例如，每次访问 S3 对象或 Lambda）
CloudTrail Insights 自动检测异常行为模式（例如，API 调用激增）
事件转发 可以将日志转发到 CloudWatch Logs、EventBridge 或 Lambda

因此，虽然 CloudTrail 专注于 API 调用日志记录，但您可以使用这些日志构建安全警报、自动化和合规性管道。

🧩 摘要
CloudTrail 功能重点
跟踪 API 活动 ✅ 是（主要任务）
来自内部 AWS 服务 ✅ 是
来自外部用户/应用 ✅ 是
分析异常行为 ✅ （通过 CloudTrail Insights）
将日志发送到其他服务 ✅ （S3、CloudWatch Logs、EventBridge）
监控性能（CPU 等）❌ 否（这是 CloudWatch 的任务）

##### 注意点：

cloudTrail只记录API Call，不论是AWS内部的还是外部的。但是其可以记录并监控的只有AWS自身有的API。其可以知道任何服务调用AWS API时的具体API是哪一个，然后其请求是通过哪个权限（IAM Role/User），还有诸如来源的IP地址（谁请求的），然后具体时间，时区等信息。其不会记录API Call的body和response，以免数据泄露。

但是如果你自己造一个API，然后把它放到EC2 instance或者什么别的地方运行，然后在AWS系统外部call这个API，CloudTrail是不会知道的。判断cloudTrail是否知道，只需要确定该流程内是否有用到AWS服务。如果有，则cloudTrail可以知道。如果你想监控你自己创造的API。你需要在API内添加记录log的功能，然后将这个log送到如RDS，S3，CloudWatch log等地方。



#### CloudTrail   event

CloudTrail Event是cloudtrail的一个log记录文件。其用于记录向AWS 服务发起的API Call。其详细记载了谁发出的请求，请求时间，请求对象，请求从哪里来，以及其结果。

例子如下：

**{**
  **"eventVersion": "1.08",**
  **"eventTime": "2025-06-04T00:41:16Z",**
  **"eventSource": "s3.amazonaws.com",**
  **"eventName": "PutObject",**
  **"awsRegion": "ap-northeast-1",**
  **"sourceIPAddress": "203.0.113.10",**
  **"userAgent": "Boto3/1.26.0 Python/3.9",**
  **"userIdentity": {**
    **"type": "AssumedRole",**
    **"principalId": "ABC1234567890",**
    **"arn": "arn:aws:sts::123456789012:assumed-role/EC2SSMRole/i-0abc123def456",**
    **"accountId": "123456789012"**
  **},**
  **"requestParameters": {**
    **"bucketName": "my-bucket",**
    **"key": "example.txt"**
  **},**
  **"responseElements": {**
    **"x-amz-request-id": "C0A1...",**
    **"x-amz-id-2": "kJdFi+..."**
  **},**
  **"eventID": "8f3f0d90-ef7a-4ab1-a3aa-76dfb0a14e07",**
  **"eventType": "AwsApiCall"**
**}**

CloudTrail Event内记录3种事件，1.management event（用于对AWS资源而执行的活动）2.Data Event（大量的数据活动，如读，写，调用Lambda等）3.Insight Event（用于检测账户上的不寻常活动，如短时间的多次IAM活动等）Insight会检测日常活动以创建一个底线，然后以此为基础检测不寻常的活动。通常来说，cloudtrial event的日志只保存90天，如果需要保存更长时间，需要将其保存到S3.

cloudtrail无法精确记录到OS级别的时间，所以在EC2 instance上装数据库，需要你自己设置log。但如果你使用的是DynamoDB，则cloudTrail可以追踪到你对数据库做的修改。因为DynamoDB是完全由AWS托管的服务。所以绝大部分行动都由API完成。



关于AWS Config：

AWS Config也是AWS旗下的一种监控服务。其直接对比对象是CloudTrail.但这两者的职能范围并不相同，各有分工，结合起来使用能事半功倍。AWS Config检测的是什么东西变了，其变化的前后状态。而CloudTrail记录的是什么时候发生了什么事情。谁干了什么？

假设有人移除了某个S3 bucket的加密设定，则在AWS Config上就会显示其变化前后的状态，包括设置的映射。

变化前：

{
  "BucketName": "secure-logs",
  "Encryption": {
    "SSEAlgorithm": "aws:kms"
  },
  "PublicAccessBlock": true,
  ...
}

变化后：

{
  "BucketName": "secure-logs",
  "Encryption": null,
  "PublicAccessBlock": true,
  ...
}

AWS Config是个区域性（region）服务，所以你可以针对各个区域来设置监控。而且每个区域，每个账号的监控log可以被聚合。用于做后续的数据分析。

在AWS Config中，可以设置Config rule。此Config Rule和eventbridge的Rule有点像。你可以在选择已有的rule来决定监测什么。或者可以自己创建一个特定的rule。如检测每台EC2 instance的类型是不是t2.micro等。这些rule可以根据时间段来激活，或者每次config改变时启动。

请注意，AWS config和其他检测服务一样，无法阻止所检测的活动进行。

#### 关于AWS Config Rules-----Remediations

字面意思，Config Rule可以与补救措施进行连接。实现这个功能的关键是SSM Automation Document。 其实就是用一个脚本文件，当检测的服务出现某种特定异常时，可以启用此脚本来进行修复。

"SSM Automation Document" 指的是 **AWS Systems Manager (SSM) Automation** 功能中使用的核心构件。它是一种 **JSON 或 YAML 格式的脚本文件**，用于定义、记录和执行一组在 AWS 资源（尤其是 EC2 实例）上自动执行的操作。



以下是关键点的详细解释：

1. **核心功能：**
   - **定义自动化流程：** 它详细列出了自动化任务需要执行的步骤序列。
   - **执行操作：** 这些步骤可以包括启动/停止实例、创建 AMI、运行命令（通过 SSM Run Command）、调用 Lambda 函数、等待特定条件、审批步骤、发送通知等等。
   - **参数化：** 文档可以定义输入参数，使得同一个文档可以灵活地用于不同的场景（例如，指定不同的实例 ID 或 AMI 名称）。
   - **安全与权限：** 文档执行时使用 AWS Identity and Access Management (IAM) 角色，确保操作具有所需的最小权限。
   - **输出：** 文档可以定义输出结果，供后续步骤或其他流程使用。
2. **类比：**
   - 想象它是一个 **菜谱**：列出了制作一道菜（完成一个自动化任务）所需的原料（输入参数）、步骤（操作）和预期的结果（输出）。
   - 想象它是一个 **剧本**：导演（SSM Automation 服务）根据剧本（Automation Document）的指示，指挥演员（AWS 资源）完成一系列动作。
3. **类型：**
   - **AWS 预置文档：** AWS 提供了大量开箱即用的文档，用于执行常见任务，例如：
     - `AWS-StartEC2Instance` / `AWS-StopEC2Instance`
     - `AWS-CreateImage` (创建 AMI)
     - `AWS-RunPatchBaseline` (打补丁)
     - `AWS-RestartEC2Instance`
     - `AWS-UpdateLinuxAmi` / `AWS-UpdateWindowsAmi`
     - `AWS-ConfigureS3BucketLogging`
     - 等等。
   - **自定义文档：** 用户可以创建自己的文档来满足特定的、独特的自动化需求。这提供了极大的灵活性。
4. **用途（常见场景）：**
   - **批量管理实例：** 启动、停止、重启大量 EC2 实例。
   - **构建 Golden AMI：** 自动化创建和更新标准化机器镜像的过程。
   - **操作系统补丁管理：** 自动扫描和安装补丁。
   - **应用程序部署：** 自动化部署代码或配置到实例组。
   - **事件响应：** 自动响应特定事件（如实例故障），例如替换不健康的实例。
   - **资源创建与配置：** 自动化创建和配置复杂的资源栈（通常结合 CloudFormation）。
   - **合规性检查与修复：** 自动检查资源配置是否符合策略，并执行修复操作。
   - **定期维护任务：** 执行清理、备份等周期性工作。
5. **如何管理和使用：**
   - **存储：** 文档存储在 SSM 的文档数据库中。
   - **访问：**
     - AWS Systems Manager 控制台 (`Documents` 部分)。
     - AWS CLI (`aws ssm list-documents`, `aws ssm describe-document`, `aws ssm create-document`, `aws ssm start-automation-execution` 等命令)。
     - AWS SDKs。
   - **执行：** 可以通过控制台、CLI、SDK、CloudWatch Events (EventBridge)、或其他触发机制（如 Lambda）启动一个 Automation Document 的执行。
6. **优点：**
   - **标准化：** 确保任务每次都按相同、可预测的方式执行。
   - **减少人为错误：** 自动化减少手动操作导致的错误。
   - **提高效率：** 快速、一致地执行重复性任务，节省时间和精力。
   - **可复用性：** 编写一次，多次使用，并可参数化以适应不同环境。
   - **可审计：** 每次执行都有详细的日志记录在 CloudTrail 和 SSM 控制台中。
   - **安全性：** 通过 IAM 角色严格控制权限。
   - **与 AWS 生态集成：** 无缝与其他 AWS 服务（如 IAM, CloudTrail, EventBridge, Lambda, CloudFormation 等）协作。

例子：你可以使用AWS Config来检测某个密钥的使用期限，当其使用期限过了之后，AWS Config会触发其设定，然后使用某个预编的SSM Automation Document来更新这个密钥的使用时限。（有5次重复次数，以防一次修改不成功）

但是请注意，如果建立一个客制的rule，其本质意思是让你用python或node.js去写一个Lambda function.然后AWS Config会触发你的Lambda function，每当有对应的资源被改变时。然后，其会返回一个结果， COMPLIANT，NON_COMPLIANT，NOT_APPLICABLE三者之一。

