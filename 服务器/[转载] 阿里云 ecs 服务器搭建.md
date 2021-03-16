# 阿里云 ecs 服务器搭建

## 一、服务器搭建的网址入口：

### 有试用资格：

[点击进入阿里云云产品试用中心](https://free.aliyun.com/?spm=5176.19720258.J_2937333540.11.e9392c4akpDIuS&userCode=k6ji3ksd&scm=20140722.3079.7.2925)，选择下图产品，点击试用30天。

![img](https://uploader.shimo.im/f/ynfnkTqWllEL7gzJ.png!thumbnail)



### 无试用资格：

[点击进入ecs主页](https://www.aliyun.com/product/ecs?userCode=k6ji3ksd)，如下图点击立即购买，约花费0.08元.

![img](https://uploader.shimo.im/f/HsIxutgXNJqvLpZ7.png!thumbnail)





## 二、开始搭建：

> 试用的同学，请选择试用规格和任意镜像，然后跳转到第3步骤

### 1、选择基础配置

购买时，如图选择按量付费，选择cpu和内存：

![img](https://uploader.shimo.im/f/Xf8rK1GxHMPUavET.png!thumbnail)



如下图选择任意镜像+高效云盘40g，然后点击下一步

![img](https://uploader.shimo.im/f/2T83cZpjzwNCGfhZ.png!thumbnail)



### 2、选择网络和安全组

选择按使用流量 设置为80M ，点击确认订单。

![img](https://uploader.shimo.im/f/Q2xGUd9xVvHpZngB.png!thumbnail)



### 3、跳转到管理控制台页面

页面地址：[点击进入](https://ecs.console.aliyun.com/?spm=5176.8789780.J_1092585.3.620055caUeR2vh#/server/region/cn-beijing)，选择试用或购买的服务器地理位置，呈现效果如下图。

![img](https://uploader.shimo.im/f/TQfAFmDScBCyO1tD.png!thumbnail)





### 4、设置安全组

如图操作，进入安全组页面

![img](https://uploader.shimo.im/f/chP8Ljuz7bRMQpxs.png!thumbnail)

点击配置规则

![img](https://uploader.shimo.im/f/3CzAOysrObUiuTcd.png!thumbnail)

通过百度获取自己ip

![img](https://uploader.shimo.im/f/QxRQQtFGoWTNYgEV.png!thumbnail)



将8888端口号入口放开为你的ip（百度得到到ip）：

![img](https://uploader.shimo.im/f/xI8yCjvVYX13T4nD.png!thumbnail)

将80端口号放开入口：



![img](https://uploader.shimo.im/f/l45DqFV8dbOVUhhi.png!thumbnail)



### 5、重置操作系统和镜像

回到控制台页面，页面地址：[点击进入](https://ecs.console.aliyun.com/?spm=5176.8789780.J_1092585.3.620055caUeR2vh#/server/region/cn-beijing)。然后如图关闭服务器

![img](https://uploader.shimo.im/f/krT81E0KuPgloM57.png!thumbnail)





选择重置系统和镜像

![img](https://uploader.shimo.im/f/rBBpNEl5Sr2KZNZu.png!thumbnail)



如图点击从镜像市场选择操作系统

![img](https://uploader.shimo.im/f/H1Ny0ZKT7L3RliPf.png!thumbnail)

选择宝塔

![img](https://uploader.shimo.im/f/Ru6gUmyj7eRbXoFs.png!thumbnail)设置自定义密码并确认订单

![img](https://uploader.shimo.im/f/TWjlQmevkfRb6VKl.png!thumbnail)

### 6、链接服务器，获取宝塔初始信息

![img](https://uploader.shimo.im/f/Fej0llEmxAQwB4jg.png!thumbnail)



### 7、设置服务器环境

进入宝塔控制面板后 , 进入软件商店,搜索tomcat后点击安装，选择tomcat9版本

安装后, 如图搜索tomcat, 并设置端口号

![img](https://uploader.shimo.im/f/CSzaM37OaQx1lfeB.png!thumbnail)

![img](https://uploader.shimo.im/f/5lIBTSQN3qQ7XOLK.png!thumbnail)

![img](https://uploader.shimo.im/f/PSBhkHiD7pBg8qqA.png!thumbnail)

### 8、部署项目

如图点击进入tomcat安装的文件夹 , 并进入webapps文件夹

![img](https://uploader.shimo.im/f/Gbf10nqUrIi810s5.png!thumbnail)

将webapps中的ROOT文件夹内容清空，将我们的代码上传到ROOT