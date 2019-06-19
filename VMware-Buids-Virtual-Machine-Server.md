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

## 5 docker 安装 MySQL

```
docker pull daocloud.io/library/mysql:5.7
```

参考 [Docker搭建MySQL服务](https://www.cnblogs.com/pwc1996/p/5425234.html)

通过国内镜像daocloud.io/library库下载，下载速度快到感人！（否则 timeout ，下载失败）

然后使用 Navicat 连接 MySQL 。