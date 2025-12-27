### 有关AWS CloudFront的笔记和个人理解



CloudFront是AWS旗下的一种CDN服务。

在了解CloudFront是什么前，需要了解CDN是什么。

CDN（Content Delivery Network）在物理意义上是坐落于世界各个国家的主干城市的数据中心的服务器集群。其作用是充当服务端和用户浏览器之间的缓冲与衔接。CDN的出现源于网络的快速发展，用户出现了访问世界各地的网站的需求，但受限于当时的数据传输技术等各种各样的限制，用户访问离自己物理位置很远的服务器里的网站时，回应速度很慢，甚至断连的情况频出。所以CDN便出现了。CDN被设置于主干城市的数据中心，是因为这里的网速和带宽最高。可以利用高速网络与世界各地的服务器连接。

CDN的工作原理与逻辑：假设一个用户需要访问某个网站，浏览器会向DNS服务器请求网站的ip地址。DNS服务器不直接返回源服务器的ip地址，而是返回最近的CDN服务器的ip地址。然后浏览器向CDN服务器请求访问网站的资源。如果有（CDN cache hit），则直接返回所需资源。如果没有，CDN服务器向源服务器请求资源，资源获取到之后，CDN自身会缓存这些资源，并建立一个TTL（Time to Live）。然后，CDN服务器将网站资源返回给用户的浏览器。

有一点需要注意的是，CDN只是作为用户和服务端沟通的桥梁，如果用户访问的网站需要互动的话，还是需要和源服务器沟通。比如买东西。在这些场景下，CDN可以做到的为：

1.优化网络路径，帮助用户连接到附近的最近的边缘服务器，以便降低向源服务器发送请求的延迟。

2.负责在边缘服务器的SSL握手。简化了通讯加密的过程，以加速访问。

3.可以通过客制化的逻辑优化某个或某些网站的访问流程，以减少交互延迟（其实就是在CDN上自己编一套针对某个或某些网站的访问逻辑，编程），这种客制化的逻辑程序被称为边缘程序，通常支持javascript,typescript,node.js。



CDN从90年代后期开始出现第一代，2005年出现二代，2010年第三代，目前是第四代（2020年出现）。第一代只能缓存简单的图片，js，css文件。且都以HTTP为基础。二代开始，支持缓存视频数据流和大型文件，开始支持动态内容加速（Dynamic Content acceleration）。第三代开始与各种云端平台进行融合，如CloudFront，同时CDN开始作为网络中的安全层。用于防护网站遭受SSL，DDoS，WAF攻击。并且边缘脚本出现（解决CDN的定制化流量导向需求，说白了就是在CDN服务器编写自己的导向，缓存逻辑）。第四代，也是最新的一代，可以进行边缘计算，然后缓存和导流的逻辑开始由AI驱动，实时log等功能也开始被使用.

### **云计算服务的类型** 

云计算服务一般有三种类型：

### **IaaS** 

- 英文全称：`Infrastructure as a service`
- 中文名称：**基础设施即服务**

IaaS，供应商为用户提供对存储、网络和服务器等计算资源的访问，公司在服务提供商的基础架构中使用自己的平台和应用程序。

说人话就是，你想部署一个自己造的网站，但你自己没法获取到足够的硬件资源。所以云可以帮你提供基础设施硬件，然后，你在此基础上，搭建自己的环境，然后部署你的程序。

#### **IaaS使用场景**

- 灾难恢复
- 自动扩展和集群管理
- [高性能计算](https://cloud.tencent.com/product/thpc?from_column=20065&from=20065) (HPC) 和[大数据分析]





### **PaaS** 

- 英文全称：`Platform as a service`
- 中文名称：**平台即服务**

PaaS，供应商为客户提供完整的应用程序开发环境，允许他们开发和管理应用程序，而无需构建耗时的开发环境。

使用 PaaS 的用户还可以访问应用程序堆栈中的各种资源，例如中间件、编程语言、操作系统和数据库。

PaaS 最大的好处是**无需重复造轮子**，公司可以利用 API 快速组装第三方解决方案的集合，开发团队可以按月支付费用并使用资源来构建和部署应用程序，这样的话比从头开始构建更快。

这个Paas以刚才的Iaas为基础，云服务商替你建立环境，你只要开发好程序，然后部署就行。

#### **PaaS使用场景**

- 全周期自动化和可组合服务
- 实现快速应用程序开发

#### **PaaS例子**

- Google App Engine
- 红帽 OpenShift
- Heroku
- Apprenda



### **SaaS** 

- 英文全称：`Software as a service`
- 中文名称：**软件即服务**

SaaS也称为云应用程序服务，是最全面的云计算服务形式，通过 Web 浏览器提供由提供商管理的整个应用程序，SaaS 非常适合没有能力开发自己的软件应用程序的小公司或初创公司。

这个也很好理解，你没有软件开发能力，那么就使用云服务公司提供的软件，来为顾客提供服务。



#### ok，所以CloudFront是什么

cloudFront是AWS旗下的一种，与AWS服务深度融合，可编程，安全性高，为无状态网络而生的一种CDN服务。

其特点之一是和各种AWS服务深度融合，所以可以通过很简单的设置，和其他AWS服务融合。如和S3，EC2/ELB联动，作为网站内容传输和缓存的中介。其二是安全性高。可以通过设置，启用文件加密，签名URL，cookie，食用内部防火墙防护DDoS攻击等。特点其三是，可以在其中对数据进行计算或预处理，再进行传输。

请注意，CloudFront是全球性的服务。所以没有AZ，cross-origin等设置。也正因为其是全球性的服务，所以会根据每个边缘地点的数据缓存来收费。在收费上，可以选择三种收费方式。第一种是ALL，顾名思义，可以让你接入到全球任意一个边缘地点。第二种是200，除去最贵的地区，可以让你接入世界上绝大部分的边缘地点。最后一个，也是便宜的，是100.只会让你接入最便宜的边缘地点（多数为某个区域）

在地域限制上，CloudFront可以制造黑/白名单。允许或不允许某些国家或地区的用户接入此CDN。

在网站内容更新后，原本CDN内储存的资源有可能便不再需要。但因为TTL的原因，某些资源可能无法被及时更新。所以便需要用到Cache Invalidation。即把指定文件目录下的cache全部清除，则下次用户再访问CDN请求资源时，则需要再一次向源服务器请求新的资源。

CloudFront设置：

### 1. **Origin Settings** – Where CloudFront Gets Content From

| Field                             | Description                           | Recommended Setting                                    |
| --------------------------------- | ------------------------------------- | ------------------------------------------------------ |
| **Origin domain**                 | The source (S3, ELB, EC2, etc.)       | S3 → use `s3.amazonaws.com` URL ELB → use ELB DNS name |
| **Origin path**                   | Optional path prefix                  | Only use if files are in a subfolder (e.g., `/public`) |
| **Origin ID**                     | Internal name for the origin          | Leave default or name meaningfully                     |
| **Origin type**                   | S3 or Custom                          | Choose based on the source                             |
| **Origin protocol policy**        | How CloudFront connects to the origin | `HTTPS only` (recommended) or `Match viewer`           |
| **Connection attempts / timeout** | Retry settings to origin              | Default is fine unless your origin is slow             |

------

### 2. **Default Cache Behavior**

This controls **how CloudFront handles requests**.

| Setting                     | Description                          | Static Site                                | API/Dynamic App                                |
| --------------------------- | ------------------------------------ | ------------------------------------------ | ---------------------------------------------- |
| **Viewer protocol policy**  | Enforce HTTPS?                       | `Redirect HTTP to HTTPS` ✅                 | Same                                           |
| **Allowed HTTP methods**    | Which methods are allowed            | `GET, HEAD`                                | `GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE` |
| **Cache policy**            | Controls what CloudFront caches      | `CachingOptimized`                         | `CachingDisabled`                              |
| **Origin request policy**   | Controls what info is sent to origin | `AllViewer` for dynamic `None` for static  | `AllViewer` or custom for APIs                 |
| **Response headers policy** | Set HTTP headers returned to browser | Use default or add `CORS` headers for APIs |                                                |

------

### 3. **Functionality Enhancements**

| Setting                                | Description          | Recommendation                            |
| -------------------------------------- | -------------------- | ----------------------------------------- |
| **Compress objects automatically**     | Use Gzip/Brotli      | ✅ Always enable for text assets           |
| **Smooth streaming**                   | For media (HLS/DASH) | Only enable for streaming                 |
| **Lambda@Edge / CloudFront Functions** | Run JS at the edge   | Use only if needed (e.g., redirect, auth) |

------

### 4. **Distribution Settings**

| Field                               | Description                               | Recommendation                                               |
| ----------------------------------- | ----------------------------------------- | ------------------------------------------------------------ |
| **Price class**                     | Which edge locations are used             | `Price Class 100` (cheapest, US/Europe only) `Price Class All` (global) |
| **Alternate domain names (CNAMEs)** | Use a custom domain like `www.mysite.com` | Use if you have a custom domain                              |
| **SSL certificate**                 | For HTTPS with custom domain              | Use AWS ACM to request/upload cert                           |
| **Default root object**             | File served at `/`                        | `index.html` (for static sites)                              |
| **Logging**                         | Enable CloudFront logs                    | ✅ Enable if you want analytics                               |
| **IPv6**                            | Support IPv6 requests                     | ✅ Leave enabled                                              |
| **Web Application Firewall (WAF)**  | Protect against attacks                   | Enable if needed                                             |
| **Origin access control (OAC)**     | Secure S3 bucket access                   | ✅ Use OAC instead of making bucket public                    |

------

## 🧠 Recommended Settings by Use Case

### 🗂️ Static Website (S3)

- Origin: S3 bucket
- Viewer protocol: `Redirect HTTP to HTTPS`
- Allowed methods: `GET, HEAD`
- Cache policy: `CachingOptimized`
- Default root object: `index.html`
- Enable **OAC** for private bucket access
- No need for Lambda@Edge

------

### 🔧 Dynamic Web App (EC2 or ALB)

- Origin: ELB or EC2 public endpoint
- Viewer protocol: `Redirect HTTP to HTTPS`
- Allowed methods: `All`
- Cache policy: `CachingDisabled` (or cache selective APIs only)
- Enable WAF if exposed to public

------

### 🔌 REST API or GraphQL

- Origin: API endpoint or ALB
- Allowed methods: `GET, POST, PUT, etc.`
- Cache policy: `CachingDisabled` (or custom if some endpoints cacheable)
- Enable CORS and Caching headers manually
- Use CloudFront Functions or Lambda@Edge for:
  - Auth tokens
  - IP filtering
  - Geo restrictions



对于Unicast IP和Anycast IP：

Unicast IP,一个服务器只有一个ip

AnyCast IP,某个服务的多台服务器都是相同的IP。用户请求连接时，会被导向最近的服务器。

##  什么是 AWS Global Accelerator（全球加速器）？

**AWS Global Accelerator** 是一个用于优化网络连接的服务，它通过 **Amazon 的全球专有网络骨干**，为来自世界各地的用户提供更快、更稳定的访问路径到你的应用（如 EC2、ALB、NLB 等）。

> 💡 它不是 CDN，而是一个**全球流量调度 + 低延迟优化服务**。

------

## 🧠 它的核心作用：

Global Accelerator 能为你的应用提供：

- **两个静态 IP 地址（Anycast 类型）**
- 用户会自动连接到最近的 AWS 边缘节点
- 然后通过 **AWS 内部高速网络** 路由到你的后端服务

------

## 🔧 它能解决哪些问题？

| 问题                          | Global Accelerator 的解决方式    |
| ----------------------------- | -------------------------------- |
| 用户离部署区域较远            | 通过 AWS 全球网络加速访问        |
| 公网不稳定、延迟波动大        | 走 AWS 专线，提供稳定低延迟连接  |
| 多区域部署 + 需要故障自动切换 | 支持健康检查和智能路由到可用区域 |
| 需要固定 IP 地址供白名单使用  | 提供全球固定 IP                  |

------

## 📦 常见的使用场景

| 场景                 | 说明                               |
| -------------------- | ---------------------------------- |
| 全球游戏、应用服务器 | EC2 或 ALB 多区域部署              |
| 低延迟 API           | 如金融交易系统、直播服务           |
| 客户需要固定 IP 通信 | 固定 IP 可用于防火墙规则或审计需求 |
| 多区域高可用性应用   | 自动切换至健康区域                 |

------

## 📊 Global Accelerator 与 CloudFront 的区别

| 特性             | AWS Global Accelerator             | AWS CloudFront                   |
| ---------------- | ---------------------------------- | -------------------------------- |
| **主要用途**     | 优化应用性能、实现全球高可用       | 加速静态资源（CDN）              |
| **支持缓存？**   | ❌ 不缓存任何内容                   | ✅ 支持缓存（HTML、图片、视频等） |
| **适用于**       | ALB、NLB、EC2、EIP                 | S3、API Gateway、EC2、静态站点等 |
| **IP 地址**      | ✅ 提供 2 个静态 IP（Anycast）      | ❌ 使用 CloudFront 子域名         |
| **自动容灾切换** | ✅ 内建多区域故障转移               | ❌ 需要自行配置                   |
| **可编程/扩展**  | 基于网络路由和健康检查，不运行函数 | 可配合 Lambda@Edge 执行边缘逻辑  |
| **适合场景**     | 动态应用、API、多区域部署          | 静态资源分发、大量用户访问优化   |

------

## 🛠️ 工作方式（内部流程）

1. 创建 Global Accelerator
2. 系统分配两个全球可路由的静态 IP（Anycast）
3. 配置一个或多个 **终端组**（比如：us-east-1 的 ALB、eu-west-1 的 NLB）
4. 用户从最近的边缘节点发起连接
5. 流量通过 AWS 内部高速网络传递到最佳的终端节点（健康检查支持）

------

## 🚀 示例场景：全球游戏服务器

- 在美国、欧洲、日本部署游戏服务器
- 使用 Global Accelerator：
  - 所有玩家使用 **统一的 IP 地址** 连接游戏
  - 自动连接到最近、最健康的服务器区域
  - 如果某个区域宕机，自动切换到备份区域





## 核心一句话总结：

> 🔁 **CDN（如 CloudFront）** 是用于**缓存和分发内容**的，
>  🚀 **Global Accelerator** 是用于**优化动态流量的路由**到你的应用后端。

它们都能提升全球访问速度，但**工作原理、使用场景和目的完全不同**。

------

## 📊 全面对比：CloudFront（CDN） vs Global Accelerator

| 对比项                 | CloudFront（CDN）                        | Global Accelerator（全球加速器）      |
| ---------------------- | ---------------------------------------- | ------------------------------------- |
| **主要用途**           | 分发静态/动态内容，加快加载速度          | 优化应用访问路径，降低延迟            |
| **核心功能**           | 内容分发与缓存                           | 路由优化与健康检查                    |
| **是否缓存内容**       | ✅ 是（HTML、JS、图片、视频等）           | ❌ 否                                  |
| **适配服务类型**       | S3、ALB、EC2、API Gateway 等             | ALB、NLB、EC2、EIP、自定义后端        |
| **边缘节点的作用**     | 存储/缓存内容，并响应请求                | 仅做流量接入点，然后转发到终端        |
| **网络路径**           | 用户 → 边缘节点 →（缓存/源站）           | 用户 → 最近边缘 → AWS 私网 → 应用终端 |
| **支持协议**           | 仅 HTTP/HTTPS                            | 所有 TCP / UDP 协议                   |
| **是否提供固定 IP**    | ❌ 否                                     | ✅ 是（2 个 Anycast 静态 IP）          |
| **支持多区域容灾切换** | ❌ 需要手动配置（如 Route 53 + 健康检查） | ✅ 内置自动故障转移                    |
| **是否支持地理路由**   | ✅ 有（通过边缘选择）                     | ✅ 有（基于边缘位置路由到最健康区域）  |
| **TLS 终止**           | ✅ 支持（可在边缘终止）                   | ✅ 支持（配合 ALB/NLB）                |
| **适用场景**           | 静态站点、媒体内容、缓存接口、Web 应用   | 游戏、低延迟 API、多区域高可用服务    |
| **计费模式**           | 按请求量 + 数据传输计费                  | 固定小时费 + 流量费                   |

------

## 🔍 技术细节对比

### 1. 🔂 数据流向路径

#### CloudFront：

```
text


CopyEdit
用户 → 最近的边缘节点 → 缓存命中或源站（S3、ALB 等）
```

- 支持缓存，减少源站压力
- 可选择是否强制通过 AWS 内部网络（但不是默认）

#### Global Accelerator：

```
text


CopyEdit
用户 → 最近 AWS 全球边缘 → AWS 私网 → 应用终端（ALB、NLB、EC2）
```

- 所有数据都经过 AWS 高速专用网络
- 无缓存，只做智能路由和加速

------

### 2. 🔁 缓存能力对比

| 功能          | CloudFront | Global Accelerator |
| ------------- | ---------- | ------------------ |
| 缓存静态资源  | ✅ 支持     | ❌ 不支持           |
| 缓存 API 响应 | ✅ 可选     | ❌ 不支持           |
| 减少后端负载  | ✅ 是       | ❌ 否               |

------

### 3. 🌍 全球分发策略

| 功能               | CloudFront                           | Global Accelerator             |
| ------------------ | ------------------------------------ | ------------------------------ |
| 内容复制到全球节点 | ✅ 支持                               | ❌ 不复制，仅做传输             |
| 自动区域故障转移   | ❌ 不支持（需配合 Route 53 手动设置） | ✅ 内置健康检查+区域切换        |
| 最适合             | 内容访问多、读多写少的网页类业务     | 实时性强、交互密集的低延迟业务 |

------

## 🎮 游戏场景例子

你开发了一个全球在线游戏，部署了美国、欧洲、日本三个区域的游戏服务器。

- ✅ 使用 CloudFront：
  - 分发游戏网站、下载器、补丁说明（HTML/JS/图像等）
- ✅ 使用 Global Accelerator：
  - 玩家进入游戏时，连接最近的游戏服务器（低延迟）
  - 如果某区服务器故障，自动切换到健康区域

------

## 🧠 Web + API 场景例子

| 模块                     | 推荐使用服务                 |
| ------------------------ | ---------------------------- |
| 静态网页                 | CloudFront + S3              |
| 静态资源（CSS/JS/图片）  | CloudFront                   |
| REST API 接口            | CloudFront（可配缓存）       |
| 实时通信、登录验证服务器 | Global Accelerator（低延迟） |

------

## 🤝 能同时使用吗？

✅ 完全可以。实际生产中，经常会同时使用 CloudFront 和 Global Accelerator：

| 使用 CloudFront 的部分 | 使用 Global Accelerator 的部分 |
| ---------------------- | ------------------------------ |
| 网站、媒体、静态文件   | 游戏/通信/API 实时请求         |
| 图片和页面缓存         | 聊天、语音、实时交互逻辑       |
| 新闻动态、文档         | 登录服务、状态同步             |