### AWS额外储存服务

AWS中除了一般业务用到的存储系统（EBS,EFS,S3等），还有用于特定目的的特殊存储系统。

第一个特殊存储系统为AWS Snowball。这种存储系统，或服务适用于文件系统备份，或需要大量算力的场景。

当你请求了snowball服务后，AWS会给你发送一个实体的snowball单元。其本质就是一台高性能的服务器加存储。你可以用于S3的备份，假如你有很多文件，使用现有的带宽保存到AWS上很花时间。那么使用snowball会是一个好选择。你只需要再snowball服务中选定你需要的机器，选择合适的规则让snowball访问S3，然后选定需要保存到哪个bucket里。不过需要注意的一点是，从snowball到S3的文件，无法直接被存储到备份或长期保存level的存储中。所以你需要为此设置生命周期，来让他们存储到合适的位置。

另外一种使用场景为，受到环境限制，你无法获得或使用高算力的机器（公司小，或没有网络），而你需要短时间内获得机器来帮你完成生产任务。则可以使用snowball。但需要注意，此机器不是你的，只是租的。所有权归AWS。



第二种特殊存储系统为FSx。

在现有已学的AWS存储中，EBS是为EC2专门设立的存储，EFS虽为共享存储系统，但延迟较大，且也为EC2 专用。而S3虽然不是S3专用，但是无序存储系统，用户需要自己创建，管理索引。所以需要一种可以适应多种os，多种存储协议，IOPS高，可以作为高性能计算的存储。这便是FSx。现阶段FSx提供4种文件系统。



##### Amazon FSx for Windows 文件服务器

✅ 特色：
提供完全托管的 Windows 文件系统，由 NTFS 文件系统和 SMB 协议支持。

🧰 最适合：
需要文件存储的基于 Windows 的应用程序。

需要 Active Directory 集成的环境。

Windows 客户端访问的共享文件夹。

⚙️ 功能：
具有文件级权限 (ACL) 的 NTFS 支持。

SMBv2 和 SMBv3 协议支持。

本机支持 DFS 命名空间、卷影副本（快照）等 Windows 功能。

与 Microsoft Active Directory（自我管理或 AWS 托管 AD）集成。

使用多可用区实现完全托管的备份和自动故障转移。



##### 🟢 2. Amazon FSx for Lustre

✅ 特色：
专为速度和大量吞吐量而设计的高性能并行文件系统，通常与 HPC 和 S3 集成一起使用。

🧰 最适合：
机器学习 (ML) 和人工智能 (AI)。

高性能计算 (HPC)。

视频渲染、基因组处理、模拟。

S3 数据的突发处理。

⚙️ 特点：
亚毫秒级延迟和高达 100 GB/s 的吞吐量。

符合 POSIX 标准。

可以直接链接到 Amazon S3 存储桶（按需或自动导入/导出）。

临时或持久存储模式。



##### 🔴 3. Amazon FSx for NetApp ONTAP

✅ 专长：
将 NetApp ONTAP 文件系统引入 AWS，完全支持多协议访问、企业功能和混合云部署。

🧰 最适合：
已经在本地环境中使用 NetApp 的企业。

需要 NFS 和 SMB 访问的应用程序。

数据复制和灾难恢复设置。

将冷数据分层到 S3。

⚙️ 功能：
支持 NFS、SMB 和 iSCSI。

内联压缩、重复数据删除和克隆。

快照、SnapMirror 复制。

将数据分层到 Amazon S3（冷存储）。

在混合云设置中效果很好。

##### 🟠 4. Amazon FSx for OpenZFS

✅ 专长：
基于 Linux 的 ZFS 文件系统，以数据完整性、快照和写时复制功能而闻名。

🧰 最适合：
需要高级文件系统功能的 Linux 工作负载。

受益于克隆和快照的开发/测试环境。

需要压缩、配额和低延迟存储的应用程序。

⚙️ 功能：
ZFS 功能：快照、克隆、校验和。

符合 POSIX 标准。

高吞吐量和 IOPS。

与基于 Linux 的系统集成。



有关这些文件系统，有许多相关的协议。有关其文件的访问，共享，和存储。



文件共享协议（通过网络）
这些协议允许多个客户端从中央服务器或设备访问文件。

1. **SMB（服务器消息块）**
🖥️ 主要使用者：Windows

⚙️ 提供：文件和打印机共享、网络驱动器

🛠 使用者：FSx for Windows File Server、FSx for NetApp ONTAP

🔐 支持文件锁定、身份验证（Active Directory）

📡 通过 TCP/IP 工作（通常为端口 445）

2. **NFS（网络文件系统）**
🐧 主要使用者：Linux/UNIX

⚙️ 提供：通过网络共享文件

🛠 使用者：Amazon EFS、FSx for NetApp ONTAP、FSx for OpenZFS

📡 通过 TCP/IP 运行（通常为端口 2049）

3. **iSCSI（Internet 小型计算机系统接口）**
🧱 用于：通过 IP 网络进行块级存储

💾 将远程存储视为客户端的本地磁盘

🛠 已使用作者：FSx for NetApp ONTAP

⚙️ 常见于 SAN（存储区域网络）

📡 用于通过 TCP/IP 传输 SCSI 命令的协议

💾 本地磁盘文件系统
这些定义了数据在物理或虚拟驱动器上的存储和结构。

4. **NTFS（新技术文件系统）**
🖥️ 原生于：Windows

🛠 使用者：FSx for Windows

📂 支持：文件权限、加密、压缩、日志记录

5. **ZFS（Zettabyte 文件系统）**
🐧 原生于：UNIX/Linux

🛠 使用者：FSx for OpenZFS

📂 支持：快照、克隆、校验和以确保数据完整性

⚙️ 写时复制机制（非常安全高效）

6. **Lustre**
🚀 高性能并行文件系统

🛠 使用者：FSx for Lustre

📂 针对以下应用进行了优化：HPC、ML、视频渲染、大规模计算作业

📡 符合 POSIX 标准（如 Linux 文件系统）

7. **ONTAP（NetApp 文件系统）**
🧩 专有企业文件系统

🛠 已使用作者：FSx for NetApp ONTAP

🧠 支持：多协议访问（NFS、SMB、iSCSI）、快照、克隆、数据分层



| Protocol   | Network or Local       | Best For              | Real Use                  |
| ---------- | ---------------------- | --------------------- | ------------------------- |
| **SMB**    | Network (file sharing) | Windows clients       | File servers, FSx Windows |
| **NFS**    | Network (file sharing) | Linux clients         | EFS, FSx ZFS/NetApp       |
| **iSCSI**  | Network (block-level)  | Raw disk access       | SANs, FSx ONTAP           |
| **NTFS**   | Local (file system)    | Windows OS            | Local drives, FSx Windows |
| **ZFS**    | Local/Shared           | Linux apps, snapshots | FSx for OpenZFS           |
| **Lustre** | Parallel (HPC)         | ML, big data          | FSx for Lustre            |
| **ONTAP**  | Hybrid (block + file)  | Enterprise storage    | FSx for NetApp            |



#### Storage Gateway：

如果你想把服务器内的存储空间和云端存储联通起来使用。可以使用storage gateway.通常这些gateway通常用于备份，数据分级等。

已知的gateway有S3 gateway，FSx gateway，Volume gateway，tape gateway(使用磁带作为备份)

![image-20250331155507261](C:\Users\msduser\Desktop\学习笔记\assets\image-20250331155507261.png)

同样的，在storage gateway里，你也可以租赁服务器。其作用为你的自己的服务器和AWS云之间的桥梁，用于运行gateway相关的软件。



##### AWS DataSync

一个很简单的功能，用于移动大量的数据，无论是从一个AWS服务转到另一个，还是从你自己的物理服务器到AWS的存储服务（反向也可以）。

对于AWS服务之间的转移，不需要AWS DataSync Agent，不过，对于物理服务器到AWS，需要此软件。