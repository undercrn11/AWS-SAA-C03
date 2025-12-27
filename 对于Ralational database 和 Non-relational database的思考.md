##### 对于Ralational database 和 Non-relational database的思考



我个人认为中文翻译和英语中，对于关系型数据库，非关系型数据库的定义有失偏颇。

实际上应该称为亲近关系型数据库和非亲近型关系数据库。为什么这么说呢？

因为，按道理说，存在数据库中的数据（无论哪种数据库），都有一定程度的联系。史上不存在两个完全无联系的数据。只不过是数据之间的联系性有多少。在MySQL，PostgreSQL，SQLServer中，有主键，外键或join来约束表中一行数据中数据和数据彼此之间的关系。

而在多种非关系型数据库中，如：

1. ‌**[文档数据库](https://www.baidu.com/s?rsv_idx=1&wd=文档数据库&fenlei=256&usm=1&ie=utf-8&rsv_pq=eb6c04810064fc50&oq=非关系数据库有哪几种&rsv_t=4f33NNXTa%2BEe7Hge9NiaNwkpRpddYxXzHWEjztx8D7FMOcfSf%2FjhO863%2BcY&rsv_dl=re_dqa_generate&sa=re_dqa_generate)**‌：如[MongoDB](https://www.baidu.com/s?rsv_idx=1&wd=MongoDB&fenlei=256&usm=1&ie=utf-8&rsv_pq=eb6c04810064fc50&oq=非关系数据库有哪几种&rsv_t=d3165lnlde2nvWEP5UVvbNpsB3D3N2ucwzb10oKtl0C6WWJH84GXbU7%2FlgU&rsv_dl=re_dqa_generate&sa=re_dqa_generate)和[CouchDB](https://www.baidu.com/s?rsv_idx=1&wd=CouchDB&fenlei=256&usm=1&ie=utf-8&rsv_pq=eb6c04810064fc50&oq=非关系数据库有哪几种&rsv_t=d3165lnlde2nvWEP5UVvbNpsB3D3N2ucwzb10oKtl0C6WWJH84GXbU7%2FlgU&rsv_dl=re_dqa_generate&sa=re_dqa_generate)。这类数据库使用BSON或JSON格式存储数据，适合快速开发和迭代，支持复杂查询功能，并且可以水平扩展和高可用性复制。
2. ‌**[键值存储数据库](https://www.baidu.com/s?rsv_idx=1&wd=键值存储数据库&fenlei=256&usm=1&ie=utf-8&rsv_pq=eb6c04810064fc50&oq=非关系数据库有哪几种&rsv_t=100ckA6aWxJ97f71Y9V%2BLz%2Bmi58k4c6mGE3xMrnapoDgdZAZ6hJF3YyMJbM&rsv_dl=re_dqa_generate&sa=re_dqa_generate)**‌：如[Redis](https://www.baidu.com/s?rsv_idx=1&wd=Redis&fenlei=256&usm=1&ie=utf-8&rsv_pq=eb6c04810064fc50&oq=非关系数据库有哪几种&rsv_t=2ce1FjQF9PhPbqSyGAWz554h7bkWx%2BJjjMYld%2BLpI3BGZdzuDbcP7X0rYVY&rsv_dl=re_dqa_generate&sa=re_dqa_generate)和[Memcached](https://www.baidu.com/s?rsv_idx=1&wd=Memcached&fenlei=256&usm=1&ie=utf-8&rsv_pq=eb6c04810064fc50&oq=非关系数据库有哪几种&rsv_t=2ce1FjQF9PhPbqSyGAWz554h7bkWx%2BJjjMYld%2BLpI3BGZdzuDbcP7X0rYVY&rsv_dl=re_dqa_generate&sa=re_dqa_generate)。这类数据库通过键值对存储数据，查找速度快，通常用于缓存和实时数据处理。
3. ‌**[列存储数据库](https://www.baidu.com/s?rsv_idx=1&wd=列存储数据库&fenlei=256&usm=1&ie=utf-8&rsv_pq=eb6c04810064fc50&oq=非关系数据库有哪几种&rsv_t=ad3arj1FWBEF5uvY4MEesEMQzMT7BYMBAaopOBTaRh3heqOY44KdA4IwYY8&rsv_dl=re_dqa_generate&sa=re_dqa_generate)**‌：如[Cassandra](https://www.baidu.com/s?rsv_idx=1&wd=Cassandra&fenlei=256&usm=1&ie=utf-8&rsv_pq=eb6c04810064fc50&oq=非关系数据库有哪几种&rsv_t=ad3arj1FWBEF5uvY4MEesEMQzMT7BYMBAaopOBTaRh3heqOY44KdA4IwYY8&rsv_dl=re_dqa_generate&sa=re_dqa_generate)和[HBase](https://www.baidu.com/s?rsv_idx=1&wd=HBase&fenlei=256&usm=1&ie=utf-8&rsv_pq=eb6c04810064fc50&oq=非关系数据库有哪几种&rsv_t=ad3arj1FWBEF5uvY4MEesEMQzMT7BYMBAaopOBTaRh3heqOY44KdA4IwYY8&rsv_dl=re_dqa_generate&sa=re_dqa_generate)。这类数据库将同一列数据存储在一起，适合分布式存储海量数据，具有高扩展性和容错性。
4. ‌**[图数据库](https://www.baidu.com/s?rsv_idx=1&wd=图数据库&fenlei=256&usm=1&ie=utf-8&rsv_pq=eb6c04810064fc50&oq=非关系数据库有哪几种&rsv_t=e5aaY3xRbIqZc93Af48cHIjlvxxLVwl2Ug61MjSoNMvBOuVNFwl9WmoA%2B8Y&rsv_dl=re_dqa_generate&sa=re_dqa_generate)**‌：如[Neo4j](https://www.baidu.com/s?rsv_idx=1&wd=Neo4j&fenlei=256&usm=1&ie=utf-8&rsv_pq=eb6c04810064fc50&oq=非关系数据库有哪几种&rsv_t=e5aaY3xRbIqZc93Af48cHIjlvxxLVwl2Ug61MjSoNMvBOuVNFwl9WmoA%2B8Y&rsv_dl=re_dqa_generate&sa=re_dqa_generate)。这类数据库将数据以图的方式存储，适合处理复杂的关系网络数据，广泛应用于社交网络和推荐系统。
5. ‌**[搜索引擎](https://www.baidu.com/s?rsv_idx=1&wd=搜索引擎&fenlei=256&usm=1&ie=utf-8&rsv_pq=eb6c04810064fc50&oq=非关系数据库有哪几种&rsv_t=00abbOdz1XE33ogq4lBAe4XrX20ABJJpuR7ShUVGWqoV2Q9sdHCVCk6ZbwU&rsv_dl=re_dqa_generate&sa=re_dqa_generate)**‌：如[Elasticsearch](https://www.baidu.com/s?rsv_idx=1&wd=Elasticsearch&fenlei=256&usm=1&ie=utf-8&rsv_pq=eb6c04810064fc50&oq=非关系数据库有哪几种&rsv_t=00abbOdz1XE33ogq4lBAe4XrX20ABJJpuR7ShUVGWqoV2Q9sdHCVCk6ZbwU&rsv_dl=re_dqa_generate&sa=re_dqa_generate)。这类数据库用于存储、搜索和分析大量结构化和非结构化数据，适合大数据应用场景。



在这些数据库中，不少是使用键值对以体现关系。而非关系型数据库比关系型数据库做得更好的一点是，可以存储视频，图片，文件等，如MongoDB，以大型二进制对象的形式直接存储数据（在存储小文件时有用）。或者像 **Amazon S3**, Google Cloud Storage一样,将数据存储在别处，数据库里存储文件的地址或链接（存储大文件）。



有关DocumentDB和MongoDB:

DocumentDB是一个可以兼容MongoDB（另一个 NoSQL 数据库）的文件储存数据库。但其不兼容MongoDB6.0之后的新功能。其存储单位是文件，数据会以json格式写入文件中，而每个文件（或者被称为object）都有一个唯一的object_id.此id也会被写入json中。如果你在写入文件时，不添加id，则DocumentDB会手动添加。而且此id也会被单独储存在其他地方，用于快速检索。



文件数据库和图数据库的区别：

### **一、核心概念对比**

| **维度**     | **文件数据库**                  | **图像数据库**                 |
| :----------- | :------------------------------ | :----------------------------- |
| **核心目标** | 文件级存储管理（增删改查）      | 图像内容理解与智能检索         |
| **数据粒度** | 以文件为最小单位（如.jpg/.png） | 支持像素级/特征级操作          |
| **索引方式** | 文件名/路径/基础元数据          | 视觉特征向量+语义标签+时空索引 |
| **典型操作** | 文件上传/下载/版本控制          | 相似图搜索/目标检测/跨模态检索 |

------

### **二、技术架构差异**

#### **文件数据库典型架构**

mermaid

```
graph LR
    F[文件系统] --> G[文件分块存储]
    M[元数据库] --> H[记录文件路径/大小/类型]
    C[客户端] -->|HTTP API| G
    C -->|SQL查询| M
```

- **核心痛点**：无法回答"找出所有包含红色跑车的图片"这类内容级查询

#### **图像数据库增强架构**

mermaid

```
graph LR
    F[对象存储] --> G[原始文件]
    G --> H[特征提取管道]
    H --> V[向量数据库]
    M[图数据库] --> L[语义关系网]
    C[客户端] -->|"找风格类似的插画"| Q[混合查询引擎]
    Q --> V & M
```

- **关键技术**：CNN特征提取、近似最近邻搜索（ANN）、知识图谱关联

------

### **三、功能场景对照**

1. **医疗场景**
   - 文件数据库：存储DICOM文件，确保完整性校验
   - 图像数据库：通过肺结节特征匹配相似病例
2. **电商场景**
   - 文件数据库：管理商品主图CDN分发
   - 图像数据库：实现"以图搜同款"功能
3. **自动驾驶**
   - 文件数据库：路采视频归档存储
   - 图像数据库：实时交通标志特征匹配

------

### **四、混合架构实践**

现代系统常采用分层设计：

1. **存储层**：MinIO/Amazon S3 作为文件存储
2. **特征层**：Milvus/Qdrant 存储特征向量
3. **语义层**：Neo4j 构建视觉知识图谱
4. **接口层**：GraphQL 统一访问入口

**查询示例**：

python

```
# 查找"与图片A相似且拍摄于北京朝阳区的照片"
results = hybrid_search(
    vector=model.encode(imgA), 
    filters={"location": "朝阳区"},
    graph_cond="MATCH (tag:Architecture)"
)
```

------

### **五、本质区别总结**

- **文件数据库**是**内容不可知**（Content-Agnostic）的存储系统
- **图像数据库**是**内容驱动**（Content-Centric）的认知系统
- 二者关系类似于"图书馆书架"与"文献分析专家"的区别

实际系统设计中，往往需要同时部署文件数据库（管理原始数据）和图像数据库（实现智能应用），通过统一元数据服务进行桥接，形成完整的视觉数据管理体系。

> **“图数据库中的图不是指照片或图片……”**

✅ 正确。
 这里的“图”指的是**数学意义上的图结构（Graph）**，而不是视觉图像或图片。

------

## 📌 图数据库中存储的是什么？

图数据库存储：

- **实体（Entity）** → 称为**节点（Node）**，例如：照片、人物、地点、标签。
- **关系（Relationship）** → 称为**边（Edge）**，例如：拍摄、标签、位置归属。
- 这些**关系是有方向的**，包括：
  - **起点节点（source）**
  - **终点节点（target）**

示例：

```
plaintextCopyEdit(爱丽丝) --[拍摄]--> (照片1)
(照片1) --[标签]--> ("山")
```

------

## ❌ 图数据库不会做以下事情：

- 不存储实际的照片文件（图片）
- 不分析照片的视觉内容
- 不根据图像内容做相似度搜索

------

## 🧠 如果你要处理实际的照片，你还需要其他系统的配合：



| 功能                     | 技术/工具                                          |
| ------------------------ | -------------------------------------------------- |
| **存储照片文件**         | S3、CDN、本地文件系统                              |
| **分析照片内容**         | AI 图像识别模型（如 AWS Rekognition、OpenAI CLIP） |
| **将照片转为向量**       | 图像嵌入模型（Embedding Model，如 CLIP）           |
| **相似图像搜索**         | 向量数据库（如 Pinecone、FAISS、Weaviate）         |
| **组织照片的标签和关系** | 图数据库（如 Amazon Neptune、Neo4j）               |

------

## 🧩 全流程系统架构示意

1. 📸 用户上传照片
    → 照片存储在 S3 或类似系统中

2. 🤖 AI 对图片内容进行分析
    → 生成标签（如 “mountain”，"snow"）
    → 生成图像的向量嵌入（如 `[0.22, -0.1, 0.88, ...]`）

3. 🧠 使用图数据库存储元数据和关系
    → 照片1 --[标签]--> “山”
    → 照片1 --[拍摄者]--> “爱丽丝”

4. 🧮 使用向量数据库存储图像向量
    → 实现“找出相似照片”的能力

5. 🔍 用户搜索时的操作流程：
    → 先用图数据库筛选：“2023年拍摄，标签为‘山’的照片”
    → 再用向量数据库找出“看起来相似”的照片

   

#### 有关数据库的视图和表的区别：



### **数据存储方式**

- **表**
  - 是**物理存储**的数据库对象，实际存储数据。
  - 数据以行和列的形式持久化保存在磁盘中。
  - 占用存储空间（取决于数据量）。
- **视图**
  - 是**虚拟表**，本质是一个**预定义的查询（SQL 语句）**。
  - **不存储实际数据**，每次访问视图时动态生成结果。
  - 不占用额外存储空间（仅保存查询逻辑）。

------

### **2. 功能用途**

- **表**
  - 直接存储原始数据，是数据操作（增删改查）的基础。
  - 用于持久化保存业务数据。
- **视图**
  - **简化复杂查询**：将多表关联、过滤等复杂操作封装成一个虚拟表。
  - **数据安全**：隐藏敏感列（如薪资、密码），仅暴露部分数据。
  - **逻辑抽象**：为应用程序提供统一的数据接口，屏蔽底层表结构变化。
  - **权限控制**：通过视图限制用户访问特定行或列。

------

### **3. 数据更新**

- **表**
  - 支持直接对数据进行增删改操作（`INSERT`/`UPDATE`/`DELETE`）。
- **视图**
  - **大多数视图不可直接更新**，尤其是涉及多表关联、聚合函数或子查询的复杂视图。
  - 简单视图（基于单表且不含聚合或计算列）可能允许更新，但实际操作会映射到基表。

------

### **4. 性能**

- **表**
  - 查询速度快，尤其是通过索引优化后。
- **视图**
  - 每次查询视图时需执行其定义的 SQL 语句，若视图逻辑复杂或数据量大，可能导致性能下降。
  - 某些数据库支持“物化视图”（Materialized View），定期缓存结果以提升性能。

------

### **5. 结构修改**

- **表**
  - 修改表结构（如添加列）会影响存储的数据和依赖它的应用程序。
- **视图**
  - 修改视图只需调整其 SQL 查询逻辑，不影响底层表的数据。
  - 若视图的查询结果列名或类型变化，依赖它的查询可能需要调整。

------

### **示例对比**

- **表**

  sql

  ```
  CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    salary INT,
    department VARCHAR(50)
  );
  ```

- **视图**

  sql

  ```
  -- 创建一个隐藏薪资的视图
  CREATE VIEW employee_view AS
  SELECT id, name, department
  FROM employees;
  ```

------

### **总结**

| **特性**     | **表**                   | **视图**                     |
| :----------- | :----------------------- | :--------------------------- |
| 存储数据     | 是（物理存储）           | 否（仅保存查询逻辑）         |
| 占用存储空间 | 是                       | 否                           |
| 数据更新     | 直接支持增删改           | 多数情况下不支持             |
| 性能         | 通常更快（直接访问数据） | 依赖查询复杂度，可能较慢     |
| 安全性       | 直接暴露所有数据         | 可隐藏敏感信息               |
| 用途         | 存储原始数据             | 简化查询、安全控制、逻辑抽象 |
