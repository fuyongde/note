## zkCli常用命令

### 1、获取子节点列表

`ls path`

example：

```shell
[zk: localhost:2181(CONNECTED) 2] ls /
[letou, dubbo, elastic-job-data, zookeeper, elastic-job-letou-20180829]
```

### 2、获取节点数据

`get path`

example:

 ```shell
[zk: localhost:2181(CONNECTED) 3] get /

cZxid = 0x0
ctime = Thu Jan 01 08:00:00 CST 1970
mZxid = 0x0
mtime = Thu Jan 01 08:00:00 CST 1970
pZxid = 0x3000086b2
cversion = 4
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0

 ```

### 3、