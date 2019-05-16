# Config CentOS7

## 1.源

### 1.1.备份旧的源

```shell
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

### 1.2.更改源为阿里云

```shell
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

or

```shell
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

### 1.3.生成缓存

```shell
yum makecache
```

### 1.4.安装其他的源

```shell
# 安装remi的源
yum install remi-release
# CentOS 6 / RHEL 6
yum install http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
# CentOS 7 / RHEL 7
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm

#安装epel源
yum install epel-release
# 或 epel-release-7-11.noarch.rpm
yum install http://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm
```

## 2.网络

### 2.1.net-tools工具

net-tools工具包含常用`ifconfig`命令

#### 2.1.1.查询net-tools工具

```shell
yum search net-tools
```

#### 2.1.2安装net-tools

```shell
yum install net-tools
```

### 2.2.静态IP

#### 2.2.1.查看源网卡设置

```
cat /etc/sysconfig/network-scripts/ifcfg-eth0
```

#### 2.2.2.备份源网卡设置

```
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="dhcp"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="eth0"
UUID="a5fde2de-0e17-433d-bf6d-8900ee33ab58"
DEVICE="eth0"
ONBOOT="yes"
```

#### 2.2.3.修改IP

```shell
nmtui edit eth0
```

#### 2.2.4.当前网卡设置

```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eth0
UUID=a5fde2de-0e17-433d-bf6d-8900ee33ab58
DEVICE=eth0
ONBOOT=yes
IPADDR=192.168.1.66
PREFIX=32
GATEWAY=192.168.1.1
DNS1=114.114.114.114
DNS2=114.114.115.115
```

### 2.3.防火墙

#### 2.3.1.查看防火墙状态

```shell
firewall-cmd --state
```

#### 2.3.2.防火墙重载

```shell
firewall-cmd --reload
firewall-cmd --complete-reload
```

#### 2.3.3.开放端口或服务

开放端口

```shell
# 开放80端口
firewall-cmd --zone=public --add-port=80/tcp
# 永久开放80端口
firewall-cmd --permanent --zone=public --add-port=80/tcp
```

开放服务

```shell
# 开发HTTP服务
firewall-cmd --zone=public --add-service=http
# 永久开放HTTP服务
firewall-cmd --permanent --zone=public --add-service=http
```

#### 2.3.4.重启

```shell
systemctl start firewalld
```



## 3.常用工具

### 3.1.lrzsz

lrzsz包含常用的命令`rz`、`sz`

#### 3.1.1.查询lrzsz

```shell
yum search lrzsz
```

#### 3.1.2.安装lrzsz

```shell
yum install lrzsz
```

### 3.2.Wget

#### 3.2.1.更新源

```shell
 yum -y update
```

#### 3.2.2.安装Wgte

```shell
yum install wget
```

### 3.3.yum-utils

yum-utils包含`yum-config-manager`命令

#### 3.3.1.安装yum-utils

```shell
yum install yum-utils
```

### 3.4.Vim

#### 3.4.1.安装Vim

```shell
yum install vim
```

## 4.DB

### 4.1.MySQL

#### 4.1.1.下载MySQL的源并安装

```shell
wget https://repo.mysql.com//mysql57-community-release-el7-11.noarch.rpm
```

#### 4.1.2.查询MySQL

```shell
yum repolist all | grep mysql
```

#### 4.1.3.使某个版本的MySQL失效

```shell
yum-config-manager --disable mysql57-community
```

#### 4.1.4.安装MySQL服务

```shell
yum install mysql-community-server
```

#### 4.1.5.启动MySQL服务

```shell
systemctl start mysqld.service
```

#### 4.1.6.查看root用户密码

安装完成之后，系统会自动生成`root@localhost`账户，查看其密码命令如下：

```shell
grep 'temporary password' /var/log/mysqld.log
```

#### 4.1.7.修改root用户密码

登陆MySQL（`mysql -uroot -p`），并修改密码。

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'fuyongde'
```

#### 4.1.8.修改密码强度

validate_password_policy的级别有三级，默认级别为`MEDIUM`

| Policy          | shuoming                                                     |
| --------------- | ------------------------------------------------------------ |
| `0` or `LOW`    | Length                                                       |
| `1` or `MEDIUM` | Length; numeric, lowercase/uppercase, and special characters |
| `2` or `STRONG` | Length; numeric, lowercase/uppercase, and special characters; dictionary file |

修改密码强度

```mysql
set global validate_password_policy=0;
```

#### 4.1.9.新建账户

##### 4.1.9.1.新建具有本地连接的超级管理员账户并赋予权限

```mysql
CREATE USER 'fuyongde'@'localhost' IDENTIFIED BY 'fuyongde';
GRANT ALL PRIVILEGES ON *.* TO 'fuyongde'@'localhost' WITH GRANT OPTION;
```

##### 4.1.9.2.新建具有远程连接的超级管理员账户并赋予权限

需要防火墙开放3306端口

```mysql
CREATE USER 'fuyongde'@'%' IDENTIFIED BY 'fuyongde';
GRANT ALL PRIVILEGES ON *.* TO 'fuyongde'@'%' WITH GRANT OPTION;
```

## 5.Cache

### 5.1.Redis

#### 5.1.1.安装EPEL源

```shell
yum install epel-release
```

or

```shell
# 下载epel-release-7-11.noarch.rpm
wget http://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm
# 安装epel-release-7-11.noarch.rpm
rpm -ivh epel-release-7-5.noarch.rpm
```

#### 5.1.2.安装Redis

```shell
yum install redis
```

#### 5.1.3.启动Redis服务

```shell
# 启动redis服务
systemctl start redis.service
# 查看redis服务状态
systemctl status redis.service
```

#### 5.1.4.Redis配置

```shell
cat /etc/redis.conf
```

#### 5.1.5.测试

```shell
redis-cli ping
```

若返回值为`PONG`说明成功

#### 5.1.6.登录

```shell
redis-cli -h localhost
```

## 6.Java

### 6.1.配置Java环境

#### 6.1.1.安装JDK

```shell
yum localinstall jdk-8u162-linux-x64.rpm
```

