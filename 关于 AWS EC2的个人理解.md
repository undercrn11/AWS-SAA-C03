##### 关于 AWS EC2（Elastic Compute Cloud）弹性计算云的个人理解

EC2是AWS最常被使用的service之一。

EC2（弹性计算云）的主要功能如下：

1.租赁虚拟机

2.在虚拟硬盘中存储数据（云存储）

3.部署负载均衡机器

4.控制部署在其上的应用，服务的流量大小

我们需要至少一台虚拟机以使用EC2的服务。

EC2 Instance：

EC2 instance是在AWS上运行的一台虚拟机。它用于运行各种各样的程序，软件。

所以在AWS上部署EC2 instance有很多参数可以去设置。包括但不限于：

1.OS 2.CPU数量   3.RAM大小   4.储存空间（存储在EC2自身的硬盘还是在云端）

5.网卡（网络速度),ip设置 6.防火墙(是否允许外部访问，是否允许AWS 内部服务访问外部连接等)      6.Bootstrap script（在虚拟机第一次启动时，执行的初始化程序），也叫EC2 User Data。7.还有很多其他的详细细节。

EC2 User Data主要用于虚拟机的初始化。其作用一般为安装所需软件或文件，更新程序（如果需要的话），设定系统环境。

EC2 instance 有很多种，以下是具体信号的解析：

| **Instance Type** | **Category**      | **Processor** | **Key Features**                     |
| ----------------- | ----------------- | ------------- | ------------------------------------ |
| **t4g**           | General Purpose   | Graviton      | Burstable CPU, Cost-efficient        |
| **t3**            | General Purpose   | Intel         | Burstable CPU, Cost-efficient        |
| **t3a**           | General Purpose   | AMD           | Burstable CPU, Lower cost than t3    |
| **m7g**           | General Purpose   | Graviton      | Balanced CPU, memory, and networking |
| **m6i**           | General Purpose   | Intel         | Balanced CPU, memory, and networking |
| **m6g**           | General Purpose   | Graviton      | Balanced CPU, memory, and networking |
| **m5**            | General Purpose   | Intel         | Balanced CPU, memory, and networking |
| **m5a**           | General Purpose   | AMD           | Balanced CPU, lower cost than m5     |
| **m5n**           | General Purpose   | Intel         | Enhanced networking                  |
| **c7i**           | Compute Optimized | Intel         | High-performance CPU                 |
| **c6i**           | Compute Optimized | Intel         | High-performance CPU                 |
| **c6g**           | Compute Optimized | Graviton      | High-performance CPU, lower cost     |
| **c5n**           | Compute Optimized | Intel         | High networking bandwidth            |
| **r7g**           | Memory Optimized  | Graviton      | High memory for in-memory databases  |
| **r6i**           | Memory Optimized  | Intel         | High memory for in-memory databases  |
| **r6g**           | Memory Optimized  | Graviton      | High memory for in-memory databases  |
| **r5**            | Memory Optimized  | Intel         | High memory for relational databases |
| **r5n**           | Memory Optimized  | Intel         | High memory and networking           |
| **x2idn**         | Memory Optimized  | Intel         | Extreme memory workloads             |
| **i4i**           | Storage Optimized | Intel         | High-speed NVMe SSD storage          |
| **i3en**          | Storage Optimized | Intel         | High-speed NVMe SSD storage          |
| **d3**            | Storage Optimized | Intel         | High HDD storage capacity            |
| **d3en**          | Storage Optimized | Intel         | High storage throughput              |
| **h1**            | Storage Optimized | Intel         | HDD-based storage for big data       |
| **p4d**           | GPU Optimized     | NVIDIA A100   | Deep learning, AI workloads          |
| **p3**            | GPU Optimized     | NVIDIA V100   | Deep learning, AI workloads          |
| **g5**            | GPU Optimized     | NVIDIA A10G   | Machine learning, gaming             |
| **g4dn**          | GPU Optimized     | NVIDIA T4     | Machine learning, gaming             |
| **f1**            | FPGA Optimized    | Custom FPGA   | Hardware acceleration                |
| **hpc6id**        | HPC Optimized     | Intel         | High-performance computing           |
| **hpc7g**         | HPC Optimized     | Graviton      | High-performance computing           |
| **z1d**           | High Frequency    | Intel         | High clock speed, databases          |
| **u-6tb1**        | Ultra Memory      | Intel         | Up to 6TB RAM for SAP HANA           |
| **u-12tb1**       | Ultra Memory      | Intel         | Up to 12TB RAM for SAP HANA          |

注释：

 **High-Performance Computing (HPC)**  高性能计算：用于天气预报和气候建模 🌤️
计算流体动力学 (CFD) 💨    基因组研究和生物信息学 🧬
石油和天然气地震处理 ⛏️     汽车碰撞模拟 🚗💥      金融蒙特卡罗模拟 💹等需要快速处理大量数据，或利用计算模型的虚拟机，支持分布式计算，但不适合实时类型的任务。

 **Field-Programmable Gate Arrays (FPGA)** 现场可编程门阵列：客制化硬件，可预编程，重新编程的硬件。用于实时大数据计算，实时音频，视频传输，深度学习等场景，只有此类型的虚拟机提供硬件加速。

总体来说，EC2 instance 来说可以分为以下类别：

1.General purpose（通用）什么都能干一点，什么都不突出，以t或者m开头

2.Compute Optimized(计算优化型) 用于执行大量计算任务的类型，CPU和RAM都是不错的配置，且拥有较好的网络频宽，可用于网络服务器，游戏服务器，大数据模型，视频转码等。

3.Memory Optimized(记忆体优化型)适合用于执行RAM使用量高的任务，比如大数据模型计算，数据库。

4.Storage Optimized（存储优化型）有较大且读写速度快的硬盘存储空间，适合用于云存储，网盘，大数据分析或 NoSQL数据库。



5.GPU and FPGA Instances（GPU或现场可编程门阵列优化型）：适合用于视频渲染，科学实验模拟，深度学习等。

6.High-Performance Networking Instances（高性能网络型）：适用于低延迟，网络频宽高的软件或程序。

关于型号内的字母简称，解析如下：

| **Suffix** | **Meaning** | **Use Case** |
| ---------- | ----------- | ------------ |
|            |             |              |

| **i** | **Intel-based** processor | Used when AWS wants to specify Intel CPUs explicitly. (e.g., `m6i`, `c6i`) |
| ----- | ------------------------- | ------------------------------------------------------------ |
|       |                           |                                                              |

| **a** | **AMD-based** processor | Instances using AMD EPYC processors, usually cheaper than Intel equivalents. (e.g., `m5a`, `c5a`) |
| ----- | ----------------------- | ------------------------------------------------------------ |
|       |                         |                                                              |

| **g** | **Graviton (AWS ARM-based CPU)** | AWS-designed ARM64 processor for better price/performance. (e.g., `m6g`, `r6g`) |
| ----- | -------------------------------- | ------------------------------------------------------------ |
|       |                                  |                                                              |

| **d** | **Local SSD (NVMe) storage** | Includes local **instance store** disks for high-speed, temporary storage. (e.g., `i4i`, `m6id`) |
| ----- | ---------------------------- | ------------------------------------------------------------ |
|       |                              |                                                              |

| **n** | **High-speed networking** | Supports enhanced networking, better for high-throughput and low-latency applications. (e.g., `c5n`, `m5n`) |
| ----- | ------------------------- | ------------------------------------------------------------ |
|       |                           |                                                              |

| **en** | **High storage throughput** | Optimized for **storage-heavy workloads** with high **EBS bandwidth**. (e.g., `d3en`) |
| ------ | --------------------------- | ------------------------------------------------------------ |
|        |                             |                                                              |

| **zn** | **High-frequency Intel CPUs** | Used for applications requiring ultra-high CPU speed (e.g., `z1d`). |
| ------ | ----------------------------- | ------------------------------------------------------------ |
|        |                               |                                                              |

| **f** | **Field Programmable Gate Arrays (FPGAs)** | Hardware acceleration for specialized applications. (e.g., `f1`) |
| ----- | ------------------------------------------ | ------------------------------------------------------------ |
|       |                                            |                                                              |

| **p** | **GPU acceleration (NVIDIA GPUs)** | Designed for deep learning, AI, and ML workloads. (e.g., `p4d`, `p3`) |
| ----- | ---------------------------------- | ------------------------------------------------------------ |
|       |                                    |                                                              |

| **g** | **GPU acceleration (for gaming & ML inference)** | Typically uses NVIDIA T4 or A10G GPUs. (e.g., `g5`, `g4dn`) |
| ----- | ------------------------------------------------ | ----------------------------------------------------------- |
|       |                                                  |                                                             |

| **h** | **High storage HDDs** | Optimized for high-capacity storage workloads. (e.g., `h1`) |
| ----- | --------------------- | ----------------------------------------------------------- |
|       |                       |                                                             |

| **x** | **Extra-large memory (SAP HANA, large databases)** | These instances are optimized for **memory-intensive** applications. (e.g., `x1e`, `x2idn`) |
| ----- | -------------------------------------------------- | ------------------------------------------------------------ |
|       |                                                    |                                                              |

| **u** | **Ultra-high memory (TB-scale RAM)** | Instances with **up to 24 TB** of RAM, used for enterprise workloads. (e.g., `u-6tb1`, `u-12tb1`) |
| ----- | ------------------------------------ | ------------------------------------------------------------ |
|       |                                      |                                                              |

在AWS EC2 开启了第一个虚拟机后，要对虚拟机进行一些调教。

我设置的第一个虚拟机是Ubuntu（教程推荐amazon linux）。

注释：这两个os都是linux，但不同版本。Amazon linux 是由AWS开发的。特别为AWS EC2而调教的linux。其内已经预先安装了AWS CLi，Amazon SSHAgent等诸多工具。且起安全性和更新由AWS直接管理。而Ubuntu是一个多用途的Linux（一块砖，哪里需要哪里搬）。有着更大的社区，所以有更好的软件适配性。

续接上文，我们需要做的第一件事就是更改用户。第一次登陆时用的是系统默认的用户（ubuntu）。此用户有安全风险。所以要增加一个新用户。

具体命令如下。

ssh -i "your-key.pem" ubuntu@your-ec2-public-ip（用SSH连接到EC2 instance，这里用的用户是ubuntu,默认用户）

sudo adduser your-new-user（增加新用户）

sudo usermod -aG sudo your-new-user（给予其admin的权限）

su - your-new-user（转到新用户）

sudo mkdir -p /home/your-new-user/.ssh
sudo chmod 700 /home/your-new-user/.ssh（为新用户创建.ssh文件夹用于存放验证的密钥）

sudo cp /home/ubuntu/.ssh/authorized_keys /home/your-new-user/.ssh/authorized_keys（把旧的密钥拷贝过来）

sudo chown -R your-new-user:your-new-user /home/your-new-user/.ssh
sudo chmod 600 /home/your-new-user/.ssh/authorized_keys（把修改权限转给新用户）

ssh -i "your-key.pem" your-new-user@your-ec2-public-ip(最后一步，使用新用户登录EC2)



如果要在EC2 instance中运行程序并让外部程序访问你的api。需要注意以下事情：

1.在EC2 security group里设置 inbound 和outbound rule。要设置让相应的外部ip可以访问ec2 instance,并让EC2 instance可以通过网络端口来访问外部网络。

当你的程序需要接受来自外部网络的信息（无论什么），你需要设置inbound rule让外部的信息可以进来，否则会连接超时。而outbound rule默认为允许任何与外部的连接。所以通常不用设置。当你在ec2 instance上的程序需要访问其他api时则需要设置outbound rule。

注释：请注意，security group是EC2中负责管理出入控制的服务。通过将security group加载到其他服务中，如EC2 instance，其可以控制来自外部网络的什么端口下的什么应用可以访问EC2 instance，同时EC2 instance可以通过什么端口，哪种协议方式于外界联通。



在EC2 instnace 测试部署API时，需要用到的linux 命令：

1.rm app.py(删除某个文件，这里是app.py)

2.sudo netstat -tunlp | grep 5000(寻找使用某个端口的进程，这里是5000)

3.sudo kill -9 <PID> (杀死某个进程，使用进程的PID，上一个命令可得进程的info，包括PID)

4.lsof -i :5000(找到在某个端口上运行的进程，这里是5000)

5.python3 receive_api.py &(使某个进程运行在后台，而不占用控制台的输出，你登出后，程序会停止，前面是需要运行的程序入口文件名)

6.nohup python3 receive_api.py &（使某个进程运行在后台，而不占用控制台的输出，登出后，程序会继续运行）

7.tail -f output.log(查看log)

8.scp -i xxx.pem myfile.txt ubuntu@EC2-public-IP:/home/ubuntu/(将某个文件从你的电脑转到EC2 instance上，xxx.pem是你的EC2 instance的连接密钥，myfile.txt是你需要转移的文件，后面的ubuntu是你要登录的用户名，/home/ubuntu/是转移到的目录)

9.nano app.py(打开某个文件，可以修改文件内容。如果没有这个文件，系统会创建一个新文件，你编写完后，按ctrl加x保存)

10.cat app.py(展示某个文件的内容，只读，无法修改)

11.sudo ufw status(查询ubuntu防火墙是否启用  ufw为Uncomplicated Firewall的缩写)

——》 11.sudo ufw enable/disble(启用或禁用防火墙)

12.sudo ufw allow/deny 5000/tcp （允许或禁止从端口5000的tcp连接 这个为inbound的control特指外部进程连接EC2 instance）

——》sudo ufw allow from 192.168.1.50 (只允许来自某个特定IP的网络traffic)

——》sudo ufw allow from 192.168.1.50 to any port 5000（允许某IP使用端口5000，这个为outbound的control,特指EC2 instance中的进程通过某个端口访问外界网络）

——》sudo ufw allow out to any port（允许所有EC2 instance内的进程通过任何端口访问外界网络）

13.sudo ufw reload/reset(刷新防火墙状态，重置防火墙)

14.curl http://127.0.0.1:5000/users (测试某个api是否可以在系统内部被调用)

 OLTP database(Online Transaction Processing)联机事务处理数据库：此种数据库用于处理高频的，短暂且并发的事务。多用于证券交易平台，银行，购票系统，大型仓库的库存管理，或者有实时需求的软件程序。OLTP数据库是关系型数据库。是和这种数据库相似的是，OLAP database (Online Analytical Processing dtabase)联机分析处理数据库.OLAP数据库是用于复杂数据运算和分析的而诞生的。其使用列式储存。而OLTP使用行式储存。

OLTP的储存方式所展现的output：

TransactionID  |  Date        |  Customer  |  Amount

  TXN1001       |  2024-02-25  |  Alice     |  50.00
  TXN1002       |  2024-02-26  |  Bob       |  200.00
  TXN1003       |  2024-02-27  |  Charlie   |  75.00



OLAP的储存方式所展现的output：

Column: TransactionID →  TXN1001, TXN1002, TXN1003
Column: Date          →  2024-02-25, 2024-02-26, 2024-02-27
Column: Customer      →  Alice, Bob, Charlie
Column: Amount        →  50.00, 200.00, 75.00

其实，OLTP database和

### **A) Databases Built for OLTP**

✅ These databases are optimized for **fast transactions and real-time updates**.

| **Database**             | **Type**         | **Best For**                             |
| ------------------------ | ---------------- | ---------------------------------------- |
| **MySQL**                | Relational (SQL) | Web applications, e-commerce             |
| **PostgreSQL**           | Relational (SQL) | Enterprise apps, analytics (with tuning) |
| **Oracle Database**      | Relational (SQL) | Large-scale transactional systems        |
| **Microsoft SQL Server** | Relational (SQL) | Business applications, ERP               |
| **Amazon Aurora**        | Cloud OLTP       | Scalable OLTP workloads                  |
| **Google Spanner**       | Distributed SQL  | Global-scale OLTP                        |

📌 **These databases are tuned for fast transactions with ACID compliance.**



### **B) Databases Built for OLAP**

✅ These databases are optimized for **complex queries, analytics, and reporting**.

| **Database**        | **Type**        | **Best For**                            |
| ------------------- | --------------- | --------------------------------------- |
| **Amazon Redshift** | Columnar OLAP   | Cloud data warehouses                   |
| **Snowflake**       | Columnar OLAP   | Scalable analytics across clouds        |
| **Google BigQuery** | Serverless OLAP | Large-scale analytics                   |
| **Apache Druid**    | Hybrid OLAP     | Real-time analytics                     |
| **ClickHouse**      | Columnar OLAP   | Fast aggregations, logs, and dashboards |
| **SAP HANA**        | Hybrid OLAP     | Enterprise analytics                    |

📌 **These databases are designed for analytics, reporting, and aggregations on massive datasets.**





有关EC2 虚拟机购买的选择：

有很多种方式来购买以及使用EC2 instance。

基本上分为两种。一种是往性能，稳定性方面来考虑。即我购买这个EC2 instance是为了支持我的高强度使用，长期使用。而另一种是以省钱方面来考虑。省钱那就只能牺牲性能和使用时长了。

EC2 instance 的购买选择具体有如下几种：



## **1. On-Demand Instances（按需实例，随用随付）**

### **🛒 类比：** 按天租住酒店房间

- 你**按小时或按秒支付**计算资源费用，无需长期承诺。
- **随时启动或停止**，灵活性高，但长期使用成本较高。

📌 **示例：**

- 你正在开发一个**API**，想在 EC2 上测试几小时。
- 你**启动实例 → 测试 → 关闭实例**，只需支付运行时间（如 3 小时）。

✔ **适用于：**

- **短期任务**（测试、开发、临时项目）。
- **流量不稳定**，不确定需要运行多久的情况。

❌ **不适用于：**

- **24/7 运行的应用**（长期成本高）。

------

## **2. Reserved Instances（预留实例，长期折扣）**

### **🛒 类比：** 租一套公寓签 1~3 年合约

- 预先承诺使用 **1 年或 3 年**，可获得**高达 72% 的折扣**。

- 适用于**长期稳定运行的应用**，比 On-Demand 便宜很多。

- 有 

  三种类型：

  - **Standard RIs**（标准 RI） → 折扣最大，但无法更改实例类型。
  - **Convertible RIs**（可转换 RI） → 可更改实例类型，折扣略低。
  - **Scheduled RIs**（定时 RI） → 仅在特定时间段运行的实例。

📌 **示例：**

- 你的 **电商网站** 需要 24/7 运行。
- 你购买 **3 年 Reserved Instance**，每月节省大量成本。

✔ **适用于：**

- **长期运行的业务**（网站、数据库、后端服务）。
- 计划 **节省长期费用** 的公司或个人。

❌ **不适用于：**

- 需求波动较大、不确定实例类型的情况。

------

## **3. Savings Plans（节省计划，灵活折扣）**

### **🛒 类比：** 健身房会员卡，你预付一定金额，可在多个健身房使用。

- 你承诺**每小时消费一定金额**，可用于不同 EC2 实例。
- 比 Reserved Instances 更灵活，适用于**多种实例类型和地区**。
- **最高可节省 66%**，但仍需要 1~3 年的承诺。

📌 **示例：**

- 你同时运行 **多个 EC2 实例**，但类型不固定。
- 你购买 **$1/小时的 Savings Plan**，AWS 自动为你的实例应用折扣。

✔ **适用于：**

- **多种工作负载**，但仍想**长期节省成本**。

❌ **不适用于：**

- **短期任务**，或者不想有长期承诺的情况。

------

## **4. Spot Instances（竞价实例，低价但不稳定）**

### **🛒 类比：** 购买**特价机票**，但航空公司可能随时取消。

- **最高可节省 90%**，价格极低。
- AWS **可能随时终止实例**（如果需要资源）。
- 适用于**可以容忍突然中断**的任务（如批量计算）。

📌 **示例：**

- 你需要用 AI 处理 **1000 张图片**。
- 你购买 **Spot Instance**，价格只需 On-Demand 的 **1/10**。
- 运行 **5 小时后 AWS 终止实例**，你重启并继续任务。

✔ **适用于：**

- **批量处理、AI 训练、大数据计算**。

❌ **不适用于：**

- **数据库、网站等必须始终可用的服务**。

------

## **5. Dedicated Hosts（专用主机，整个物理服务器）**

### **🛒 类比：** 租下一整栋办公楼，而不是只租一个办公室。

- 你获得 **整台物理服务器**，不会与其他 AWS 用户共享，甚至在你的instance中，如果你不设置，你的其他instance也无法共享资源。
- 你的程序，数据会一直留在这台主机，除非你不租了。
- 适用于 **合规要求高** 或 需要 **特定软件授权**（如 Windows per-core 许可），意思是使用的软件全部由你买产品授权。
- 硬件层面的东西也可由你自己控制（控制虚拟机在哪个cpu上运行）。

📌 **示例：**

- 你需要运行 **Windows 软件**，但要求 **物理核心授权**。
- 你租用 **Dedicated Host**，以满足软件授权要求。

✔ **适用于：**

- 需要**独立硬件资源**的企业或机构。
- **合规、安全性要求高**的应用。

❌ **不适用于：**

- 一般计算任务（成本高）。

------

## **6. Dedicated Instances（专用实例，独享但非整台服务器）**

### **🛒 类比：** 租整层公寓，但楼里还有其他住户。

- 你的 EC2 实例**运行在独占硬件上**，但仍共享服务器的部分资源。
- 提供比普通 EC2 **更高的安全性**，但**比 Dedicated Hosts 便宜**。

📌 **示例：**

- 你是**金融公司**，需要**数据隔离**但不想租整个服务器。
- 你使用 **Dedicated Instances**，确保实例运行在独立的物理服务器上。

✔ **适用于：**

- **金融、医疗等需要额外安全保障的行业**。

❌ **不适用于：**

- 预算有限，或无需额外隔离的情况。

此方式意味着：

1.只有你的AWS账号上的EC2 instance 会在此服务器上运行，无其他人的EC2 instance

2.你的服务器资源只会和你账户下的instance共享（如果你开了多个虚拟机的话），其他任何人不会占用你这台服务器的CPU，GPU，RAM，Disk，network资源，只有你自己在用。

3.当你租赁的服务器需要维护，或者你关停了虚拟机然后重启，或者服务器硬件失效，则有可能，你的虚拟机会被转到另一台服务器上。

------

## **对比表格**

| **选项**                | **价格**     | **是否需要长期承诺** | **灵活性** | **AWS 是否可能终止实例** | **适用场景**            |
| ----------------------- | ------------ | -------------------- | ---------- | ------------------------ | ----------------------- |
| **On-Demand**           | 💰💰💰（贵）    | ❌ 无                 | ✅ 高       | ❌ 否                     | 短期任务、测试开发      |
| **Reserved Instances**  | 💰💰（较便宜） | ✅ 1-3 年             | ❌ 低       | ❌ 否                     | 长期运行的应用          |
| **Savings Plans**       | 💰💰（较便宜） | ✅ 1-3 年             | ✅ 中等     | ❌ 否                     | 多种实例的长期节省      |
| **Spot Instances**      | 💰（超便宜）  | ❌ 无                 | ❌ 低       | ✅ 是                     | 大规模数据计算、AI 训练 |
| **Dedicated Hosts**     | 💰💰💰（贵）    | ✅ 1-3 年             | ❌ 低       | ❌ 否                     | 合规、高安全需求        |
| **Dedicated Instances** | 💰💰（贵）     | ✅ 1-3 年             | ❌ 低       | ❌ 否                     | 需要隔离的工作负载      |

------

## **总结：如何选择？**

1️⃣ **如果你只想测试 API，偶尔运行：**
➡ **On-Demand Instances**

2️⃣ **如果你计划长期运行 API，每月都有固定使用量：**
➡ **Reserved Instances 或 Savings Plans**

3️⃣ **如果你想省钱且不怕实例被 AWS 终止：**
➡ **Spot Instances**

4️⃣ **如果你有合规要求，必须使用独立硬件：**
➡ **Dedicated Hosts 或 Dedicated Instances**

------

### **对于你的需求（Ubuntu EC2 运行 API）：**

🔹 **短期测试 →** 🟢 On-Demand
🔹 **长期运行 →** 🟢 Reserved Instances 或 Savings Plans
🔹 **追求最低成本，可接受中断 →** 🟢 Spot Instances





有关在linux上进行有关mysql server有关操作的命令:

| **Command**                                                 | **Description**                                             |
| ----------------------------------------------------------- | ----------------------------------------------------------- |
| `ls -l /etc/mysql/mysql.conf.d/mysqld.cnf`                  | Check file permissions and ownership.                       |
| `ls -ld /etc/mysql/mysql.conf.d/`                           | Check directory permissions.                                |
| `sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf`              | Edit MySQL configuration file.                              |
| `sudo chown $USER:$USER /etc/mysql/mysql.conf.d/mysqld.cnf` | Change ownership of the file to current user.               |
| `sudo chmod 644 /etc/mysql/mysql.conf.d/mysqld.cnf`         | Set read/write permissions for owner, read-only for others. |
| `sudo chmod 755 /etc/mysql/mysql.conf.d/`                   | Restore correct directory permissions.                      |
| `sudo chmod 777 /etc/mysql/mysql.conf.d/`                   | Temporarily allow full write access.                        |
| `sudo mv /etc/mysql/mysql.cnf /etc/mysql/mysqld.cnf.bak`    | Rename file (backup).                                       |
| `sudo rm /etc/mysql/mysqld.cnf`                             | Delete the file.                                            |

以上的命令中，mysqld.cnf是mysql的重要配置文件。修改bind-address是在此修改。

bind-adress用于确定mysql server可以在什么范围内和其他程序通信（像EC2 的inbound rule）

默认是127.0.0.1,所以只能在本机环境内通信，无法和外界机器通讯。改成0.0.0.0则可以允许外部机器的任意ip进行通讯。

以下是直接操作mysql database的命令

## 🔹 **2. MySQL Database Commands**

| **Command**                                                  | **Description**                                |
| ------------------------------------------------------------ | ---------------------------------------------- |
| `sudo mysql -u root -p`                                      | Log in to MySQL as root.                       |
| `SELECT user(), current_user();`                             | Check which MySQL user is currently logged in. |
| `SELECT user, host FROM mysql.user;`                         | List all MySQL users and their allowed hosts.  |
| `SELECT @@bind_address;`                                     | Check the current bind address setting.        |
| `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'NewPassword';` | Change MySQL root password.                    |
| `SHOW GRANTS FOR CURRENT_USER();`                            | Show all privileges for the current user.      |
| `SHOW GRANTS FOR 'myuser'@'localhost';`                      | Show privileges for a specific user.           |
| `GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%' WITH GRANT OPTION;` | Allow remote access for a user.                |
| `FLUSH PRIVILEGES;`                                          | Apply changes to user privileges.              |
| `CREATE DATABASE testdb;`                                    | Create a new MySQL database.                   |
| `USE testdb;`                                                | Switch to a specific database.                 |
| `CREATE TABLE users (...);`                                  | Create a new table.                            |
| `INSERT INTO users (...) VALUES (...);`                      | Insert sample data into a table.               |
| `SELECT * FROM users;`                                       | Retrieve all records from the `users` table.   |
| `DELETE FROM users WHERE username = 'Alice';`                | Delete a record.                               |
| `DROP TABLE users;`                                          | Delete a table.                                |
| `SHOW PLUGINS WHERE Name = 'mysqlx';`                        | Check if MySQL X Protocol is enabled.          |
| `INSTALL PLUGIN mysqlx SONAME 'mysqlx.so';`                  | Enable MySQL X Protocol.                       |

以上命令包含了新建用户，授予用户权限等重要命令。





## 🔹 **1. Security & Authentication Privileges**

| **Privilege**                | **Example Use Case**                                     | **SQL Command**                                              |
| ---------------------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| `APPLICATION_PASSWORD_ADMIN` | Set a **strong password policy** for applications.       | `ALTER USER 'appuser'@'%' IDENTIFIED BY 'StrongP@ss123!' REQUIRE SSL;` |
| `AUDIT_ABORT_EXEMPT`         | Allow **a trusted user** to bypass audit logs.           | `GRANT AUDIT_ABORT_EXEMPT ON *.* TO 'trusted_admin'@'%';`    |
| `PASSWORDLESS_USER_ADMIN`    | Create a **passwordless user** using SSL authentication. | `CREATE USER 'certuser'@'%' REQUIRE X509;`                   |
| `ROLE_ADMIN`                 | Assign a **predefined role** to a user.                  | `GRANT 'reporting_role' TO 'manager'@'%';`                   |
| `SET_USER_ID`                | Allow a user to **run queries as another user**.         | `GRANT SET_USER_ID ON *.* TO 'support_staff'@'%';`           |
| `SYSTEM_USER`                | Allow an admin to **override security restrictions**.    | `GRANT SYSTEM_USER ON *.* TO 'sysadmin'@'%';`                |

------

## 🔹 **2. System Administration & Performance Privileges**

| **Privilege**                | **Example Use Case**                                   | **SQL Command**                                            |
| ---------------------------- | ------------------------------------------------------ | ---------------------------------------------------------- |
| `CONNECTION_ADMIN`           | Allow an admin to **bypass max_connections limit**.    | `GRANT CONNECTION_ADMIN ON *.* TO 'dba'@'%';`              |
| `SESSION_VARIABLES_ADMIN`    | Allow a user to **modify session variables**.          | `GRANT SESSION_VARIABLES_ADMIN ON *.* TO 'perf_user'@'%';` |
| `SYSTEM_VARIABLES_ADMIN`     | Allow a user to **modify global settings**.            | `GRANT SYSTEM_VARIABLES_ADMIN ON *.* TO 'tuner'@'%';`      |
| `PERSIST_RO_VARIABLES_ADMIN` | Modify **read-only system variables** without restart. | `SET PERSIST_ONLY max_connections = 200;`                  |
| `RESOURCE_GROUP_ADMIN`       | Create a **CPU/memory resource group**.                | `CREATE RESOURCE GROUP reporting TYPE USER VCPU=0-3;`      |
| `RESOURCE_GROUP_USER`        | Allow a user to **use a resource group**.              | `GRANT RESOURCE_GROUP_USER ON *.* TO 'query_user'@'%';`    |

------

## 🔹 **3. Replication & Backup Privileges**

| **Privilege**                   | **Example Use Case**                                 | **SQL Command**                                              |
| ------------------------------- | ---------------------------------------------------- | ------------------------------------------------------------ |
| `BINLOG_ADMIN`                  | Allow a user to **manage binary logs**.              | `GRANT BINLOG_ADMIN ON *.* TO 'replication_mgr'@'%';`        |
| `BACKUP_ADMIN`                  | Allow a user to **perform backups**.                 | `GRANT BACKUP_ADMIN ON *.* TO 'backupuser'@'%';`             |
| `GROUP_REPLICATION_ADMIN`       | Allow a user to **manage MySQL Group Replication**.  | `GRANT GROUP_REPLICATION_ADMIN ON *.* TO 'cluster_admin'@'%';` |
| `TRANSACTION_REPLICATION_ADMIN` | Allow a user to **control transaction replication**. | `GRANT TRANSACTION_REPLICATION_ADMIN ON *.* TO 'replica_mgr'@'%';` |
| `XA_RECOVER_ADMIN`              | Allow a user to **manage XA transactions**.          | `GRANT XA_RECOVER_ADMIN ON *.* TO 'xa_manager'@'%';`         |

------

## 🔹 **4. Data Encryption & Firewall Privileges**

| **Privilege**            | **Example Use Case**                             | **SQL Command**                                              |
| ------------------------ | ------------------------------------------------ | ------------------------------------------------------------ |
| `ENCRYPTION_KEY_ADMIN`   | Allow a user to **manage encryption keys**.      | `GRANT ENCRYPTION_KEY_ADMIN ON *.* TO 'encryption_admin'@'%';` |
| `TABLE_ENCRYPTION_ADMIN` | Allow a user to **enable table encryption**.     | `ALTER TABLE customers ENCRYPTION='Y';`                      |
| `FIREWALL_ADMIN`         | Allow a user to **manage firewall rules**.       | `GRANT FIREWALL_ADMIN ON *.* TO 'security_officer'@'%';`     |
| `FIREWALL_USER`          | Allow a user to **set personal firewall rules**. | `GRANT FIREWALL_USER ON *.* TO 'dev_user'@'%';`              |

------

## 🔹 **5. Logging, Monitoring & Query Access Privileges**

| **Privilege**             | **Example Use Case**                        | **SQL Command**                                    |
| ------------------------- | ------------------------------------------- | -------------------------------------------------- |
| `INNODB_REDO_LOG_ARCHIVE` | Enable **archiving of redo logs**.          | `ALTER INSTANCE ENABLE INNODB REDO LOG ARCHIVE;`   |
| `SHOW_ROUTINE`            | Allow a user to **view stored procedures**. | `GRANT SHOW_ROUTINE ON *.* TO 'dev_readonly'@'%';` |



以上这些是mysql 8.0以后新增加的权限，大半部分是正常时候用不到的。



EC2 Elastic IP（弹性ip）

通常来说，当一个EC2 instance被停止，再次重启时，ip地址会变，如果你对固定的ip地址有需求的话，可以申请一个弹性ip，其实就是租一个固定的ip，然后把这个ip套到EC2 instance上，那么，EC2 instance的关闭和重启就不会改变instance的ip

EC2 placement group

placement group 是一项你可以用来控制EC2 instance 在AWS物理设施里的位置。以达成最大化效能或高容错性等目的的特性。

Placement groups有三种

1.cluster(簇)，字如其名，所有的EC2 instance都会被放置在同一个服务器机柜里，所以如果这个机柜挂了，则所有的的EC2 instances都会失效。

2.Spread,完全分布式放置。每一个instance都会被放置在不同机柜的不同服务器内，所以任意一个instance所在的硬件失效都不会导致其他instance失效。适合微服务架构的程序，需要高可用性的数据库。

3.partition:鉴于以上两者之间。它既不像cluster一样把鸡蛋都放在一个篮子里，也不像spread一样把所有的instance都完全分布设置。它将instance进行分组，每一组放置在同一个服务器机柜中，每个instance拥有独立的硬件，并且彼此独立，但由于在同一机柜，所以一个物理硬件的失效会影响到一组的instances。

EC2 ENI（elastic network interface）

ENI是EC2 instance内的一个虚拟网卡，提供给虚拟机在Amzon VPC内部的网络连接能力

此虚拟网卡提供了一个公开的IP和私有ip，还有连接security groups的能力，还有MAC等等。

我们可以将多个ENI连接到EC2 instance中，这样，一个instance可以做到同时使用多个ENI，实现多个网段的独立通信。只要security groups 和network routing设置正常。



### **1. 一个实例可以同时使用两个 ENI 进行通信吗？**

✅ **是的！一个 EC2 实例可以同时使用多个 ENI 进行通信**，前提是**网络路由和安全组规则允许**。

### **如何运作？**

- 每个 ENI 都有**独立的 MAC 地址、私有 IP 和可选的公有 IP**。
- **操作系统 (OS) 需要正确配置路由**，以确保流量可以通过正确的 ENI 传输。
- 这种设置通常用于**网络分隔**（例如，一个 ENI 连接外部互联网，另一个 ENI 连接私有网络）。

#### **示例用例**

| **ENI**   | **网络角色** | **用途**                     |
| --------- | ------------ | ---------------------------- |
| **ENI 1** | 公有子网     | 外部访问（互联网、API）      |
| **ENI 2** | 私有子网     | 内部流量（数据库、私有服务） |

⚠️ **挑战：两个 ENI 之间的路由** 默认情况下，Linux 或 Windows 会**将所有出站流量路由到默认 ENI**。要同时使用两个 ENI，必须配置 **基于策略的路由（PBR）**（Linux）或使用 **Windows 网络接口绑定**。

------

### **2. 如果 ENI 无法从故障实例上分离，会发生什么？**

✅ **是的，这可能是一个潜在问题**，在某些情况下，ENI 可能无法从故障实例上分离，例如**系统崩溃、实例卡死或 AWS 侧的网络问题**。

### **为什么 ENI 可能无法分离？**

1. **实例无响应** —— 如果 EC2 实例处于不可用状态（例如冻结或异常关机），ENI 可能会**卡在已附加状态**。
2. **活动的网络连接** —— 如果 ENI 仍然在处理活动连接，AWS 可能会阻止分离。
3. **权限问题** —— 如果 IAM 角色缺少 `ec2:DetachNetworkInterface` 权限，ENI 将无法被分离。

### **解决方案：强制分离 ENI**

如果 ENI 被卡住，可以使用 AWS CLI **强制分离**：

```
sh


CopyEdit
aws ec2 detach-network-interface --attachment-id eni-attach-12345678 --force
```

分离后，可以重新附加到新的实例上。

------

### **3. 为什么要给实例附加多个 ENI，而不是直接创建新的 ENI？**

💡 **简短回答：创建新 ENI 会导致新的 IP、MAC 地址和安全组规则，可能会破坏现有服务。**

### **附加多个 ENI 的优势**

✅ **故障切换无需更改 IP**

- 如果 EC2 实例故障，可以**分离现有 ENI 并附加到新实例**。
- 这样可以**保持相同的私有 IP 和弹性 IP (EIP)**，避免**DNS 变更或连接中断**。

✅ **保持 MAC 地址（对授权软件有用）**

- 某些应用程序（如安全软件、授权系统）使用**MAC 地址**识别服务器。
- **新创建的 ENI 会生成新的 MAC 地址**，可能会导致**软件授权失效**。

✅ **支持多网络分隔**

- 可以使用不同的 ENI 连接到不同的网络，而不需要在同一个 ENI 上手动配置多个 IP。

✅ **适用于有状态应用的高可用性**

- 如果 Web 服务需要**零停机时间**，保留一个备用 ENI 允许流量**快速切换**。

------

### **4. 为什么不直接把 ENI 作为实例的备份？**

✅ 你**可以**在一个 EC2 实例上附加多个 ENI 作为备份，但它**不会自动切换**，除非经过额外的配置。

💡 **示例：备用 ENI 设定**

- **主 ENI** 处理正常流量。
- **备用 ENI** 只有在主 ENI 故障时**手动激活**。
- 需要**自定义路由规则**来在两者之间切换。

⚠️ **问题：**
如果整个**实例故障**，那么在同一个实例上保留备用 ENI **不会有任何帮助**。



EC2 Hibernrate:EC2 instance停止的一种方式。普通的stop会导致RAM内的数据消失，如果你在执行计算中，你需要停止虚拟机，但你不想计算中的数据消失，可以使用这种方式停止EC2 instance。在此方式下，EC2 instance会将RAM中的数据保存在EBS中，并被加密。下次启动时，instance会将EBS中的RAM包会被加载回RAM。所以你不会丢失数据。
