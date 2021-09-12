# VMware搭建虚拟机服务器

## 1 Linux 环境配置

1. 虚拟机安装 [VMWare](https://my.vmware.com/web/vmware/details?downloadGroup=WKST-1510-WIN&productId=799&rPId=33369)
    - [注册码](https://www.b2fun.net/archives/146)
2. CentOS 镜像安装 [CentOS-7-x86_64-Minimal-1810.iso](https://mirrors.aliyun.com/centos/7/isos/x86_64/)
3. 在虚拟机上按照正常步骤安装。
   - **注意**：虚拟机网络设置 最好选择 NAT模式！（折腾过 Bridged 不行）具体可参考[实战VMware的三种网络模式](https://www.aneasystone.com/archives/2015/04/three-network-modes-of-vmware-in-action.html)

## 2 测试网络

1. 显示网络信息 `vi /etc/sysconfig/network-scripts/ifcfg-XXX`
   - 唯一需要修改的是 ONBOOT=no 改为 ONBOOT=yes 
2. 重启服务 `service network restart`
3. 安装使用`ifconfig`命令 `yum install net-tools`
   - 之前可以使用`ip addr` 查看
4. 查看本地网络 `ifconfig`
    - lo 代表本地，主要关注 inet 192.168.147.128 。
    - 之前如果可以使用 yum install 命令，证明网络连通（之前使用桥接模式，一直失败。）
![linux_ifconfig_result](https://raw.githubusercontent.com/514723273/.md-Pictures/master/linux_ifconfig_result.png)

## 3 远程连接 SSH

SSH是英文Secure Shell的简写形式。通过使用SSH，你可以把所有传输的数据进行加密，这样"中间人"这种攻击方式就不可能实现了，而且也能够防止DNS欺骗和IP欺骗。使用SSH，还有一个额外的好处就是传输的数据是经过压缩的，所以可以加快传输的速度。SSH有很多功能，它既可以代替Telnet，又可以为FTP、Pop、甚至为PPP提供一个安全的"通道"。

### 3.1 安装 SSH 服务（若是无自带）

1. 安装SSH `yum install openssh-server`
2. 启动SSH `service sshd start`
3. 设置开机运行 `chkconfig sshd on`

### 3.2 安装 XSHELL

- [XSHELL 6 破解版](https://blog.csdn.net/u012062455/article/details/87189358) 下载更新后，即为免费版本。
- 新建会话，添加主机 192.168.147.128 ，输入 root 账号密码。

## 4 安装 docker

## 4.1 添加 Docker 的 yum 源

创建一个 /etc/yum.repos.d/docker.repo 文件，内容如下：
```
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/7/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
```

## 4.2 安装新版本 Docker

```
$ sudo yum install -y docker-engine
```

## 4.3 安装完后设置为系统开机自动启动服务

```
$ sudo systemctl enable docker.service
```
## 4.4 启动服务

```
$ sudo systemctl start docker
```

测试一下

```
docker info
```

## 4.5 卸载 Docker

若是有需要

```
sudo yum erase docker docker-common docker-client docker-compose
```

## 5 docker 安装 Mysql

参考 [Docker搭建MySQL服务](https://www.cnblogs.com/pwc1996/p/5425234.html)

### 5.1 拉去 Mysql 镜像

通过国内镜像 daocloud.io/library 库下载（下载速度快到感人！）（否则 timeout ，下载失败）
```
docker pull daocloud.io/library/mysql:5.7
```
拉取成功后，使用 `sudo docker images` 查看。

### 5.2 创建并启动一个 Mysql 容器

```
sudo docker run --name yqb-mysql -e MYSQL_ROOT_PASSWORD=123456 -p 3306:3306 -d mysql:5.7
```

（一定要指定版本 :5.7 ，不然 NaviCat 连接的时候，会报错，说是 需要提升 Mysql 版本。）

- –name：给新创建的容器命名，此处命名为 yqb-mysql
- -e：配置信息，此处配置 mysql 的 root 用户的登陆密码
- -p：端口映射，此处映射主机 3306 端口到容器 yqb-mysql 的 3306 端口
- -d：成功启动容器后输出容器的完整 ID
- 最后一个 mysql:5.7 指的是 mysql 镜像名字

查看容器运行状态 `sudo docker ps`

### 5.3 使用本地 Navicat 连接 Mysql

Navicat 下载：[Navicat for MySQL 安装和破解（完美）](https://blog.csdn.net/WYpersist/article/details/79834490)

连接MySQL前需要防火墙开放端口或者关闭防火墙。（没有操作此步骤依旧可行）

开放端口：
```
sudo firewall-cmd --add-port=3306/tcp
```
关闭防火墙：
```
sudo systemctl stop firewalld
```

使用 Navicat ，输入正确的 ip 端口 用户名 密码 即可连接成功。

### 5.4 docker 内使用 mysql

```
# 在容器 yqb-mysql 中开启一个交互模式的终端
docker exec -ti XXX /bin/bash 
```
- XX  为启动的 mysql id
- -t  分配一个伪终端
- -i  即使没有附加也保持STDIN 打开

其他操作就和平时的 mysql 一致。

`exit` 退出 mysql ，再 `exit` 退出容器。 


## 附录

## docker 命令

1. 查看所有容器（运行或者不运行） `docker ps -a`
2. 停止容器 `docker stop id`
   - 停止所有的 container `docker stop $(docker ps -a -q)`
3. 删除容器 `docker rm id`
   - 删除所有的停止容器 `docker rm $(docker ps -a -q)`