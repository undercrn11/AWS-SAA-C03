#### 有关 AWS IAM(Identity and Access Management)的理解

IAM是用来管理使用AWS服务的用户及其权力的服务。

可以把整个AWS的服务想象成一个可以被拥有的，可以被使用的物体。

Root Account是整个AWS的服务的拥有者。他可以使用任何AWS的服务，他也可以把部分使用的权力分给其他人使用。这些获得Root Account授权，可以使用部分AWS服务，并可以对其进行管理的人，是IAM User.Root Account可以随时改变IAM user使用的服务（能否使用），和其在此中的权力范围（可以干什么，不可以干什么）。IAM的大部分功能是与此相关的。

首先，Groups是受Root Account管理的下辖组织，其可以有一个或多个。这个东西的本质意义是特定权力的载体。你（IAM User）进入了某个组织（Groups），你就获得了行使某种权力的能力（当然，IAM User一开始是没有任何权力的，所以他什么都干不了）。一个人可以进入多个组织（有点像会员，去多个商店，成为每一个商店的会员，但人只有你一个）。当然，IAM User可以不属于任何组织（单飞）。

说完IAM User。说一下IAM Permission。就像刚才说的，IAM User需要权力，而且其自身可以不加入任何组织，所以。IAM Permission是可以分给个人（IAM User）或者组织（Groups）。Permission，字如其名，就是权力的规定与限制。规定某人，某组织可以，不可以干什么。（一个组织在被创立的初始，也是什么都干不的）。

所以，需要IAM permission以来下发权力。授予IAM Permission有两种方法。第一种，通过IAM Console（GUI）的方式授予。具体流程为：IAM Console => 选择User或Role或Groups =>点击permission,添加permission.然后选就行，或者自己创建一个独一的Policy。第二种为使用授权文件（JSON格式）。

实例：

{
"Version": "2012-10-17",
"Statement": [
{
"Effect": "Allow",
"Action": "ec2:Describe*",
"Resource": "*"
},
{
"Effect": "Allow",
"Action": "elasticloadbalancing:Describe*",
"Resource": "*"
},
{
"Effect": "Allow",
"Action": [
"cloudwatch:ListMetrics",
"cloudwatch:GetMetricStatistics",
"cloudwatch:Describe*"
],
"Resource": "*"
}
]
}

结构样板:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow" or "Deny",
            "Action": "service:action",
            "Resource": "arn:aws:service:region:account-id:resource",
            "Condition": { ...optional... }
        }
    ]
}

使用方法：IAM Console =>POlicies=>Create Policy,转到json editor.把代码拷到上面,保存然后把这个自定义的policy套到IAM User,group,role上。

json文件的结构

version:policy language的版本,目前为2012-10-17,

id;此policy的id

Statement:关于policy的具体内容

Statement之下包含以下内容:1.sid:statement的id,2.effect:此声明的类型（允许或禁止）=>(Allow, Deny)

3.principal:此声明作用的对象,4.action:声明内容(允许做什么，禁止做什么),5.resource:基于用户或对象的资源（用于定义用户执行允许操作时，可访问并使用的资源，数据）6.Condition:在什么情况下该条声明生效。



##### IAM Password Policy:

在IAM里，root account可以给密码设定一个规则，以增强用户的使用安全性。

你可以做如下设置：1,最低长度,2.是否需要大小写字母，数字，符号 3.是否允许IAM user更换其密码. 4.要求IAM User每隔一段时间就换一次密码.5.不允许重复使用密码。



##### MFA(Multi Factor Authentication)

多重验证，不多解释，使用一个第三方认证软件或物理密钥来进行二重验证（在输入密码后）。IAM User和Root account都可以使用。root account必须要有MFA。IAM User可有可不有。

添加MFA流程如下：

1.AWS Management Console => IAM => User => IAM User => Security credentical => MFA 

关于MFA你也可以设置一个policy来让IAM user强制使用MFA

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Action": "*",
            "Resource": "*",
            "Condition": {
                "BoolIfExists": {
                    "aws:MultiFactorAuthPresent": "false"
                }
            }
        }
    ]
}此设置的意义是让IAM User强制使用MFA，否则无法干任何事。



关于如何访问AWS，有以下三种方式。

1.AWS Management Console（网页登陆）

2.AWS Command Line Interface（CMD打命令）

3.AWS Software Development Kit（打代码）

能用网页登录是最好的，如果不行，用CLI。CLI需要通过access key和secret access key登录，可以使用自己电脑的cmd或powershell（需要下载AWS CLI，然后在cmd或powershell上设置身份验证）。SDK在一般情况下无需使用，除非你要在云端部署自设开发的APP。



AWS SDK支持以下语言的编程：

• SDKs (JavaScript, Python, PHP, .NET, Ruby, Java, Go, Node.js,
C++)
• Mobile SDKs (Android, iOS, …)
• IoT Device SDKs (Embedded C, Arduino, …)

还有一个比较重要的东西叫IAM Role

这个东西和IAM User很相似。但他不是作用于真正的user，而是用于AWS service。

举个例子就是。你的AWS中，有一个服务需要和另一个服务做交互（传送数据，计算，拿资料等）。所以你需要授权给这个服务，允许让他访问另一个服务。所以，IAM role本质上是一种授权的对象化。我把这个授权整成一个role，然后把他套在需要的服务上，就行了。

常用的role：

• EC2 Instance Roles
• Lambda Function Roles
• Roles for CloudFormation

IAM Security tool

1.IAM Credential report(account-level):

用于列出所有user的账户状态和其他细节

2.IAM Access Advisor(user-level):

用于展示IAM user有的权限和权限过期日期。