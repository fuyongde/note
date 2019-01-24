# Docker常用命令

## 1、安装镜像

### 1.1、查询所需的镜像

- Usage：

  `docker search [OPTIONS] TERM`

- Options：

  `-f --filter` ：根据提供的条件过滤输出

  `--format` ：使用Go模板进行漂亮的打印搜索

  `--limit` ：最大搜索结果数（默认为25）

  `--no-trunc`：不要截断输出

- Example：

```shell
docker search mysql
```

### 1.2、安装所需的容器

- Usage：

  `docker pull [OPTIONS] NAME[:TAG|@DIGEST]`

- Options：

  `-a --all-tags`：下载存储库中的所有标记镜像
  
  `--disable-content-trust`：跳过图像验证（默认为true）

- Example：

  ```shell
  # 安装MySQL的5.7.24版本
  docker pull mysql:5.7.24
  # 缺省TAG时默认安装最新版本
  docker pull mysql
  # 安装所有版本
  docker pull -a mysql
  # 安装zookeeper
  docker pull zookeeper
  ```

## 2、容器管理

### 2.1、查看容器

```shell
# 查看所有正在运行的容器
docker ps
# 查看所有容器
docker ps -a
查看所有容器ID
docker ps -a -q
```

### 2.2、启动容器

`docker run IMG`

Example：

```shell
# 启动MySQL
docker run -p 3306:3306 --name MySQL -e MYSQL_ROOT_PASSWORD=fuyongde mysql:5.7.24
# 启动zookeeper
docker run -p 2181:2181 --name zookeeper zookeeper
```

### 2.3、停止容器

`docker stop [containerId]`：停止容器

Example:

```shell
# 删除id为8fcc3dad236a的容器
docker rm 8fcc3dad236a
```

### 2.4、重启容器

```shell
# 重启id为1e4b2a31028d的容器
docker restart 1e4b2a31028d
```

### 2.5、删除容器

`docker rm [containerId]`

```shell
# 删除id为1e4b2a31028d的容器
docker rm 1e4b2a31028d
```

### 2.6、重命名容器

```shell
# 将docker-mysql重命名为mysql
docker rename docker-mysql mysql
```

## 3、镜像管理

### 3.1、查看镜像信息

Usage：

`docker images [OPTIONS] [REPOSITORY[:TAG]]`

Options:

`-a, --all`：展示全部镜像
​    `--digests`：展示镜像的哈希值
`-f, --filter filter`：根据提供的条件过滤输出
​    `--format string`：使用Go模板的漂亮打印图像
​    `--no-trunc`：不要截断输出
`-q, --quiet`：仅显示数字ID

Example:

```shell
# 查看所有的进行信息
docker images -a
```

### 3.2、删除镜像

Usage：

`docker rmi [OPTIONS] IMAGE [IMAGE...]`

Options:
 `-f, --force`：强制删除某个镜像
 `--no-prune ` ：不删除未标记的父类

Example：

```shell
# 删除id为cd14cecfdb3a的镜像
docker rmi cd14cecfdb3a
# 删除jenkins镜像
docker rmi jenkins
```

## 4、其他命令

### 4.1、进入到一个容器

`docker exec -it 063caee1d235 /bin/bash`

### 4.2、拷贝文件到一个容器

`docker cp C:\Users\fuyon\Documents\repo\github\goku\atomikos-demo\zhifubao-server\src\main\resources\sql\zhifubao.sql 1e4b2a31028d:/tmp`

### 4.3、从容器拷贝文件到主机

`docker cp 1e4b2a31028d:/tmp/zhifubao.sql C:\Users\fuyon\Documents`