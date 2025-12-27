#### 有关AWS ELS的个人理解与笔记

ELS（Elastic Load Balancer）弹性负载均衡器是AWS上一个重要的托管功能。其可自动将传入的应用程序流量分配到一个或多个可用区 (AZ) 中的多个目标（例如 EC2 实例、容器、IP 地址和 Lambda 函数）。它通过高效平衡工作负载来帮助提高应用程序的可用性、可扩展性和容错能力。

ELB 具有多种类型，每种类型都针对不同的用例进行定制，例如第 4 层（传输层）和第 7 层（应用层）负载平衡。(ELS 使用OSI模型)

### **AWS 弹性负载均衡（ELB）需要执行的所有任务**

AWS **Elastic Load Balancer（ELB，弹性负载均衡）** 负责高效地管理和分配流量，以**确保高可用性、可扩展性、安全性和性能**。以下是 ELB 需要执行的所有主要任务。

------

## **1. 流量分发与负载均衡**

### **✅ 将流量分发到多个后端目标**

- ELB 确保不会让某个后端实例（EC2、容器、Lambda）超载。
- 根据负载均衡算法**均匀分配流量**。

### **✅ 支持不同类型的流量**

- **ALB（应用负载均衡）：** 处理 **HTTP/HTTPS（第 7 层）** 流量。
- **NLB（网络负载均衡）：** 处理 **TCP/UDP（第 4 层）** 流量。
- **CLB（经典负载均衡）：** 处理 **第 4 层和第 7 层流量（旧版本）**。

### **✅ 基于规则的流量路由**

- **基于路径路由**（`/api/*` → 目标组1，`/dashboard/*` → 目标组2）。
- **基于主机名路由**（`app.example.com` → 目标组1，`api.example.com` → 目标组2）。
- **基于查询参数路由**（不同参数返回不同的后端服务）。
- **基于 HTTP 头的路由**（例如，根据 `User-Agent` 发送不同的响应）。

------

## **2. 健康检查与故障转移**

### **✅ 对后端目标执行健康检查**

- ELB **定期检查** 后端实例是否正常运行。
- **健康的实例** 接收流量，**不健康的实例** 自动从负载均衡池中移除。

### **✅ 自动故障转移**

- **如果实例或可用区（AZ）宕机，ELB 会自动将流量重定向到健康实例**，确保高可用性。

------

## **3. 安全与访问控制**

### **✅ SSL/TLS 终止（HTTPS 卸载）**

- ELB 可**解密 SSL/TLS 流量**，降低后端服务器的 CPU 负载。
- 通过 **AWS 证书管理器（ACM）** 处理 SSL 证书。

### **✅ 强制执行安全策略**

- 支持 **TLS 策略** 以定义加密强度。
- **AWS WAF（Web 应用防火墙）** 集成，防止 SQL 注入、XSS 等攻击。

### **✅ 访问控制（安全组 & IAM）**

- ELB 的**安全组（Security Group）** 控制允许/拒绝哪些 IP 访问。
- **IAM 权限** 限制谁可以修改 ELB 设置。

### **✅ 防御 DDoS 攻击**

- **AWS Shield Standard** 保护 ELB 免受 **DDoS 攻击**。
- **AWS WAF** 过滤恶意流量。

------

## **4. 高可用性与自动扩展**

### **✅ 自动扩展（Auto Scaling）**

- ELB 可与 **Auto Scaling 组** 配合，**根据流量负载自动扩展或缩减后端实例**。

### **✅ 多可用区负载均衡**

- ELB **在多个可用区（AZ）之间分发流量**，如果某个 AZ 出现故障，它会自动切换到其他可用区。

------

## **5. 连接管理与优化**

### **✅ 维护持久连接（Sticky Sessions）**

- ELB 支持 **会话保持（Session Stickiness）**，确保用户请求始终发送到同一个后端实例。

### **✅ 启用长连接（Keep-Alive）**

- 通过**保持连接**减少延迟，提高数据传输效率。

### **✅ 支持 WebSockets（实时通信）**

- ALB 支持 **WebSockets**，适用于聊天应用、金融交易系统等。

### **✅ 连接排空（Connection Draining）**

- **在实例终止前，确保现有连接完成处理后再关闭**，防止请求丢失。

------

## **6. 监控、日志记录与故障排查**

### **✅ 记录所有传入请求（访问日志）**

- ELB **将访问日志存储到 Amazon S3**，用于分析和审计。
- 记录内容包括 **IP 地址、请求路径、响应时间、错误信息**。

### **✅ 使用 CloudWatch 监控性能**

- 监控 **请求数量、延迟、错误率、流量模式**。
- 可配置 **警报（Alarms）**，当异常情况发生时通知管理员。

### **✅ AWS X-Ray 调试**

- **AWS X-Ray 集成**，帮助开发者追踪请求流动路径，进行性能优化和故障排查。

------

## **7. 支持多种后端目标**

### **✅ 负载均衡到不同类型的后端**

- 支持 **EC2、ECS（Docker 容器）、Lambda（无服务器计算）、本地服务器（Hybrid Cloud）**。

### **✅ 与容器化应用集成**

- ALB 可与 **Amazon ECS、Kubernetes（EKS）、Docker** 集成。
- **支持动态端口映射**，适用于容器化部署。

### **✅ 负载均衡到混合云**

- 支持将 **流量分发到本地数据中心和 AWS 之间**（混合云架构）。

------

## **8. DNS 解析与 IP 地址管理**

### **✅ 处理 DNS 解析（Route 53）**

- ELB **提供一个 DNS 名称**，而不是静态 IP 地址。
- **可通过 AWS Route 53 解析域名到 ELB**（`myapp.com → ELB`）。

### **✅ 处理静态 IP（仅适用于 NLB）**

- **NLB 支持分配静态 IP 地址**，适用于需要固定 IP 的场景。

------

## **总结表**

| **ELB 任务**                                        | **详情**                                                  |
| --------------------------------------------------- | --------------------------------------------------------- |
| **流量分发**                                        | 负载均衡多个后端服务器（EC2、容器、Lambda、本地服务器）。 |
| **健康检查 & 故障转移**                             | 监控后端健康状态，并在实例故障时自动切换流量。            |
| **安全（SSL/TLS、WAF、IAM、DDoS 保护）**            | 终止 SSL，加密流量，过滤恶意访问，防御攻击。              |
| **高可用性（Auto Scaling，多 AZ）**                 | 在多个可用区内分发流量，保证业务不中断。                  |
| **连接管理（Sticky Sessions，WebSockets，长连接）** | 管理长连接、会话保持、WebSockets 实时通信。               |
| **监控 & 日志（CloudWatch，X-Ray，访问日志）**      | 记录请求日志，监控性能，提供故障排查工具。                |
| **支持多种后端目标**                                | 负载均衡 **EC2、ECS、Lambda、本地服务器**。               |
| **DNS 解析 & IP 地址管理**                          | 提供 **DNS 名称（ALB）或静态 IP（NLB）**。                |

拿用户访问amazon来做例子：

 User Request (Amazon.com)  
       ↓  
 AWS Route 53 (DNS Resolution)  
       ↓  
 AWS Global Accelerator (1st ELB Layer)  
       ↓  
 AWS CloudFront (CDN, Load Balancing for Static Content)  
       ↓  
 AWS Regional Application Load Balancer (ALB - 2nd ELB Layer)  
       ↓  
 Microservices (ECS, EC2, Lambda) Behind Separate ALBs (3rd ELB Layer)  
       ↓  
 Amazon RDS / DynamoDB (Database)  
       ↓  
 Response Sent Back Through ELBs  
       ↓  
 User Sees the Amazon Page

请注意，ELB靠特定的服务器用于流量管理和分流。但其服务器并不是指单一服务器。ELB是分布式的集群，所以会有一个服务区域内的多个服务器一起为后台应用提供服务。而ELB并没有一个固定的IP地址(除了NLB)，所以当用户访问某个服务时，浏览器向AWS Route 53（DNS服务器) 请求对应IP地址后，其会返回多个ELB的地址，然后浏览器或系统OS根据自身规则（最近访问过的，或者可以最快连接的，或者随机选择一个）选择一个ip地址，然后向其ELB服务器发送请求。然后ELB收到请求后，再根据自身逻辑，算法来决定将请求转给后台的哪个服务器。

在尝试了首次搭建ELB，感受如下：搭建过程没想象中那么麻烦，我选择的是ALB（Application Load Balancer），创建ELB前先要先建立Target group。其实就是要被ELB托管的虚拟机组，选中想要托管的EC2 instance，然后加入target group便可。然后在创建ELB时选择对应Target group。在创建后，必须保证被托管的EC2 instance有一个正常允许的Http Web server来保证ELB服务器可以与其正常通讯。在Amazon linux的系统中，推荐使用Nginx来作为Http web 服务器。安装即可，不用额外的设置。而如果使用httpd，则需要进行额外的设置，否则无法通过健康检测（ELB检测是否可以与EC2 instance正常连接，通讯）。同时需要保证security group里有设定inbound rule是让ELB server可以与EC2 instance通讯。通常在创建ELB时，会设置在什么协议下进行通讯。如HTTP port80，ssh port22等。这在security group也要进行设置。

其实amazon linux上的Httpd和ubuntu上的apache2是同一个软件。但在两个os上表现不同。在ubuntu上，apache2会更容易使用，不需要设置什么东西。但在amazon linux上的httpd，由于os有更严格的安全设定的原因，一开始，httpd会否决所有请求连接。所以，如果尝试连接，会有403出现。所以就需要在conf文件下进行权限修改。

1.sudo nano /etc/httpd/conf/httpd.conf

2.找到<Directory/>
              Require all denied
          </Directory>

3.改成 <Directory "/var/www/html">
                    Require all granted
              </Directory>

当然，有可能该文件的权限属于root账户，所以需要修改权限。

所以，其实向Mysql，apache,nginx,ssh等重要软件，其设定主要由os控制，如果你的os是对安全比较严格（如Amazon linux），则一开始，你基本不会有什么权限去进行操作，需要进行设定。而对于比较宽松的os，如(ubuntu)则则默认都是access granted。所以只是测试连通性的话，不需要什么设定。对于第三方的软件和环境，则不存在问题，因为其设置由软件自己掌握（如node.js,java app，docker等）。

有关NLB：

NLB（Network load Balancer）在osi模型的第四层（网络层）运作。所以其可以直接处理TCP或UDP的流量。所以NLB可以处理短时且大量的网络流量。NLB的特点之一是每个AZ只有一个固定IP。而ALB和CLB都只有固定的域名，而没有固定IP。

然后NLB的health check是使用TCP，HTTP,HTTPS三种协议。如果后台无法正常回应NLB服务器的请求，则会被判定为不健康。NLB将不会向其发送信息。NLB使用过程中，可以全程使用同种协议（如TCP或者UDP），或者前半段使用TCP或者UDP，后半段使用HTTP或HTTPS，所以NLB的健康检查时同时支持TCP，UDP和HTTPS，HTTP。

关于TLS Passthrough：自如其名，passthrough,客户端向NLB服务器发起一个TLS连接申请，NLB不对此做解密工作，而是将请求传给处于负载均衡内的后台服务器，由后台服务器来处理其申请。但此过程，需要后台服务器有处理TLS的能力（有SSL证书）

有关GLB：

GWLB（AWS Gate Load balancer）网关负载均衡器。作用在网络层。是所有负载均衡中处于最底层的存在。此负载均衡用于你想先对用户的流量做检测，如能否通过防火墙，检查数据包内部内容等，是专门为了网络安全而生。其作用过程为：首先，用户向GLB发送请求，然后，GLB会通过分流的方式，将其转发到一个用于检测数据包安全性的target group。其中的检测如果失败，则此请求会被丢弃。如果成功，此target group会把请求转回GLB服务器，然后GLB服务器在传给其管理的后台服务器。请注意，网关负载均衡器是单一入口，单一出口，对于所有的网络流量都是如此。



`X-Forwarded-For` (XFF) is an **HTTP header** used to track the **original IP address of a client** when their request passes through a proxy or load balancer.

🚀 **Why is it needed?**
 When a request goes through a **proxy, CDN, or load balancer**, the original client IP **gets hidden** because the request appears to come from the proxy. The `X-Forwarded-For` header **keeps track of the real client’s IP**.

在port 6081上使用GENEVE（Generic Network Virtualization Encapsulation）通用网络虚拟化封装 协议。

这是一种网络隧道协议。旨在为现代数据中心和云环境提供灵活、可扩展的覆盖网络（Overlay Network）解决方案。

### **2. 封装格式**

GENEVE数据包的封装结构如下（从外层到内层）：

1. **外层传输头**：
   - 使用**UDP协议**，目标端口号为**6081**。
   - 源端口可由实现动态选择，支持负载均衡和ECMP（等价多路径）。
2. **GENEVE头部**：
   - **固定部分（8字节）**：
     - `Version`（2位）：协议版本（当前为0）。
     - `Opt Len`（6位）：选项字段的总长度（以4字节为单位）。
     - `OAM`（1位）：用于操作、管理和维护（OAM）帧的标识。
     - `Critical`（1位）：指示选项是否关键（若设备无法解析关键选项，需丢弃数据包）。
     - `Reserved`（6位）：保留字段。
     - `Protocol Type`（16位）：内层协议类型（如Ethernet的0x6558）。
     - `VNI`（24位）：虚拟网络标识符，用于多租户隔离。
     - `Reserved`（8位）。
   - **可变选项字段**（长度由`Opt Len`定义）：
     - 每个选项以TLV格式存储，支持自定义元数据（如安全策略、路径跟踪信息）。
3. **原始负载**：被封装的原始数据帧（如以太网帧）。



在GLB封装

### **1. 各组件职责的明确划分**

#### **(1) 负载均衡器（GLB）的职责**

- **封装流量**：将原始数据包（如 HTTP 请求）封装到 GENEVE 隧道中。
- **添加元数据**：在 GENEVE 头部插入 TLV 字段（如租户 ID、策略标记），为安全设备提供上下文。
- **路由转发**：将封装后的流量通过隧道发送到配置好的第三方安全设备（如防火墙、IDS/IPS）。

**关键点**：

- **负载均衡器不进行安全检查**，它仅负责封装和路由，安全判断是后续步骤。
- TLV 字段中的元数据是“提示信息”，而非安全结论。例如，它可能标记“此流量需要应用策略 A”，但策略的具体执行由安全设备完成。

------

#### **(2) 第三方安全设备的职责**

​	这里的第三方指os自带的防火墙，或者用户购买的其他防火墙软件（AWS 也有自己的防火墙，专为AWS上的程序服务）。

- **解封装**：剥离 GENEVE 头部，还原原始数据包（如 TCP/UDP 负载）。
- **深度检测**：对原始数据包进行安全检查，包括：
  - **基于元数据的策略匹配**：例如，根据 TLV 中的 `tenant_id` 选择对应的防火墙规则。
  - **基于内容的深度扫描**：例如，检测 HTTP 负载中的恶意代码、SQL 注入等（需要解析到应用层）。
- **决策与转发**：
  - 如果流量安全：重新封装为 GENEVE 数据包，发送到后端服务。
  - 如果流量不安全：丢弃或记录告警。

**关键点**：

- **安全检查的核心步骤发生在解封装之后**，安全设备必须看到原始流量内容才能彻底分析。
- TLV 字段仅用于辅助决策（如加速策略匹配），但不能替代实际内容扫描。





##### 有关sticky Session

**Sticky Session（粘性会话）** 是一种负载均衡策略，用于确保用户的多次请求在会话期间被定向到同一台后端服务器。以下是详细解释：

------

### **1. 为什么需要 Sticky Session？**

在分布式系统中，负载均衡器通常将请求分发到多台后端服务器以提高性能和可靠性。但某些场景下，**用户需要与同一台服务器保持连续交互**，例如：

- **会话状态维护**：用户登录后的购物车信息、表单填写进度等存储在服务器内存中。
- **文件上传/处理**：大文件分块上传需发送到同一服务器进行拼接。
- **缓存优化**：重复访问同一服务器可复用本地缓存，减少延迟。

若没有粘性会话，用户的多次请求可能被分发到不同服务器，导致状态丢失或操作中断。

------

### **2. Sticky Session 的工作原理**

负载均衡器通过某种标识将用户请求“绑定”到特定服务器。常见的标识方式包括：

#### **(1) 基于 Cookie**

- **负载均衡器注入 Cookie**：在首次响应中添加唯一 Cookie（如 `JSESSIONID=abc123`）。
- **后续请求携带 Cookie**：客户端发送请求时附上此 Cookie，负载均衡器根据 Cookie 值路由到同一服务器。
- **类型**：
  - **植入式 Cookie**：由负载均衡器生成并管理（如 AWS ALB 的 `AWSALB`）。
  - **应用生成 Cookie**：由后端服务器生成（如 Tomcat 的 `JSESSIONID`），负载均衡器仅读取。

#### **(2) 基于源 IP 地址**

- 根据客户端 IP 的哈希值分配服务器。
- **缺点**：同一 NAT 后的多个用户会被视为同一客户端，可能导致负载不均。

#### **(3) 基于自定义 Header 或 URL 参数**

- 例如通过 `X-User-ID` 或 URL 中的 `?session_id=xyz` 标识用户。

------

### **3. 配置示例（常见负载均衡器）**

#### **(1) Nginx（基于 Cookie）**

nginx

复制

```
upstream backend {
    sticky cookie srv_id expires=1h domain=.example.com path=/;
    server 10.0.0.1;
    server 10.0.0.2;
}

server {
    location / {
        proxy_pass http://backend;
    }
}
```

- `sticky cookie`：启用基于 Cookie 的粘性会话。
- `srv_id`：Cookie 名称。
- `expires=1h`：Cookie 有效期 1 小时。

#### **(2) AWS Application Load Balancer (ALB)**

- 在目标组（target group）设置中启用粘性会话，选择基于 `AWSALB` Cookie 或应用生成的 Cookie。
- 支持设置粘性持续时间（如 1 天）。

------

### **4. 优缺点分析**

| **优点**                                 | **缺点**                               |
| :--------------------------------------- | :------------------------------------- |
| 简化有状态应用的设计（无需共享会话存储） | 可能导致负载不均（某些服务器压力过大） |
| 提升缓存命中率，降低延迟                 | 服务器故障时会话中断（需结合健康检查） |
| 兼容传统单机会话管理代码                 | 扩展性受限（新增服务器需重新分配会话） |

------

### **5. 替代方案：无状态设计**

若需避免 Sticky Session 的缺点，可采用以下方式：

- **共享会话存储**：使用 Redis、Memcached 等集中存储会话数据，所有服务器均可访问。
- **JWT（JSON Web Token）**：将会话数据加密存储在客户端 Token 中，服务器无需本地存储。

------

### **6. 注意事项**

- **超时设置**：合理设置粘性持续时间，避免服务器资源浪费或用户意外断开。
- **故障转移**：结合健康检查自动剔除故障节点，并设计会话迁移机制（如备份存储）。
- **合规性**：基于 Cookie 的方案需符合隐私法规（如 GDPR），明确告知用户。



#### Cross Zone Load Balancing

假设你有两个负载均衡服务器，分别位于两个不同的AZ，第一个管理两台服务器，第二个管理8台服务器，当开启跨区域负载均衡，对于用户来说，他们的请求会随机访问10台后台服务器。所以每台服务器分配的请求流量都是10%。如果不开启，则两台负载均衡服务器个分担50%的流量，则第一的AZ的两台服务器分担总流量的25%，第二个AZ的8台后台服务器分别承担总流量的6.25%。



### **SSL/TLS 基础**

#### **(1) 核心目标**

- **加密**：防止数据在传输中被窃听（如 HTTP 明文→HTTPS 加密）。
- **认证**：通过证书验证服务器身份，防止中间人攻击。
- **完整性**：确保数据未被篡改（如使用 HMAC 校验）。

#### **(2) 协议演进**

- **SSL（Secure Sockets Layer）**：已淘汰（存在 POODLE 等漏洞）。
- **TLS（Transport Layer Security）**：现代标准（TLS 1.2/1.3 为主）。

------

### **2. TLS 握手流程（以 TLS 1.2 为例）**

plaintext

复制

```
1. Client Hello
   - 支持的 TLS 版本
   - 支持的加密套件（Cipher Suites）
   - 客户端随机数（Client Random）
   - SNI 扩展（携带目标域名）  ← 关键点！

2. Server Hello
   - 选择的 TLS 版本
   - 选择的加密套件（如 TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256）
   - 服务器随机数（Server Random）
   - 服务器证书（含公钥）

3. 验证证书
   - 客户端检查证书是否由可信 CA 签发
   - 检查证书域名是否与 SNI 匹配

4. 密钥交换
   - 客户端生成预主密钥（Pre-Master Secret），用服务器公钥加密后发送
   - 双方基于 Client Random、Server Random、Pre-Master 生成会话密钥

5. 加密通信
   - 使用会话密钥对称加密后续数据
```

------

### **3. SNI（Server Name Indication）**

#### **(1) 解决的问题**

- **传统问题**：一个服务器（或负载均衡器）的单一 IP 地址无法托管多个 HTTPS 域名（每个域名需独立证书）。
- **SNI 方案**：在 TLS 握手的 `Client Hello` 中明确携带目标域名，服务器据此返回对应证书。

#### **(2) 工作原理**

- **客户端行为**：发起 HTTPS 请求时，在 `Client Hello` 的扩展字段中写入域名（如 `sni.example.com`）。
- **服务器行为**：读取 SNI 值，选择匹配的证书返回。若未匹配，可能返回默认证书（导致浏览器告警）。

#### **(3) 配置示例（Nginx）**

nginx

复制

```
server {
    listen 443 ssl;
    server_name domain1.com;
    ssl_certificate /path/to/domain1.crt;
    ssl_certificate_key /path/to/domain1.key;
}

server {
    listen 443 ssl;
    server_name domain2.com;
    ssl_certificate /path/to/domain2.crt;
    ssl_certificate_key /path/to/domain2.key;
}
```

- **要求**：Nginx 需启用 SNI 支持（默认开启）。

------

### **4. 关键注意事项**

#### **(1) 兼容性**

- **支持 SNI 的客户端**：所有现代浏览器（IE7+、Android 2.3+、iOS 4+）。
- **不支持的场景**：旧客户端（如 Windows XP 的 IE6）无法处理 SNI，导致 HTTPS 失败。

#### **(2) 证书类型**

- **单域名证书**：仅保护一个域名（如 `www.example.com`）。
- **通配符证书**：保护同一级子域名（如 `*.example.com`）。
- **SAN 证书（多域名证书）**：在单个证书中列出多个域名。

#### **(3) 安全强化**

- **禁用弱加密套件**：在服务器配置中仅启用 TLS 1.2+ 和强密码套件（如 AES-GCM、ECDHE）。
- **OCSP Stapling**：减少证书验证延迟，提升隐私性。
- **HSTS（HTTP Strict Transport Security）**：强制客户端使用 HTTPS。

------

### **5. SNI 与负载均衡器（如 AWS ALB、Nginx）**

#### **(1) AWS ALB 的 SNI 支持**

- **多证书绑定**：一个 ALB 可关联多个证书，根据 SNI 自动匹配。
- **配置步骤**：
  1. 创建或导入 ACM 证书。
  2. 在 ALB 监听器添加 HTTPS 规则，选择证书列表。
  3. 客户端请求时将根据 SNI 动态选择证书。

#### **(2) 无 SNI 的备选方案**

- **单一证书**：使用 SAN 证书覆盖所有域名（管理复杂）。
- **独立 IP 地址**：为每个域名分配独立 IP（IPv4 资源紧张，不推荐）。

------

### **6. 故障排查工具**

- **OpenSSL 命令**：测试 SNI 握手：

  bash

  复制

  ```
  openssl s_client -connect example.com:443 -servername example.com -tlsextdebug
  ```

- **Wireshark**：抓包分析 `Client Hello` 中的 SNI 字段。

- **SSL Labs Test**（https://www.ssllabs.com/ssltest/）：评估服务器 TLS 配置安全性。

关于对称加密与非对称加密：

对称加密是指，加密和解密共用同一个密钥，只有一个私钥，没有公钥，在客户端和服务端开启通话时，会有密钥传输的环节（明码传输）。

**加密过程**： 加密:  原文+密钥 = 密文 解密：密文-密钥 = 原文

常见的对称加密算法： DES, AES, 3DES等

**特点**： **优点** - 算法简单，加解密容易，效率高，执行快。 **缺点** - 相对来说不安全，只有一把钥匙，密文如果被拦截，且密钥被劫持，那么信息很容易被破译。

非对称加密指的是：加密和解密使用不同的秘钥，一把作为公开的公钥，另一把作为私钥。 公钥加密的信息，只有私钥才能解密。 私钥加密的信息，只有公钥才能解密。 常见的给对称加密: RSA,ECC

区别： 对称加密算法，加解密的效率要高很多。 但是缺陷在于对秘钥的管理上，以及在非安全信道中通讯时，密钥交换的安全性不能保障。 所以在实际的网络环境中，会将两者混合使用。

非对称加密:  A和B传输数据， A具有自己的公私钥，B具有自己的公私钥。 (公钥是在公网上公开的，任何人都能看见， 私钥自己保留)

A拿着B的公钥+信息数据， 传递给B。  这个时候 ， 只有B手里的私钥才能解开。

假设C拦截了A传递的信息，他是解不开的， 因为C没有这个公钥对应的私钥。 所以比较安全，但是，还有一个问题，就是，黑客可以假装通讯的客户端A，先拦截服务端B的公钥，向A发送自己造的假公钥，这样，也可以获得A的通讯内容，然后再用服务端B的公钥加密信息，发给服务端，那么，两端的通讯对于黑客就是透明的。

SSL，TLS证书解决的其中一个问题就是我不知道和我进行通讯的人是不是我真正想要通讯的人。所以，使用由C颁发的证书，来证明通讯的对方可信，是你想要的那个人，它发的密钥也是可信的。防止别人用此名义来和你通讯，骗你。

 直白点说，HTTPS就是在明文的上层和TCP层之间加上一层加密，这样就保证上层信息传输的安全。如[HTTP协议](https://so.csdn.net/so/search?q=HTTP协议&spm=1001.2101.3001.7020)是明文传输，加上SSL层之后，就有了雅称`HTTPS`。它存在的唯一目的就是`保证上层通讯安全的一套机制`。

随机数是指随机选择的整数，尤其是大整数。

TLS 计算最终key的方程式：

Shared Secret=(Public Key of the Other Party)Private Keymodp

SharedSecret1=(gBmodp)Amodp

SharedSecret2=(gAmodp)Bmodp

(gBmodp)Amodp =(gmodp)ABmodp =(gAmodp)Bmodp =g(A⋅B)modp

(A mod P)^k mod  P=A^k mod P，这是模运算的核心性质之一

对于TLS生成key时的计算：

在 TLS（传输层安全协议）中，密钥生成的核心是依赖**大整数**的数学特性，而不是“小数点后很多位的随机无规律数”。

------

### **1. TLS 密钥生成的核心机制**

TLS 使用的密钥交换协议（如 Diffie-Hellman 或椭圆曲线 Diffie-Hellman）和加密算法（如 RSA）均基于以下数学问题：

- **大整数的离散对数问题**（如 DH 协议）。
- **大素数的因数分解问题**（如 RSA 算法）。

这些问题的安全性依赖于**大整数的计算复杂性**，而非小数或浮点数。

------

### **2. 密钥生成中的“大数”是什么？**

#### **(1) Diffie-Hellman 密钥交换**

- **参数选择**：
  - **素数 P\*P\***：通常选择 2048 位或更大的素数（如 TLS 推荐参数）。
  - **生成元 g\*g\***：一个模 P*P* 的原根（也是大整数）。
- **私钥与公钥**：
  - **私钥**（如 a*a*）：随机生成的大整数（通常 256 位或更大）。
  - **公钥**（如 gamod  P*g**a*mod*P*）：通过模幂运算得到的大整数。

#### **(2) RSA 加密**

- **参数选择**：
  - **两个大素数 p\*p\* 和 q\*q\***：通常 1024 位或 2048 位。
  - **模数 n=p×q\*n\*=\*p\*×\*q\***：例如 2048 位的整数。
- **私钥与公钥**：
  - **私钥**（如 d*d*）：基于 p*p* 和 q*q* 生成的大整数。
  - **公钥**（如 e*e* 和 n*n*）：e*e* 是较小的整数（如 65537），n*n* 是大整数。

------

#### **3. 为什么用“大整数”而不是“小数点后很多位的****数”？**





#### **(1) 数学基础的限制**

- **离散对数和因数分解问题**仅适用于整数域，无法直接应用于小数或浮点数。
- 浮点数的精度有限，可能导致计算错误或信息丢失（例如，模运算需要精确的整数余数）。

#### **(2) 安全性需求**

- **大整数的熵（随机性）**：密钥的安全性依赖于随机生成的大整数的不可预测性。例如：
  - 一个 256 位的随机整数有 22562256 种可能，远超宇宙原子总数（约 10801080）。
  - 小数点后的随机数无法提供类似的熵，且浮点数的表示方式可能引入规律性。

#### **(3) 计算效率**

- **整数运算的高效性**：现代计算机和密码学库对整数运算（如模幂、大数乘法）有高度优化。
- **浮点数的复杂性**：浮点数运算涉及舍入误差、精度管理，不适合加密协议的精确性要求。

------

### **4. TLS 如何生成这些“大数”？**

#### **(1) 随机数生成器（CSPRNG）**

- TLS 使用**密码学安全的伪随机数生成器**（CSPRNG）生成私钥和大素数。
- 例如：
  - OpenSSL 的 `RAND_bytes()` 函数。
  - 操作系统提供的熵源（如 Linux 的 `/dev/urandom`）。

#### **(2) 大素数的生成**

- **步骤**：
  1. 随机生成一个大奇数（如 2048 位）。
  2. 使用素性测试（如 Miller-Rabin 测试）验证其是否为素数。
  3. 重复直到找到符合条件的素数。

------

### **5. 常见误解澄清**

#### **(1) “小数更随机”**

- 小数的“小数点后很多位”并不意味着更高的安全性。密钥的安全性取决于**熵的大小**（即随机比特的长度），而非数值形式。
- 例如：一个 256 位的整数的熵为 22562256，而一个 256 位小数（如 0.12345...）的熵可能因存储格式受限而降低。

#### **(2) “浮点数更难破解”**

- 浮点数在存储时可能丢失精度（如 IEEE 754 双精度浮点数只有 53 位有效位），导致信息泄露。
- 整数运算的确定性和精确性使其更适合加密算法。

------

### **6. 实际示例**

#### **(1) TLS 1.3 中的 DH 参数**

- **推荐参数**：使用 2048 位的素数 P*P* 和 256 位的私钥 a*a*。

- **生成方式**：

  python

  复制

  ```
  # 伪代码：生成 Diffie-Hellman 私钥
  from cryptography.hazmat.primitives.asymmetric import dh
  parameters = dh.generate_parameters(generator=2, key_size=2048)
  private_key = parameters.generate_private_key()
  ```

#### **(2) RSA 密钥对**

- **生成命令**（OpenSSL）：

  bash

  复制

  ```
  openssl genpkey -algorithm RSA -out private_key.pem -pkeyopt rsa_keygen_bits:2048
  ```



##### Auto Scaling Group

用于管理EC2 instance资源，可自动增加或减少instance以保证连接效率和instance利用率

scale out = 增加 EC2 instance

scale in = 减少 EC2 instance

在ASG的管理下如果一个EC2 instance被认为不健康，则会被关掉，使用别的，健康的来代替它的位置。

指定ASG策略的参考：

1。CPU使用率

2。时间点（什么时间段增加，什么时间段减少）

3。IOPS数

4.对未来的预测（比如从某日开始，每周增加一个）

还可以设定当EC2 instance改变后，有一个冷却时间，在此时间内，ASG不做任何变更动作
