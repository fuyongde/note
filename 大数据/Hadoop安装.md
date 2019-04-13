## 安装

### 基础环境

-  jdk
-  ssh
-  rsync
  
```shell
# 安装jdk（略）
# 安装ssh
yum install ssh
# 安装 rsync
yum install rsync
```

### 安装hadoop

下载hadoop，并解压

```shell
# 下载Hadoop
wget https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/hadoop-2.9.2/hadoop-2.9.2.tar.gz
# 解压
tar -xzvf hadoop-2.9.2.tar.gz
```

### 修改配置文件

1. 修改`etc/hadoop/hadoop-env.sh`

```shell
# 配置hadoop使用的jdk
export JAVA_HOME=/usr/java/latest
```

2. 修改配置文件`etc/hadoop/core-site.xml`

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

3. 修改配置文件`etc/hadoop/hdfs-site.xml`

```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

### 启动服务

1. 设置文件系统的格式

```shell
bin/hdfs namenode -format
```

2. 启动`namenode`和`datasenode`守护进程
   
```shell
sbin/start-dfs.sh

# 启动所有的服务
sbin/start-all.sh
```

此时可以浏览界面 http://localhost:50070/ 查看是否启动成功

3. 创建`MapReduce`作业所需的`HDFS`目录

```shell
bin/hdfs dfs -mkdir /user
bin/hdfs dfs -mkdir /user/root
```

4. 将输入文件复制到文件系统中
   
```shell
bin/hdfs dfs -put etc/hadoop input
```

5. 运行提供的一些示例
   
```shell
bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.9.2.jar grep input output 'dfs[a-z.]+'
```