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

## 2、常用命令

### 2.1、查看容器

`docker ps`：查看所有正在运行的容器

`docker ps -a`：查看所有容器

`docker ps -a -q`：查看所有容器ID

### 2.2、启动容器

`docker run IMG`

Example：

```shell
# 启动MySQL
docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD=fuyongde mysql:5.7.24
# 启动zookeeper
docker run -p 2181:2181 zookeeper
```

### 2.3、停止容器

`docker stop [containerId]`：停止容器

### 2.4、删除容器

`docker rm [containerId]`：删除容器

### 2.5、重命名

`docker rename docker-mysql mysql`：将docker-mysql重命名为mysql