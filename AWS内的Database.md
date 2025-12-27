### AWS内的Database

AWS 提供多种托管的数据库服务，覆盖不同的数据模型和用例。以下是主要的数据库类型及其对应的服务：

------

### **1. 关系型数据库 (RDBMS)**

- **Amazon RDS**
  支持多种引擎：MySQL、PostgreSQL、MariaDB、Oracle、SQL Server。
- **Amazon Aurora**
  高性能兼容 MySQL/PostgreSQL 的关系数据库，提供 Serverless 选项。
- **Amazon Redshift**
  面向数据仓库和分析的关系型数据库（OLAP）。

------

### **2. 键值/文档数据库 (NoSQL)**

- **Amazon DynamoDB**
  全托管的键值/文档数据库，适合高并发、低延迟场景。
- **Amazon DocumentDB**
  兼容 MongoDB 的文档数据库，支持 JSON 存储。

------

### **3. 内存数据库**

- **Amazon ElastiCache**
  支持 Redis（内存键值存储）和 Memcached（简单缓存）。
- **Amazon MemoryDB for Redis**
  持久化的 Redis 兼容数据库，兼顾内存性能与数据持久性。

------

### **4. 图数据库**

- **Amazon Neptune**
  支持属性图（Property Graph）和 RDF 图，适用于社交网络、推荐系统等场景。

------

### **5. 时序数据库**

- **Amazon Timestream**
  专为时序数据（如 IoT、监控数据）优化，支持自动滚动存储。

------

### **6. 宽列存储数据库**

- **Amazon Keyspaces (Apache Cassandra 兼容)**
  全托管宽列数据库，适合大规模稀疏数据。

------

### **7. 账本数据库 (Ledger Database)**

- **Amazon QLDB (Quantum Ledger Database)**
  不可篡改的账本数据库，提供透明的数据变更历史。

------

### **8. 多模型数据库**

- **Amazon Aurora (部分支持多模型)**
  通过扩展支持文档存储（如 Aurora with PostgreSQL JSONB）。

------

### **9. 其他专用数据库**

- **Amazon Neptune ML**
  集成机器学习的图数据库扩展。
- **Amazon RDS Proxy**
  数据库连接池管理（辅助服务，非独立数据库）。





