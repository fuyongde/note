# Docker中使用常用软件

## 1、MySQL

### 1.1、查询所需的镜像

```shell
docker search mysql
```

### 1.2、安装所需的容器

```shell
# 安装MySQL的5.7.24版本
docker pull mysql:5.7.24
# 缺省TAG时默认安装最新版本
docker pull mysql
```

### 1.3、运行容器

```shell
# 启动MySQL
docker run -p 3306:3306 --name MySQL -e MYSQL_ROOT_PASSWORD=fuyongde mysql:5.7.24
```

## 2、Zookeeper

### 2.1、查询所需镜像

```shell
docker search zookeeper
```

### 2.2、安装容器

```shell
docker pull zookeeper
```

### 2.3、运行容器

```shell
# 启动zookeeper
docker run -p 2181:2181 --name zookeeper zookeeper
```

## 3、Jenkins

### 3.1、查询所需镜像

```shell
docker search jenkins
```

### 3.2、安装容器

docker hub中`Docker Official Images`版本比较旧，故不推荐安装该版本，jenkins官网的版本推荐[这个版本](https://hub.docker.com/r/jenkins/jenkins)

```shell
# Official版（不推荐）
docker pull jenkins
# 推荐安装产期支持版本
docker pull jenkins/jenkins:lts
# 每周更新版
docker pull jenkins/jenkins
```

### 3.3、运行容器

#### 3.3.1、缺省工作空间

```shell
docker run -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts
# 同时指定名称
docker run --name jenkins -p 8080:8080 -p 5000:5000 jenkins/jenkins:lts
```

#### 3.3.2、指定工作空间

```shell
docker run -d -v jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts
```

## 4、

### 4.1、查询所需镜像

```shell
docker search 
```

### 4.2、安装容器

```shell
docker pull 
```

### 4.3、运行容器

```

```

