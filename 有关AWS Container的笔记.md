### 有关AWS Container的笔记



在AWS中，容器技术是支持现代云原生应用的核心组件，主要涉及以下服务：**Amazon ECS**（Elastic Container Service）、**Amazon EKS**（Elastic Kubernetes Service）、**AWS Fargate**（无服务器容器平台）和**ECR**（Elastic Container Registry）

------

### **一、容器在AWS中的作用**

1. **应用隔离与标准化**
   - 通过容器镜像（Docker等）将应用与其依赖项打包，确保环境一致性，避免“开发环境正常，生产环境报错”的问题(一个包，可以应付多个系统环境)。
2. **快速部署与扩展**
   - 容器启动速度快，结合AWS Auto Scaling和负载均衡（如ALB），可快速横向扩展应对流量高峰。
3. **微服务架构支持**
   - 将单体应用拆分为多个独立容器（如前端、后端、数据库），每个服务可独立开发、部署和扩展。
4. **资源高效利用**
   - 与传统虚拟机相比，容器共享宿主机内核，资源消耗更低，适合高密度部署。
5. **无服务器化（Fargate）**
   - 无需管理底层EC2实例，专注于应用逻辑，降低运维复杂度。

------

### **二、核心AWS容器服务及使用场景**

#### 1. **Amazon ECS**

- **特点**：AWS自研的容器编排服务，深度集成AWS生态（如IAM、VPC）。有两种ECS集群类型，分别是EC2 Launch Type和Fargate Launch Type两种。EC2类型需要你提前设置物理硬件设施，如CPU数，RAM大小，SSD容量，网络等。且此架构日后也需要你维护。而Fargate类型不需要对硬件方面进行设置和维护，你只需要把你打包好的程序放入其中，运行便可。如果需要增加ECS里service和task的数量，可以使用ECS Service Auto Scaling来自动管理。



另外，在部署完service或task后，你需要对你的service或task授权（IAM Role），如果你使用EC2 launch type，那么，你需要EC2 Instance Profile，其会被你EC2 instance里的ECS Agent所使用，然后可以通过此授权，向其他ECS服务发起API请求，如像ECR，CloudWatch等。

![image-20250409093928394](C:\Users\msduser\Desktop\学习笔记\assets\image-20250409093928394-1744159173151-1.png)



- ECS**使用场景**：
  - 快速部署Web应用（如Node.js/Python后端）。
  - 与AWS CodePipeline结合实现CI/CD流水线。
  - 运行定时批处理任务（如数据清洗）。
  - 通过设置ASG实现自动扩容

如果你的Task或者Service需要共享文件或数据，可以使用EFS将其连接，此种做法对Fargate 类型的容器托管服务最为有效，因为Fargate类型是无服务器类型的服务，你不需要管理有关服务或Task运行所在的硬件和软件底层。而且其没有默认的SSD卷加载，所以在设置方面会简单一点。而EC2 instance type，因为其有自己的默认存储卷，所以在装载后，需要手动设定存储路径。



有关ECS服务 和ASG联动的一个示例架构

![image-20250409105059346](C:\Users\msduser\Desktop\学习笔记\assets\image-20250409105059346-1744163460961-3.png)

#### 2. **Amazon EKS**

- **特点**：全托管Kubernetes服务，有称K8s，开源，支持混合云和多集群管理。
- **使用场景**：
  - 复杂微服务架构（如需要Istio服务网格）。
  - 跨云/本地部署（通过EKS Anywhere）。
  - 机器学习模型部署（如TF Serving或TorchServe容器化）。
  - 支持EC2和AWS Fargate
  - 在多种云环境下都通用。



请注意Kubernetes比你想象中要难许多，有很多底层的细节需要另外学习。EKS的Node有三种类型。Managed Node Group,Self-Managed Nodes,AWS Fargate。

Managed Node group：是建立在EC2 instance上的，和ECS的EC2 instance Type差不多。其自动管理下面的节点，且可以通过ASG设置自由缩放处理能力。也支持便宜的EC2 instance，如on-demand或 spot instance.

Self-Managed Nodes:你自己创建Node，然后把他放入到EKS cluster里，然后同样由ASG管理node的增减。

AWS Fargate：和ECS的完全相同。

和ECS不同的是，EKS可以支持多种额外的存储卷装载，如EBS，EFS，FSx for Lustre，FSx for NetApp ONTAP

#### 3. **AWS Fargate**

- **特点**：无服务器容器计算引擎，按任务资源消耗计费。
- **使用场景**：
  - 突发性工作负载（如活动促销期间的临时扩容）。
  - 不想管理EC2集群的小型团队。

#### 4. **Amazon ECR**

- **作用**：私有Docker镜像仓库，支持镜像扫描（安全漏洞检测）。



和Github差不多，主要作用都是存储你建立好的Image文件，并进行版本管理等。但ECR和ECS服务高度绑定，所以，在ECS部署的service或task需要在ECR中存在。所以，你需要在docker上把你想要用的程序打包好，设置好标签，然后push到ECR上去。目前没有GUI可以达成打包和push到ECR上，所以，需要使用命令行来完成。或下载docker desktop。

#### 5. **其他集成**

- **Lambda容器**：将容器镜像作为Lambda函数部署（支持自定义运行时）。
- **App Runner**：全托管容器化Web应用发布服务（简化前端部署）。



| Platform | Launch Type | Uses ASG? | How?                              |
| -------- | ----------- | --------- | --------------------------------- |
| ECS      | EC2         | ✅ Yes     | ASG backs ECS Capacity Provider   |
| ECS      | Fargate     | ❌ No      | Serverless — scale tasks directly |
| EKS      | EC2         | ✅ Yes     | ASG manages worker node scaling   |
| EKS      | Fargate     | ❌ No      | Serverless pods — no ASG needed   |

------

### **三、AWS容器的局限性（极限）**

1. **资源限制**
   - 单容器资源上限：ECS/Fargate单任务最高支持4 vCPU和30GB内存（需根据实例类型调整）。
   - EKS节点组受限于EC2实例规格（如c5.4xlarge最大支持16 vCPU）。
2. **冷启动延迟**
   - Fargate任务首次启动可能需要10-30秒（镜像拉取、资源分配）。
   - Lambda容器冷启动在低频率调用时更明显。
3. **网络性能**
   - 容器间通信依赖AWS VPC网络，高吞吐场景（如视频处理）需优化ENI配置。
4. **存储限制**
   - 默认临时存储（如ECS的20GB）可能不足，需挂载EFS或EBS卷。
5. **成本考量**
   - Fargate按vCPU/内存计费，长期运行的大规模集群可能比自管EC2成本更高。
6. **安全复杂性**
   - 容器镜像漏洞（需依赖ECR扫描或第三方工具）。
   - IAM角色细粒度权限配置需要学习成本。

------

### **四、最佳实践与优化建议**

1. **镜像优化**
   - 使用多阶段构建减小镜像体积（如从1GB压缩到200MB）。
2. **混合部署策略**
   - 关键服务用Fargate（无服务器），高负载稳定服务用ECS+EC2（降低成本）。
3. **监控与日志**
   - 使用CloudWatch Container Insights和X-Ray跟踪性能瓶颈。
4. **安全加固**
   - 最小化容器权限（如ECS Task Role仅授权必要S3访问）。
   - 启用ECR镜像扫描并设置自动更新策略。

------

### **五、典型场景示例**

- **电商大促**：用ECS+Fargate自动扩展订单处理服务，峰值后自动缩容。
- **AI推理**：在EKS中部署GPU加速的TensorFlow容器，通过HPA（Horizontal Pod Autoscaler）动态调整Pod数量。
- **混合云**：通过EKS Anywhere在本地数据中心运行Kubernetes，与AWS云无缝同步。



# ECS + Docker + Python：完整操作流程中文总结

------

## 📦 1. **编写 Python Flask 应用**

```
pythonCopyEditfrom flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/')
def home():
    return "✅ Hello from ECS!"

@app.route('/echo')
def echo():
    msg = request.args.get('msg', 'no message')
    return jsonify({'received': msg})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)
```

------

## 📄 2. 创建必须的文件

### `requirements.txt`

这个requirements.txt是让系统知道你的程序需要什么库，或者框架来保持正常运作。当你开始建立某个程序的容器（build）时，os会依据此文件来安装环境。

```
nginx


CopyEdit
flask
```

### `Dockerfile`

这个dockerfile里面有一些命令，当你将做好的程序打包时，docker会执行这些命令，用来完成该程序包的建立。当然，这些命令并不是固定的，但基本的命令有指定你这个程序需要什么编译器来执行，需要从当前的环境里拷贝什么文件，安装什么框架和库，设定端口（如果需要的话），转换用户等。

```
dockerfileCopyEditFROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
EXPOSE 80
CMD ["python", "app.py"]
```

------

## 🐳 3. 在 EC2 (Ubuntu) 上构建 Docker 镜像

```
bash


CopyEdit
docker build -t ecs-flask-app .
```

### 🔍 常见警告（可以忽略）：

```
sql


CopyEdit
Running pip as the 'root' user can result in broken permissions...
```

------

## 🗂️ 4. 本地测试 Docker 镜像

```
bashCopyEditdocker images
docker run -p 8080:80 ecs-flask-app
```

✅ 然后访问：`http://<EC2 公网 IP>:8080`

------

## 🔐 5. 登录 Amazon ECR

```
bashCopyEditaws ecr get-login-password --region ap-northeast-1 \
| docker login --username AWS --password-stdin <你的账户 ID>.dkr.ecr.ap-northeast-1.amazonaws.com
```

> 💡 `docker login` 是上传到 ECR 前必须做的操作，登录有效期约为 12 小时。

------

## 🏷️ 6. 给 Docker 镜像打标签

```
bash


CopyEdit
docker tag ecs-flask-app:latest <你的 ECR 镜像地址>:latest
```

有一点需要注意，如果你想把封装好的程序都放入同一个reponsitory中，则不需要每次都对reponsitory打标签。之后，需要对封装好的程序打标签。例子如下：

docker tag flask-ip-app:latest 975049994847.dkr.ecr.ap-northeast-1.amazonaws.com/ecs-flask-app:ip-test

ecs-flask-app是你的reponsitory,ip-test是你的程序的tag

例如：

```
bash


CopyEdit
docker tag ecs-flask-app:latest 123456789012.dkr.ecr.ap-northeast-1.amazonaws.com/ecs-flask-app:latest
```

------

## 🚀 7. 上传镜像到 Amazon ECR

```
bash


CopyEdit
docker push 123456789012.dkr.ecr.ap-northeast-1.amazonaws.com/ecs-flask-app:latest
```

------

## 🛠️ 8. 创建 ECS 集群时遇到问题

### ❌ 错误 1：

```
pgsql


CopyEdit
Unable to assume the service linked role.
```

### ✅ 解决方法：

手动创建 ECS 所需的 IAM 服务关联角色：

```
bash


CopyEdit
aws iam create-service-linked-role --aws-service-name ecs.amazonaws.com
```

------

### ❌ 错误 2：

```
arduino


CopyEdit
CloudFormation stack already exists...
```

### ✅ 解决方法：

前往 **CloudFormation 控制台**：

- 找到名为 `Infra-ECS-Cluster-...` 的失败堆栈
- 状态为 `ROLLBACK_COMPLETE`
- 删除它，或者创建一个新名称的集群即可

------

## 💸 9. 关于费用的提醒

| 项目                    | 空置时是否收费？        |
| ----------------------- | ----------------------- |
| ECS 集群（空的）        | ❌ 免费                  |
| Fargate 任务（运行中）  | ✅ 会收费（按秒计费）    |
| 应用型负载均衡器（ALB） | ✅ 会收费（按小时+流量） |
| ECR 镜像存储            | ✅ 每 GB 每月微量费用    |
| 未使用的 Elastic IP     | ✅ 会收费                |



在包装程序时，出现了这样的警告：

WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv 



### **警告的本质：系统洁癖患者的唠叨**

这个警告是 pip 在提醒你："别用 root 大号随便装东西啊！万一和系统自带的包打架怎么办？"

- **普通电脑**：确实可能污染系统环境（比如把/usr/lib/python3.8/里的包覆盖了）
- **容器里**：整个环境都是你的沙盒，装完就跑路，其实问题不大 😎

------

### **二、在容器中到底有没有风险？**

#### **1. 短期无害：能跑就行**

- 你的程序如果能正常运行，这个警告可以暂时无视（就当没看见）。
- 容器本身是独立环境，不会影响宿主机的系统包。

#### **2. 长期隐患：安全强迫症预警**

- 如果容器运行时**以 root 身份运行应用进程**，被攻击时黑客会获得 root 权限（危险！）
- 某些 Python 包如果和系统包冲突，可能导致容器内部依赖混乱（但概率低）。

------

### **三、推荐修复方案（3种）**

#### **方案1：最简单粗暴——直接屏蔽警告**

在 Dockerfile 安装命令前加一行：

```
ENV PIP_ROOT_USER_ACTION=ignore  # 魔法屏蔽键！
RUN pip install -r requirements.txt
```

**效果**：世界安静了，但没解决本质问题（适合测试环境）。

------

#### **方案2：最佳实践——创建专属用户（推荐🌟）**

在 Dockerfile 里添加「用户管理」步骤：

```
# 前半段用root安装依赖
RUN apt-get update && apt-get install -y some-packages...
RUN pip install -r requirements.txt

# 后半段创建普通用户并切换
RUN useradd -m myuser  # 创建用户
USER myuser  # 从此切到普通用户
WORKDIR /home/myuser  # 切换工作目录

CMD ["python", "app.py"]  # 用普通用户运行程序
```

**好处**：安全！即使被入侵，攻击者权限也被限制。

------

#### **方案3：折中方案——虚拟环境**



```
# 在容器内创建虚拟环境
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"
RUN pip install -r requirements.txt
```

**适用场景**：既要root装系统包，又要隔离Python环境（适合复杂依赖项目）。

------

### **四、你的实际选择建议**

- **如果只是临时测试** → 选方案1（加魔法屏蔽）
- **要正式部署** → 必选方案2（安全第一！代码示例可直接复制）
- **依赖系统级包（如需要apt-get）** → 方案2+方案3组合使用

------

### **五、真实案例演示**

假设你的 Dockerfile 原本长这样：

```
FROM python:3.8
COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt  # 这里触发警告
CMD ["python", "app.py"]
```

**改造后（方案2）**：

```
FROM python:3.8
COPY . /app

# 安装阶段用root
RUN pip install -r requirements.txt

# 创建用户并切换
RUN useradd -m myuser && chown -R myuser /app
USER myuser
WORKDIR /app

CMD ["python", "app.py"]
```



#### 关于ECS 里边的Task Definition



#  ECS 任务定义：它到底做了什么？

✅ 你的理解非常正确：
 **任务定义（Task Definition）** 告诉 AWS 如何部署和运行你的容器化应用，包括：

- 💻 **硬件配置**（CPU、内存）
- 🌐 **网络模式**
- 🔐 **安全与权限**（IAM 角色）
- 📦 **容器细节**（镜像、端口、运行命令）
- ⚙️ 一些高级运行时配置（日志、限制、环境变量等）

------

# 👥 Task Role 与 Task Execution Role 的区别

| 角色类型                                | 用途                                          | 是否必须？ | 举例说明                                 |
| --------------------------------------- | --------------------------------------------- | ---------- | ---------------------------------------- |
| **Task Execution Role**（任务执行角色） | 允许 ECS 拉取 ECR 镜像、发送日志到 CloudWatch | ✅ 必须     | AWS 代表任务执行操作                     |
| **Task Role**（任务角色）               | 给容器中的应用访问 AWS 服务的权限             | ❌ 可选     | 比如从 S3 下载文件，或访问 DynamoDB、SQS |

### 🧠 你可以这样理解：

- **执行角色**：AWS 替你做初始化工作（拉镜像、写日志）
- **任务角色**：容器内的程序访问 AWS 资源（S3 等）

✅ 你当前的 Flask 应用，只需要 `ecsTaskExecutionRole` 就可以了。

------

# ⚙️ ECS 高级任务定义设置（可选配置解释）

你在控制台看到的这些设置，有很多都是可选项，下面是逐条解释：

------

## 🔄 启动依赖顺序（Startup Dependency Ordering）

> 当你在一个任务中定义了多个容器时，可以控制它们的**启动顺序**。

举例：

- 数据库先启动 → 后端容器启动 → 前端容器最后启动

🧠 如果你只有一个容器，就不需要设置这个。

------

## ⏱️ 容器超时时间（Container Timeouts）

> 控制容器在以下情况下等待的时间：

- 启动：超时视为失败
- 停止：超时后强制终止容器

默认配置已经适合大多数应用。

------

## 🌐 容器网络设置（Container Network Settings）

> 设置容器的网络行为，比如是否独立 IP、共享 DNS

- **Fargate** 默认使用 `awsvpc`，每个任务都有自己的 IP
- **EC2** 还可以选择 `bridge`、`host` 等模式

✅ 一般使用默认设置即可。

------

## 🐳 Docker 配置（Docker Configuration）

> 设置容器的底层 Docker 行为，比如：

- 日志驱动
- 是否使用 init 进程
- 是否是特权模式（privileged）

这些设置比较高级，对简单 Web 应用不需要。

------

## 🚫 资源限制（Ulimits）

> 控制 Linux 系统层面的限制，比如打开文件数、进程数等

只有在高并发、大计算任务中才可能需要，一般 Web 应用可以忽略。

------

## 🏷️ Docker 标签（Docker Labels）

> 给容器打上标签（key-value 格式），供监控、自动化工具识别

不是必须项，除非你做 DevOps 或微服务监控。

------

# 🔁 任务定义版本（Versioning）

✅ 是的！ECS 会为每一次任务定义变更自动生成版本号：

例如：

```
makefileCopyEditflask-task:1
flask-task:2
flask-task:3
```

你可以选择某个具体版本部署，也可以自动使用最新版本。

### 🧠 这样做的好处：

- 可以快速**回滚旧版本**
- 服务可以自动更新
- 可以追踪每次部署的历史记录

------

## ✅ 总结表格

| 功能                | 作用                  | 当前是否需要？       |
| ------------------- | --------------------- | -------------------- |
| Task Execution Role | ECS 拉镜像/写日志所需 | ✅ 必须               |
| Task Role           | 容器内程序访问 AWS    | ❌ 一般用不到         |
| 启动顺序            | 多容器控制启动顺序    | ❌ 你只有一个容器     |
| 超时时间            | 启动/停止等待时限     | ❌ 保留默认即可       |
| 网络配置            | IP、DNS 等控制        | ✅ 使用默认即可       |
| Docker 设置         | 日志、特权等          | ❌ 不建议动           |
| Ulimits             | Linux 资源限制        | ❌ 暂时用不到         |
| Docker 标签         | 元数据标记            | ❌ 非常可选           |
| 版本控制            | 自动版本管理          | ✅ 非常有用，默认启用 |



##### 对于ECS cluster的创建设置的解析（部分）

1.Service Connect: 允许服务和服务之间的通讯（假设你在ECS上部署了两个或以上的服务，而这几个服务之间的工作有关联性，则可以启用这个设定）

2.Service Discovery: 给你的服务一个特定的DNS域名（如my-api.local），这样在VPC内网的其他服务可以发现此服务，并直接与其连接，而不用通过IP地址。

3.VPC Lattice:这个设置只有在你的服务需要和其他VPC网络，其他AWS区域，其他账号内的其他服务进行连接（此服务不是指AWS的服务，而是你在ECS里的服务，同上）

4.Loading Balancing:把你在ECS运行的某个服务连接到ALB或NLB中。

另外：在ECR中，每个你push的包（image）是没有名字的，即便你在创建这个image的过程中，会创建一个名字，但这个名字在docker成功将文件包build成功后，就不再具有实用性。而真正在ECR和后续创建ECS service中起标识作用的是tag和包的version(latest的那个)，所以，在创建包的过程中，必须保证tag的唯一性。否则会出错。而且，在ECR的responsitory中，比较重要的还有一个更新时间，可以用这个时间来衡量micro-service的迭代。





什么是微服务（Microservice）：

------

# ❓ 什么是微服务？

> **微服务 ≠ 小代码量**
>  **微服务 ≠ 小型部署**

微服务的定义，关键在于它的**职责清晰**与**独立性**，而不是它的体积大小或部署位置。

------

## 🔑 微服务的核心特征是：职责划分清晰、独立部署、边界明确

### ✅ 一个“微服务”通常具备以下特点：

1. **聚焦于一个具体的业务功能**
2. **可以单独部署**
3. **与其他服务松耦合**
4. **拥有自己的数据和逻辑**
5. **可以独立扩展（scale）**

------

## 🔍 什么**不是**微服务的定义标准？

| 常见误解                   | 为什么不正确                               |
| -------------------------- | ------------------------------------------ |
| “代码量必须很少”           | ❌ 微服务的代码可以多达几十MB甚至更多       |
| “必须部署在单独的服务器上” | ❌ 它可以和其他服务部署在同一个节点上       |
| “必须对外提供服务”         | ❌ 它可能只是内部服务，不公开访问           |
| “必须用容器部署”           | ❌ 容器是一种**部署方式**，不是微服务的定义 |

------

## 📦 实际例子（电商平台）

假设你正在开发一个电商系统：

| 服务                            | 是微服务吗？ | 理由                                           |
| ------------------------------- | ------------ | ---------------------------------------------- |
| `order-service`（订单服务）     | ✅ 是         | 只处理订单生成、支付触发                       |
| `auth-service`（认证服务）      | ✅ 是         | 只处理登录、令牌验证、权限控制                 |
| `inventory-service`（库存服务） | ✅ 是         | 管理商品库存，与仓库同步                       |
| `frontend`（前端）              | ❌ 不一定是   | 如果与后端紧耦合，可能只是一个整体应用的一部分 |

每个微服务：

- 可以单独部署
- 职责非常清晰
- 不需要了解其他服务的内部工作方式

------

## 📏 微服务应该有多“小”？

并没有什么规定比如“微服务代码必须少于 1000 行”。
 但许多团队遵循这个**经验法则**：

> ✅ **如果你能用一句话（不使用“并且”）清楚描述服务的职责，那它的粒度就是合适的。**

比如：

- ✔️ “这个服务负责处理发票。”
- ❌ “这个服务处理订单**并且**发票**并且**支付。”

------

## 🌐 那么是否“部署在公网”才算是微服务？

> ❌ 并不是。

一些微服务：

- 仅在 **内网或 VPC 中运行**
- 通过 **API Gateway** 或负载均衡公开部分接口
- 只供 **其他服务调用**，不对用户开放

------

## ✅ 总结：什么才是真正的微服务？

| 特征                           | 是否必要？         |
| ------------------------------ | ------------------ |
| 职责单一，专注一个功能         | ✅ 必须             |
| 能够独立部署                   | ✅ 必须             |
| 与其他服务松耦合               | ✅ 必须             |
| 拥有自己的数据（数据库或缓存） | ✅ 推荐             |
| 使用容器或独立应用运行         | ✅ 常见，但不是必须 |
| 在公网可以访问                 | ❌ 不必要           |
| 体积小于几 MB                  | ❌ 与体积无关       |