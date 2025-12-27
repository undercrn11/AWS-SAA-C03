#### 有关AWS EBS的个人理解与笔记



##### EBS（ Elastic Block Store）弹性块存储

当你创建了EC2 instance，AWS会默认给你建立一个EBS 卷。用于存储你的EC2 instance的os和其他数据。这个卷就叫做root volume.如果你把他删掉，EC2 instance会运行，但是，当下一次你把他停掉后再启动，EC2 instance会无法正常启动，报instance boot failure error。

EBS的特性之一是分离储存。当你创建一个EC2 instance，系统会分配给你一个EBS卷，这个卷的实际物理硬盘是随机存储在你当前可用区域的某个机柜中，或者多个机柜中，而这些机柜与EC2 instance所在的机柜是独立的。所以EC2 instance因为某种原因而down掉后，EBS里的数据得以安全保存。无论你是单个还是多个EC2 instance。或者说你选择了哪种placement group，EBS都与EC2 instance本体分离。但是，这种分离储存的本质，只是把数据分布储存在一个可用区域的设备中的不同硬盘上。除非，你手动设置它，让他可以在不同AZ下有复制的备份（只是备份）。而且，一个EBS卷只能被接入到一个虚拟机或一个数据库中（RDS instance）。



注释：当你有一个EBS 卷，这并不意味着你所有的数据都被放在同一个硬盘上。EBS是块的云储存。所以你的data可能在同一硬盘上，也有可能分布在多个硬盘的存储空间内。

当你增加EBS卷的存储容量时，有可能发生以下情况中的一种：

1.你存储数据的硬盘还有可用空间，那AWS会直接更改存储空间的分配，让你有权限可以使用更多存储空间。

2.如果你所在的硬盘没有足够的存储空间，AWS会在别的地方寻找足够的存储空间，然后将新的存储空间指向你的EC2 instance，或者直接将数据转移过新的存储空间。



当然EC2 instance还有一个储存设施叫EC2 instance store。这是一种临时的，高速储存。这个储存直接与EC2 instance所在的服务器连接。所以其性能表现是要优于EBS。当EC2 instance执行除了重启意外的其他动作（停止，删除），EC2 instance store里的数据会丢失。

当你在ubuntu控制台输入lsblk后，系统会输出当前EC2 instace所连接到的所有储存块的状况。以下是一个例子。

NAME     MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0      7:0    0 26.3M  1 loop /snap/amazon-ssm-agent/9881
loop1      7:1    0 73.9M  1 loop /snap/core22/1722
loop2      7:2    0 73.9M  1 loop /snap/core22/1748
loop3      7:3    0 44.4M  1 loop /snap/snapd/23545
xvda     202:0    0    8G  0 disk 
├─xvda1  202:1    0    7G  0 part /
├─xvda14 202:14   0    4M  0 part 
├─xvda15 202:15   0  106M  0 part /boot/efi
└─xvda16 259:0    0  913M  0 part /boot

一般来说，如果只有像loop0,loop1,xvda这样的名字，这意味着你的EC2 instance里没有 EC2 instance store。

以下是有关lsblk输出内容的解析：

| Column | Meaning |
| ------ | ------- |
|        |         |

| **NAME** | The name of the block device (disk, partition, or loop device) |
| -------- | ------------------------------------------------------------ |
|          | 名字                                                         |

| **MAJ:MIN** | The major and minor device numbers (used by the Linux kernel) |
| ----------- | ------------------------------------------------------------ |
|             | 设备的唯一id                                                 |

| **RM** | Removable flag (1 = removable, 0 = not removable) |
| ------ | ------------------------------------------------- |
|        | 可不可移除                                        |

| **SIZE** | Size of the device |
| -------- | ------------------ |
|          | 容量大小           |

| **RO** | Read-Only flag (1 = read-only, 0 = read/write) |
| ------ | ---------------------------------------------- |
|        | 读写权限                                       |

| **TYPE** | Type of device (`disk`, `part` for partition, `loop` for virtual devices) |
| -------- | ------------------------------------------------------------ |
|          | 存储块类型                                                   |

| **MOUNTPOINT** | Where the device is mounted in the filesystem |
| -------------- | --------------------------------------------- |
|                | 存储块被加载到文件系统的位置                  |

xvda是root EBS卷，内部包含os。loop1，loop2等是虚拟机的虚拟环回设备（用于 snap 包、ISO 挂载或者压缩文件系统）。

loop所在的文件路径的snap是ubuntu的一个文件管理器.这些loop是只读的。

在xvda下有个xvda14的文件夹是没有具体的被加载到文件系统内的路径。虽然它没有被加载到文件系统，但不意味着无用，通常来说，这个空间是用于EFI（Extensible Firmware Interface）可扩展固件接口之类的东西。即用来存放bios有关文件。或者AWS有时会预留一些空间用于其他用途。

**MAJ:MIN** (Major and Minor numbers) 是Linux kernel发给各个储存块的设备id。这些id有助于系统识别和管理不同种类的储存设备。

**Major Number (`MAJ`)** 用于标识用于控制设备的设备控制器。

Minor Number (`MIN`)用于标识被同一驱动器管理的多个单独存储器设备或分区（partition）

例如，在以上的输出中：

| **MAJ:MIN** | **Device** | **Explanation** |
| ----------- | ---------- | --------------- |
|             |            |                 |

| `7:0` | `loop0` | **Loop device** controlled by the loopback driver (`7`) |
| ----- | ------- | ------------------------------------------------------- |
|       |         |                                                         |

| `7:1` | `loop1` | Another **loop device**, assigned a different **minor number (1)** |
| ----- | ------- | ------------------------------------------------------------ |
|       |         |                                                              |

| `202:0` | `xvda` | **EBS root volume**, controlled by driver `202` |
| ------- | ------ | ----------------------------------------------- |
|         |        |                                                 |

| `202:1` | `xvda1` | Partition of `xvda` (same driver `202`, different **minor number (1)**) |
| ------- | ------- | ------------------------------------------------------------ |
|         |         |                                                              |



##### EBS Volume Types

| **Volume Type** | **Storage Type** | **Best For**                      | **Max Size** | **Max IOPS** | **Max Throughput** |
| --------------- | ---------------- | --------------------------------- | ------------ | ------------ | ------------------ |
| **gp3**         | SSD              | General-purpose workloads         | 16 TiB       | 16,000       | 1,000 MB/s         |
| **gp2**         | SSD              | General-purpose workloads         | 16 TiB       | 16,000       | 250 MB/s           |
| **io2**         | SSD              | High-performance databases        | 16 TiB       | 256,000      | 4,000 MB/s         |
| **io1**         | SSD              | Legacy high-performance workloads | 16 TiB       | 64,000       | 1,000 MB/s         |
| **st1**         | HDD              | Streaming and big data            | 16 TiB       | 500          | 500 MB/s           |
| **sc1**         | HDD              | Archival and cold storage         | 16 TiB       | 250          | 250 MB/s           |

在以上的EBS volume中，只有pg2,gp3,io2,io1可以作为root volume承载os。

其实EBS volume type主要就分三种。general purpose的gp2和gp3。为高i/o而准备的io2,io1.还有为低预算，或是为了数据备份而设的st1,sc1.

其中gp3算是最通用的型号，其特点是可以单独设置IOPS（每秒的i/o数量）和吞吐量

而gp2不行，其提高或降低IOPS和吞吐量的方式有且只有增加或减少此EBS卷的容量来实现。

io2和io1的独特点在于其可以使用EBS muiti-Attach。这个功能允许你将一个EBS卷连接到多个EC2 instance中。一个EBS 卷最多可以同时连接16个EC2 instance。这个功能主要用于高并发性的作业。

EFS(**Elastic File System**)弹性文件系统是一种由AWS管理的网络文件系统。其最大的特点是可以使多个EC2 instance连接到同一个EFS并实时共享文件。因其由AWS完全管理，所以会自动管理其储存用量，自动跟据使用量增加或减少。还有，其可以被不同可用区域的EC2 instance连接。如ap-northwest-1a,ap-northwest-1b,ap-northwest-1，三个可用区域（AZ）内的不同EC2 instance可以于同一区域(region)内的EFS连接。不同区域内的则无法直接连接。有一点需要注意，EFS是用于文件储存与共享，其延迟比较大，所以不适合用于实时系统，或数据库的存储部署等高I/O，需要低延迟的系统。



你在创建EC2 instance时可以选择装载efs.那么你的EC2 instance就会同时拥有两个存储空间，一个root EBS 卷，一个EFS。但有一点要注意的是，在创建EFS时，不要装载名字为efs-sg-1的security group.因为，当创建EC2 instance时，如果使用EFS，那么，AWS会自动帮你生成一个叫**instance-sg-1**的security group，在里面会引用一条**efs-sg-1**的security group，efs-sg-1也是实时生成的，所以如果已经创建了同名的security group，其会报错：The security group 'efs-sg-1' already exists for VPC 'vpc-xxxxxxxx'。

而且即便多个EC2 instance连接到了同一个EFS中，没有读写权限的话，是无法看到efs系统下的文件。所以在第一次登录中，需要检查用户是否有权限读写efs。

命令是：ls -l /mnt/efs

一般，第一次输出会是：-rw-r--r-- 1 ec2-user ec2-user  100 Feb 27 12:00 myfile.txt

其意思如下：

| **Field**      | **Example Value**      | **Meaning**                                                |
| -------------- | ---------------------- | ---------------------------------------------------------- |
| `-rw-r--r--`   | **File permissions**   | Owner can **read/write**, group & others can **only read** |
| `1`            | **Hard link count**    | Number of links to this file                               |
| `ec2-user`     | **File owner (User)**  | The Linux user who created the file                        |
| `ec2-user`     | **File owner (Group)** | The group that owns the file                               |
| `100`          | **File size (bytes)**  | The file is **100 bytes** in size                          |
| `Feb 27 12:00` | **Last modified date** | When the file was last changed                             |
| `myfile.txt`   | **File name**          | The name of the file                                       |

如果这个EC2 instance上的user没有权限访问efs文件夹，可以使用以下命令。

1. sudo chmod 777 /mnt/efs  这个命令可以让此EC2 instance上现有的所有用户，组都可以读写此路径下的文件。但不是很安全
2. sudo groupadd efs-users         sudo usermod -aG efs-users ec2-user1    sudo chown ec2-user:efs-users /mnt/efs/myfile.txt         sudo chmod 770 /mnt/efs/myfile.txt   以上命令是创建一个组，将用户加入这个组，然后把该文件夹的权限归到这个组下。在此组内的所有成员都可访问。此种方法最安全。
3. sudo chown nobody:nogroup /mnt/efs/myfile.txt
   sudo chmod 666 /mnt/efs/myfile.txt  此命令是把该文件夹的读写权限转移给匿名用户，所以所有EC2 instance都可以读写该文件夹下的内容
