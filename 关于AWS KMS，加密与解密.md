#### 关于AWS KMS，加密与解密的高级内容



关于云端加密，解密的常见形式：

1.Encryption in flight：客户端在传输文件前使用TLS/SSL进行加密，加密后的文件被传输给服务器，然后服务器在收到文件后，使用其自己有的密钥进行解密。

2.Server-side encryption at rest:  其实就是为了保证数据在数据库的安全性，数据在进入数据库前，会被加密，然后当此份数据被发送到外部网络中前，会被解密。

3.client-side encryption：数据会在一个客户端被加密，然后传到数据库，在数据库中不会被解密，而是被另外一个客户端收到时，在客户端解密。





在AWS KMS中，可以选择两种密钥形式，一种是对称密钥（加密和解密用同一个密钥），一种是非对称密钥（加密和解密使用不同的密钥）。

对称密钥所使用的是AES-256-GCM加密算法（目前只有一种）。这些密钥由KMS直接管理，且不会暴露于公众之中。其在以下API中被使用：Encrypt，Edcrypt，GenerateDataKey，GenerateDataKeyWithoutPlaintext。

非对称密钥则有多种加密解密算法：RSA_2048,RSA_3072,RSA_4096.而用于数字签名的算法为ECC_NIST_P256,ECC_SECG_P256K1



#### 用户自己创建KMS Key（Customer Managed Keys）：

在KMS Console里，选择创建Key时都多种参数选择，第一种为对称加密用的Key，第二种为非对称加密用的Key。你可以用这个KEY作为普通的加密，解密使用，或者选择另一种被称为

Generate and verify MAC的选项。MAC = 真实性和完整性（证明数据未被更改）。当你选择这个选项时，生成的KEY将用于验证数据的真实性和完整性。其通常被用于私密API Call，会话token验证，微服务通信间的数据检测等。其工作流程大致为：当你在KMS上Call一个叫GenerateMac的API，并发给它一条信息（json），KMS会返回一个MAC的Tag。然后你就可以将这个Tag和你的信息发给另一个服务。然后收到的服务会使用VerifyMac的API来验证其数据完整性。输入原本的信息加上MAC Tag，KMS会返回True或者False。 MAC = HMAC(secret_key, message)。

**请注意，**当你选择对称加密和KEY的用途为加密，解密时，是不会有Key spec的选项出来的，这个选项是让你选择需要用的算法。但是在选择MAC选项是会有，因为在此种应用场景下，有多种算法可供选择。如HMAC_256,HMAC_512,HMAC_224,HMAC_384。其实这些算法就是HMAC加上SHA算法。

另外，做完以上设置后，还需要给这个key设定合适的permission，让其可以被其他服务调用。



#### KMS Key种类：

除了上面所说的Customer Managed Key，KMS还有大量的由AWS自行创建并维护的KMS Key。

AWS自己管控的Key有三种：SSE-S3，SSE-SQS，SSE-DDB（默认情况下）。这些key是免费的，如：aws/rds，aws/ebs。而Customer Managed Key需要收每个月1美元，加上每10000次CALL 0.03美金的API CALL收费。

而自动密钥轮换（Automatic Key rotation）对于AWS自己的key来说，密匙轮换是自动的，每一年一次。而对于Cutomer-managed Key来说，你需要启用自动轮换才行。对于外部导入的KMS key，你只有使用alias手动轮换这种选择。



##### 如何将加密的快照转移到另一个区域（region）：

举例：假设你有一个在us-east-1上使用KMS CMK加密的EBS快照。你需要将其copy到us-west-2.

因为KMS Key（你的CMK）是单区域性的，所以你无法使用us-east-1的key来解密us-west-2的加密快照。

所以有两种选择：1.使用muti-Region CMK。这样的话这个加密的Key就会同时出现在两个区域内的KMS中，解决了无法在另一区域解码快照的问题。其实所谓的muti-region key就是将这个区域的Key，复制到另一个区域。这两个key有相同的Key materical（加密解密过程中被AES256算法实际使用的256bit的key）,相同的KeyID（因为启用了muti-region-key,所以其会显示MultiRegionKeyId，两个key在此是完全相同的）。所以从密码学角度来说，这两个Key是相同的key（因为使用了相同的Key materical）。但是这两个Key的ARN，Permission，Policies都是不同的。所以在系统层面上，这两个Key是不同的Key（ARN不同）。这些key还是区域性的，所以只能在本区域进行相关操作。

对于使不使用muti-region-key，将快照，或别的什么文件转到其他区域的流程基本是一样的。首先，都需要使用本区域的CMK解压加密的快照，然后将快照发送到目标区域（使用TLS），然后再使用目的地的CMK加密快照或文件。

所以，选择muti-region-key的意义是什么?1.便于追踪，两边的Key的Key ID是相同的，所以容易追踪Key的使用。2.便于配置policy，你可以使用同一个Key ID来配置不同区域的policy。3.对于金融，医疗等行业，可以减少或消除相关程序的修改。

请注意：IAM policy，cloudTrail log是区域性的。复制了Key到另一个区域，需要重新配置。



##### 关于KMS Policies

就像S3 bucket policies一样。KMS也有自己的policies.其作用和S3 bucket policies作用相同。用于控制谁可以访问并使用自己的服务。在KMS上即为，谁可以访问KMS，查看KMS Key并使用特定或多个KMS Key用于加密或解密。

如果你在创建KMS Key时不特别设置，默认的KMS Key Policies将会被创建。这个Key Policies将会给予整个AWS账号所有接入权限。而对于Customer KMS Key Policies，则你需要定义那些users，roles可以访问这个Key，然后谁可以管理这个Key（在跨账户访问时有用）

KMS Policies实例：

![image-20250624152548514](C:\Users\msduser\Desktop\学习笔记\assets\image-20250624152548514.png)

此KMS Policies的意义是：

1. **`"Sid": "Allow use of the key with destination account"`**
   - `Sid`（Statement ID）： 这是这条语句的标识符，用于方便人类阅读和理解。这里清楚地表明这条语句的目的是允许目标账户使用该密钥。
2. **`"Effect": "Allow"`**
   - 这是最关键的部分，表明这条语句是**授权**（Allow）指定的主体执行指定的操作。
3. **`"Principal": {"AWS": "arn:aws:iam::TARGET-ACCOUNT-ID:role/ROLENAME"}`**
   - **主体（Principal）：** 指定了谁被授予权限。
   - `"AWS": "arn:aws:iam::TARGET-ACCOUNT-ID:role/ROLENAME"`： 这是一个 **IAM 角色的 ARN（Amazon Resource Name）**。它明确地指出权限被授予给账户 `TARGET-ACCOUNT-ID` 中名为 `ROLENAME` 的 IAM 角色。
   - **含义：** 只有扮演 `TARGET-ACCOUNT-ID` 账户中 `ROLENAME` 这个 IAM 角色的实体（例如 EC2 实例、Lambda 函数、或该角色被代入的用户）才能利用这个策略语句的权限。
4. **`"Action": ["kms:Decrypt", "kms:CreateGrant"]`**
   - **操作（Action）：** 指定了被允许的具体操作。
   - `kms:Decrypt`： 允许使用该 KMS 密钥来**解密**被其加密过的数据。这是最核心的权限。
   - `kms:CreateGrant`： 允许创建 **KMS Grant（授权）**。Grant 是一种机制，允许委托权限（例如 `kms:Decrypt`）给其他 AWS 主体（例如同一目标账户内的另一个 IAM 角色、EC2 实例、Lambda 函数等），而无需修改密钥策略本身。这在目标账户内部授权访问该密钥时非常有用。
5. **`"Resource": "\*"`**
   - **资源（Resource）：** 指定权限应用在哪个资源上。
   - `"*"`： 这是一个通配符，表示权限应用于**当前 KMS 密钥策略所附加的 KMS 密钥本身**。在 KMS Key Policy 中，`Resource` 通常设置为 `"*"`，因为策略是直接附加到密钥上的，它天然地作用于该密钥。
6. **`"Condition": { ... }`**
   - **条件（Condition）：** 这是对权限施加的额外限制。即使主体和操作匹配，也必须满足这些条件，权限才会生效。这里有两个重要的条件，它们共同构成了强大的安全约束：
   - **`"StringEquals": { "kms:ViaService": "ec2.REGION.amazonaws.com" }`**
     - 这个条件要求 KMS 的 API 调用必须是**通过特定的 AWS 服务端点**发起的。
     - `kms:ViaService`： 这是一个特殊的 KMS 条件键，表示**哪个 AWS 服务代表调用者联系了 KMS**。
     - `"ec2.REGION.amazonaws.com"`： 这指定调用必须是通过 **Amazon EC2 服务**在特定的 **AWS 区域（REGION）** 发出的。这意味着：
       - 调用必须源自 EC2 实例（例如，实例使用存储在加密 EBS 卷上的数据，或者访问加密的实例存储）。
       - 调用必须发生在指定的 `REGION`（例如 `us-east-1`, `ap-southeast-2`, `eu-central-1`）。你需要用实际的区域代码替换 `REGION`。
     - **含义：** 目标账户中的 `ROLENAME` 角色只能在其附加到的、运行在指定 `REGION` 的 **EC2 实例**上使用这个密钥进行解密或创建 Grant。它不能直接从某个应用程序、Lambda 函数或其他服务（如 S3）调用 KMS 进行解密（除非那些服务最终代表该 EC2 实例调用 KMS）。
   - **`"StringEquals": { "kms:CallerAccount": "TARGET-ACCOUNT-ID" }`**
     - 这个条件要求 KMS API 调用必须**由指定的 AWS 账户直接发起**。
     - `kms:CallerAccount`： 这个条件键标识了**最终调用 KMS API 的 AWS 账户 ID**。
     - `"TARGET-ACCOUNT-ID"`： 这里再次指定了目标账户 ID（需要与 Principal 中的账户 ID 一致）。
     - **含义：** 确保解密或创建 Grant 的请求**直接**来自于 `TARGET-ACCOUNT-ID` 账户本身。这防止了目标账户中的某个实体（拥有 `ROLENAME` 权限）再将权限委托给第三个账户（或其他主体）来使用这个密钥。请求必须源自目标账户内部。



这条 KMS Key Policy 语句实现了以下精细化的权限控制：

1. **允许谁？** 允许 `TARGET-ACCOUNT-ID` 账户中名为 `ROLENAME` 的 IAM 角色。
2. **允许做什么？**
   - 使用该 KMS 密钥解密数据 (`kms:Decrypt`)。
   - 为该密钥创建授权 (`kms:CreateGrant`)，以便在目标账户内部进一步委托解密权限。
3. **在什么条件下？**
   - **调用必须来自 EC2 服务 (`kms:ViaService = ec2.REGION.amazonaws.com`)**： 权限只能在附加了 `ROLENAME` 角色的、运行在指定 `REGION` 的 EC2 实例上使用（例如访问加密的 EBS 卷）。
   - **调用必须直接来自目标账户 (`kms:CallerAccount = TARGET-ACCOUNT-ID`)**： 确保是目标账户自己在使用权限，防止权限被进一步传递到其他账户。

##### 通俗解释：

这里会有KMS：CreateGrant这个API的原因是因为，你去使用EC2的服务，EC2需要解密加密了的EBS快照，这时，调用KMS：descrypt这个API的是EC2，而不是你（那个IAM Role），但EC2本身是没有权限去解密快照的，所以其要先调用CreateGrant去给自己授权。让自己有权限可以使用KMS：decrypt这个API去解密快照。



#### KMS RMK和DynamoDB Table的联动，加密与解密

因为普通的KMS Key是区域隔离的。所以只能在单一区域使用。

DynamoDB Global Table是多区域的，它会跨区域复制数据，然后让数据保持多区域同步。但是如果你启用了加密，但没有启用MRK时，当DynamoDB，从一个区域复制数据去另一个区域时，会因为在新区域内找不到密钥而解密失败。导致无法跨区域同步数据。所以，需要启动KMS的MRK**多区域密钥**

### **实现步骤**

#### **阶段 1：创建并配置 KMS MRK**

1. **创建主 MRK**

   - 在 **主区域**（如 `us-east-1`）创建 KMS 密钥，启用 **Multi-Region key** 选项。

   ```
   # AWS CLI 示例
   aws kms create-key --region us-east-1 --multi-region --description "DynamoDB Global Table MRK"
   ```

2. **创建 MRK 副本**

   - 在 **其他副本区域**（如 `eu-west-1`, `ap-northeast-1`）创建 MRK 副本。

   ```
   aws kms replicate-key --region eu-west-1 \
     --key-id mrk-1234abcd12ab \  # 主 MRK ID
     --replica-region eu-west-1
   ```

3. **授权 DynamoDB 使用 MRK**

   - 在每个区域的 MRK 密钥策略中，添加 DynamoDB 服务的访问权限：

     json

     ```
     {
       "Sid": "Allow DynamoDB service access",
       "Effect": "Allow",
       "Principal": {"Service": "dynamodb.amazonaws.com"},
       "Action": [
         "kms:Encrypt",
         "kms:Decrypt",
         "kms:GenerateDataKey*",
         "kms:ReEncrypt*"
       ],
       "Resource": "*"
     }
     ```

------

#### **阶段 2：配置 DynamoDB Global Table 加密**

1. **创建主表（主区域）**

   - 创建 DynamoDB 表时，指定该区域的 **MRK ARN** 作为加密密钥：

     bash

     ```
     aws dynamodb create-table \
       --region us-east-1 \
       --table-name MyGlobalTable \
       --attribute-definitions ... \
       --key-schema ... \
       --sse-specification Enabled=true, SSEType=KMS, KMSMasterKeyId=arn:aws:kms:us-east-1:123456789012:key/mrk-1234abcd12ab
     ```

2. **添加副本区域**

   - 添加副本时，**必须指定该副本区域的 MRK ARN**：

     bash

     ```
     aws dynamodb update-table \
       --region us-east-1 \
       --table-name MyGlobalTable \
       --replica-updates '
         {
           "Create": {
             "RegionName": "eu-west-1",
             "KMSMasterKeyId": "arn:aws:kms:eu-west-1:123456789012:key/mrk-1234abcd12ab"
           }
         }'
     ```

   - **关键点**：

     - 每个副本必须使用**本区域的 MRK 副本 ARN**（如 `eu-west-1` 用 `eu-west-1` 的 ARN）。
     - 虽然 Key ID 相同（`mrk-1234abcd12ab`），但 ARN 中的区域必须匹配副本所在区域。



### **关键注意事项**

1. **跨区域解密权限**
   - MRK 副本**自动具备跨区域解密能力**（无需额外配置策略）。
   - 确保每个区域的 DynamoDB 服务有权限访问**本区域的 MRK 副本**（通过密钥策略）。
2. **密钥轮转**
   - 主 MRK 轮转时，所有副本自动同步新密钥材料。
   - 旧数据仍可用历史密钥材料解密（KMS 自动管理多版本）。
3. **避免跨区域 KMS 调用**
   - 若误用其他区域的 MRK ARN（如 `eu-west-1` 副本使用 `us-east-1` 的 ARN），会导致：
     ❌ 跨区域 KMS 调用 → 延迟增加 + 额外费用。
     ❌ 违反数据本地化合规要求。
4. **与单区域密钥的区别**
   - 若使用单区域密钥（非 MRK），Global Table 会强制所有副本使用**相同密钥 ARN**，导致：
     ❌ 副本区域无法解密（因密钥不存在于该区域）。
     ❌ 必须通过跨区域 KMS 调用解密 → 高延迟+高成本+可能失败。





