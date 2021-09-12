# 阿里巴巴自研 PolarDB 使用

## 步骤如下

### 1、进入官网

[点击进入](https://www.aliyun.com/product/polardb?spm=5176.19720258.J_8058803260.152.e9392c4aqBO3L7&userCode=k6ji3ksd)

### 2、开始搭建

> 先登录账号，然后如下图点击购买

![img](https://uploader.shimo.im/f/12Rl5K5joETGfwCX.png!thumbnail)

### 3、调整配置

> 新用户免费试用2核8G一个月，如图点击更换节点规格

![img](https://uploader.shimo.im/f/1reG9lO0CMcGvnEy.png!thumbnail)



### 4、勾选服务协议

> 勾选服务协议，点击去支付，进行0元试用。

![img](https://uploader.shimo.im/f/0TKNgHc6HQ3xx82q.png!thumbnail)

### 5、集群搭建成功

> 点击管理控制台，开始使用。注意：开通成功需要等待10-15分钟

![img](https://uploader.shimo.im/f/Av8T97cfpnWaOseE.png!thumbnail)

### 6、进入控制台

> 如图切换到地区控制台，即可观察到 #创建中# 的数据库集群列表

![img](https://uploader.shimo.im/f/TzFxcOdTgJHqj44J.png!thumbnail)

### 7、设置集群白名单

> 需要设置集群的IP白名单，并创建集群的初始账号，才能连接和使用该集群

1. 如图单击集群ID。

![img](https://uploader.shimo.im/f/NMZlmZ3pP6QvxdLO.png!thumbnail)



1. 在左侧导航栏，单击**配置与管理 > 集群白名单**。

![img](https://uploader.shimo.im/f/OECB1N9q7dOPaUUF.png!thumbnail)

1. 在**集群白名单**页面，您可以**新增IP白名单分组**或**配置**已有白名单。

![img](https://uploader.shimo.im/f/de68N8hEbAi73MlR.png!thumbnail)

1. 单击**新增IP白名单分组**。

在**新增IP白名单分组**面板，输入分组名称和允许访问的IP白名单地址。，ip获取方式通过下图百度![img](https://uploader.shimo.im/f/LRWkjZDUWFrFsT7e.png!thumbnail)

自己ip的获取方式如图：

![img](https://uploader.shimo.im/f/EtTwW8GqabrDKeGW.png!thumbnail)



### 8、设置集群安全组

在第7步的**集群白名单**页面，单击**选择安全组**或目标安全组**操作**栏中的**配置**按钮来更改安全组设置。

![img](https://uploader.shimo.im/f/mAmKLyUao4m8v5nO.png!thumbnail)

在弹出的**选择安全组**面板，选中目标安全组，单击**确定**即可。

![img](https://uploader.shimo.im/f/hzBWsV2KbKXVeNMG.png!thumbnail)

### 9、创建数据库高权限账号

1. 回到[PolarDB控制台](https://polardb.console.aliyun.com/)。

1. 在控制台左上角，选择集群所在地域。

1. 找到目标集群，单击集群ID。

1. 在左侧导航栏中，选择**配置与管理 > 账号管理**。

1. 单击**创建账号**。

1. 在**创建账号**面板，设置以下参数后点击确定：

![img](https://uploader.shimo.im/f/D4X232LBlIkt2QQ4.png!thumbnail)



![img](https://uploader.shimo.im/f/oDQV0GmE54rXu3Ne.png!thumbnail)



### 10、了解链接类型（此步骤阅读即可，无需操作）

> 支持主地址链接和集群地址（建议）链接，区别如下

#### 集群地址和主地址

![img](https://uploader.shimo.im/f/inIDLyaVGlO2FtSA.png!thumbnail)

| 地址类型         | 地址说明                                                     | 支持的网络类型           |
| ---------------- | ------------------------------------------------------------ | ------------------------ |
| 集群地址（推荐） | 应用程序只需连接一个集群地址，即可连接到多个节点。带有读写分离功能，写请求会自动发往主节点，读请求会自动根据各节点的负载发往主节点或只读节点。 **说明** PolarDB包含一个默认的集群地址，您还可以根据业务需求创建多个自定义的集群地址，自定义集群地址可以连接到指定的节点，以及设置读写模式等。详情请参见[修改和释放集群地址](https://help.aliyun.com/document_detail/115494.html)。 | 私网公网私网（经典网络） |
| 主地址           | 总是连接到主节点，支持读和写操作。当主节点发生故障时，主访问地址会自动切换到新的主节点。 |                          |





#### 私网地址和公网地址



| 网络类型         | 说明                                                         | 使用场景                                                     |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 私网             | 通过私网的连接地址访问可以发挥PolarDB的最佳性能。创建集群时会默认生成一个私网的连接地址，该地址支持修改但无法释放，关于如何修改，请参见[修改连接地址和端口](https://help.aliyun.com/document_detail/173794.html)。 | 例如：ECS与数据库集群位于同一VPC，那么ECS可以通过私网地址访问数据库集群。使用DMS通过VPC访问数据库集群 |
| 公网             | 您可以申请或释放公网的连接地址，详情请参见[申请连接地址](https://help.aliyun.com/document_detail/68510.html?spm=a2c4g.11186623.2.6.6f4e6252MP3EHM#section-riw-i95-90u)和[释放连接地址](https://help.aliyun.com/document_detail/173794.html)。公网即因特网，通过公网访问将无法实现PolarDB最佳性能。 | 例如：通过公网访问数据库集群进行维护操作。                   |
| 私网（经典网络） | 您可以申请或释放私网（经典网络）的连接地址，详情请参见[申请连接地址](https://help.aliyun.com/document_detail/68510.html?spm=a2c4g.11186623.2.6.6f4e6252MP3EHM#section-riw-i95-90u)和[释放连接地址](https://help.aliyun.com/document_detail/173794.html)。集群之间不通过网络进行隔离，只能依靠集群自身的白名单策略来阻挡非法访问，详情请参见[设置集群白名单](https://help.aliyun.com/document_detail/68506.html)。 | 只有当业务处于经典网络中时，才能通过私网地址（经典网络）访问数据库集群。 |

### 11、申请公网地址

1. 登录[PolarDB控制台](https://polardb.console.aliyun.com/)。

1. 在控制台左上角，选择集群所在地域。

1. 找到目标集群，单击集群ID。

1. 在**基本信息**页面的**链接地址**区域，单击右上角![img](https://uploader.shimo.im/f/1W4yayMny1rGuYvV.png!thumbnail)图标切换视图。

单击**申请**。

![img](https://uploader.shimo.im/f/W6DLpDxl6Xj4M9GO.png!thumbnail)

在弹出的对话框中，设置连接地址前缀，单击**确定**。

### 12、使用DMS连接集群

DMS是阿里云提供的图形化的数据管理工具，它是一种集数据管理、结构管理、访问安全、BI图表、数据趋势、数据轨迹、性能与优化和服务器管理于一体的数据管理服务。支持对关系型数据库（MySQL、SQL Server、PostgreSQL等）和NoSQL数据库（MongoDB、Redis等）的管理，同时还支持Linux服务器管理。

1. 登录[PolarDB控制台](https://polardb.console.aliyun.com/)。

1. 在控制台左上角，选择集群所在地域。

1. 找到目标集群，单击集群ID。

1. 在**基本信息**页面右上角，单击**登录数据库**。![img](https://uploader.shimo.im/f/J3Zag1eUDQ45jC4o.png!thumbnail)

在弹出的对话框中，输入PolarDB MySQL集群中创建的**数据库账号**和**数据库密码**，单击**登录**。![img](https://uploader.shimo.im/f/RPcye68QOKKzcBgS.png!thumbnail)

1.  登录后，点击去授权，进行授权

![img](https://uploader.shimo.im/f/mmIEF8k1Xq77SqjG.png!thumbnail)



![img](https://uploader.shimo.im/f/weYDDJxrwHxe6D1T.png!thumbnail)



![img](https://uploader.shimo.im/f/UE58a3tgCkmyXPee.png!thumbnail)



1. 单击**已登录实例**。

在**已登录实例**列表中，单击目标集群名称，找到并双击目标数据库名称，即可切换到目标数据库进行管理。

![img](https://uploader.shimo.im/f/cN5WZqrd2BhBwzxr.png!thumbnail)

### 13、创建数据库

> 右键已登陆实例名称，选择数据库管理

![img](https://uploader.shimo.im/f/9VWAnEHQF7PpeKXu.png!thumbnail)



> 点击创建库

![img](https://uploader.shimo.im/f/8HWtSuTEixoO9Sjw.png!thumbnail)

> 输入数据库名称，点击确定

![img](https://uploader.shimo.im/f/NAMhmL8DgG1ScjR1.png!thumbnail)

### 14、向新创建的数据库中导入数据

导入发放的SQL文件即可