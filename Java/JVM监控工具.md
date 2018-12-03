## Monitoring Tools

## 1、相关工具

[Java8监控相关工具](https://docs.oracle.com/javase/8/docs/technotes/tools/index.html#monitor)有：

`jps`：JVM进程状态工具 - 列出目标机器上已检测到的HotSpot Java虚拟机。

`jstat`：JVM统计监视工具 - 连接到已检测到的HotSpot Java虚拟机，并收集和记录命令行选项指定的性能统计信息。

`jstatd`：JVM jstat守护程序 - 启动一个RMI服务器应用程序，用于监视已检测的HotSpot Java虚拟机的创建和终止，并提供一个接口，允许远程监视工具连接到在本地系统上运行的Java虚拟机。

## 2、用法

### 2.1、jps

`jps [options] [hostid]`

jps命令只能列出目标机器上具有访问权限的JVM进程。`hostid`缺省值为localhost。

#### 2.1.1、options

- `-q`：禁止传递给main方法的类名，JAR文件名和参数的输出，仅生成本地JVM标识符的列表。
- `-m`：显示传递给main方法的参数。 嵌入式JVM的输出可能为null。
- `-l`：显示应用程序主类的完整包名或应用程序的JAR文件的完整路径名。
- `-v`：显示传递给JVM的参数。
- `-V`：禁止传递给main方法的类名，JAR文件名和参数的输出，仅生成本地JVM标识符的列表。
- `-Joption`：传递JVM的选项，其中option是Java应用程序启动器的参考页面上描述的选项之一。 例如`jps -J-Xms48m`将启动内存设置为48 MB。

#### 2.1.2、hostid

主机标识符，缺省值为local host。语法：`[protocol:][[//]hostname][:port][/servername]`

- `protocol`：若未指定主机名，默认是本地协议；若指定了主机名，缺省值为`rmi`协议。
- `hostname`：主机名或ip，缺省值是本地主机。
- `port`：端口。对于本地协议，忽略该参数；对于`rmi`协议，缺省值是`1099`。
- `servername`：对于该参数的处理取决于实现。对于本地协议，忽略该参数；对于`rmi`协议，该参数表示远程主机上的对象名称。更多信息需要参考`jstatd -n`命令。

#### 2.1.3、输出格式

`lvmid [[classname|JARfilename|"Unknown"] [arg*] [jvmarg*]]`

- `lvmid`：其实就是进程号pid。

例如：

```shell
21503 letoubi-provider-1.0.0-SNAPSHOT.jar -Denv=test -Xmx256m -Xms256m -XX:+UseG1GC
```

### 2.2、jstat

`jstat [generalOption|outputOptions vmid [interval[s|ms][count]]`

#### 2.2.1、options

查看所有的选项`jstat -options`

- `-class`：显示有关类加载器行为的统计信息。
- `-compiler`：显示有关Java HotSpot VM实时编译器行为的统计信息。
- `-gc`：显示有关垃圾收集堆行为的统计信息。
- `-gccapacity`：显示有关代的容量及其相应空间的统计信息。
- `-gccause`：显示有关垃圾收集统计信息（与-gcutil相同）的摘要，其中包含最后一个和当前（适用时）垃圾收集事件的原因。
- `-gcnew`：显示新一代行为的统计信息。
- `-gcnewcapacity`：显示有关新代及其相应空间大小的统计信息。
- `-gcold`：显示有关旧代和元空间统计信息行为的统计信息。
- `-gcoldcapacity`：显示有关旧代大小的统计信息。
- `-gcmetacapacity`：显示有关元空间大小的统计信息。
- `-gcutil`：显示有关垃圾收集统计信息的摘要。
- `-printcompilation`：显示Java HotSpot VM编译方法统计信息。

##### 2.2.1.1、jstat -class

类加载器统计信息。 `jstat -class pid `

| Loaded | Bytes   | Unloaded | Bytes | Time |
| ------ | ------- | -------- | ----- | ---- |
| 9126   | 17624.2 | 0        | 0.0   | 7.31 |

- `Loaded`：已加载的类数。
- `Bytes`：加载的kB数。
- `Unloaded`：卸载的类数。
- `Bytes`：已卸载的Kbytes数。
- `Time`：执行类加载和卸载操作所花费的时间。

##### 2.2.1.2、jstat -compiler

Java HotSpot VM即时编译器统计信息。`jstat -compiler pid`

| Compiled | Failed | Invalid | Time  | FailedType | FailedMethod                           |
| -------- | ------ | ------- | ----- | ---------- | -------------------------------------- |
| 8802     | 1      | 0       | 41.49 | 1          | org/aspectj/weaver/Iterators$6 hasNext |

- `Compiled`：已执行的编译任务数。
- `Failed`：编译任务失败次数。
- `Invalid`：已失效的编译任务数。
- `Time`：执行编译任务所花费的时间。
- `FailedType`：编译上次失败编译的类型。
- `FailedMethod`：上次失败编译的类名和方法。

##### 2.2.1.3、jstat -gc

gc统计信息。`jstat -gc pid`

| S0C     | S1C     | S0U  | S1U  | EC        | EU       | OC       | OU      | MC      | MU      | CCSC   | CCSU   | YGC  | YGCT  | FGC  | FGCT  | GCT   |
| ------- | ------- | ---- | ---- | --------- | -------- | -------- | ------- | ------- | ------- | ------ | ------ | ---- | ----- | ---- | ----- | ----- |
| 72192.0 | 74752.0 | 0.0  | 0.0  | 1157120.0 | 921880.6 | 231424.0 | 82444.0 | 35456.0 | 34840.5 | 3968.0 | 3759.7 | 11   | 0.619 | 2    | 0.316 | 0.935 |

- `S0C`：当前幸存者空间0容量（kB）。
- `S1C`：当前幸存者空间1容量（kB）。
- `S0U`：幸存者空间0利用率（kB）。
- `S1U`：幸存者空间1利用率（kB）。
- `EC`：当前的伊甸园空间容量（kB）。
- `EU`：伊甸园空间利用率（kB）。
- `OC`：当前旧空间容量（kB）。
- `OU`：旧空间利用率（kB）。
- `MC`：元空间容量（kB）。
- `MU`：Metacspace利用率（kB）。
- `CCSC`：压缩类空间容量（kB）。
- `CCSU`：使用的压缩类空间（kB）。
- `YGC`：年轻一代垃圾收集活动的数量。
- `YGCT`：年轻一代垃圾收集时间。
- `FGC`：完整GC事件的数量。
- `FGCT`：完全垃圾收集时间。
- `GCT`：垃圾收集总时间。

##### 2.2.1.4、jstat -gccapacity

内存池生成和空间容量。`jstat -gccapacity pid`

| NGCMN   | NGCMX     | NGC       | S0C     | S1C     | EC        | OGCMN    | OGCMX     | OGC      | OC       | MCMN | MCMX      | MC      | CCSMN | CCSMX     | CCSC   | YGC  | FGC  |
| ------- | --------- | --------- | ------- | ------- | --------- | -------- | --------- | -------- | -------- | ---- | --------- | ------- | ----- | --------- | ------ | ---- | ---- |
| 82944.0 | 1321984.0 | 1321984.0 | 72192.0 | 74752.0 | 1157120.0 | 166912.0 | 2644992.0 | 231424.0 | 231424.0 | 0.0  | 1081344.0 | 35456.0 | 0.0   | 1048576.0 | 3968.0 | 11   | 2    |

- `NGCMN`：最小新一代容量（kB）。
- `NGCMX`：最大新一代容量（kB）。
- `NGC`：当前的新一代容量（kB）。
- `S0C`：当前幸存者空间0容量（kB）。
- `S1C`：当前幸存者空间1容量（kB）。
- `EC`：当前的伊甸园空间容量（kB）。
- `OGCMN`：最小旧代容量（kB）。
- `OGCMX`：最大老一代容量（kB）。
- `OGC`：当前的旧代容量（kB）。
- `OC`：当前旧空间容量（kB）。
- `MCMN`：最小元空间容量（kB）。
- `MCMX`：最大元空间容量（kB）。
- `MC`：元空间容量（kB）。
- `CCSMN`：压缩类空间最小容量（kB）。
- `CCSMX`：压缩类空间最大容量（kB）。
- `CCSC`：压缩类空间容量（kB）。
- `YGC`：年轻一代GC活动的数量。
- `FGC`：完整GC事件的数量。

##### 2.2.1.5、jstat -gccause、jstat -gcutil

这两个命令均是统计GC回收的信息，但`jstat -gccause`相较于`jstat -gcutil`多展示两列，包括上次gc的原因，以及当前gc的原因。`jstat -gccause pid`|`jstat -gcutil pid`

| S0   | S1     | E     | O     | M     | CCS   | YGC  | YGCT  | FGC  | FGCT  | GCT   | LGCC                | GCC   |
| ---- | ------ | ----- | ----- | ----- | ----- | ---- | ----- | ---- | ----- | ----- | ------------------- | ----- |
| 0.00 | 100.00 | 30.87 | 37.94 | 97.70 | 94.80 | 24   | 0.614 | 0    | 0.000 | 0.614 | G1 Evacuation Pause | No GC |

- `S0`：幸存者空间0利用率占空间当前容量的百分比。
- `S1`：幸存者空间1利用率占空间当前容量的百分比。
- `E`：伊甸园空间利用率占空间当前容量的百分比。
- `O`：旧空间利用率占空间当前容量的百分比。
- `M`：元空间利用率占空间当前容量的百分比。
- `CCS`：压缩的类空间利用率百分比。
- `YGC`：年轻一代GC活动的数量。
- `YGCT`：年轻一代垃圾收集时间。
- `FGC`：完整GC事件的数量。
- `FGCT`：完全垃圾收集时间。
- `GCT`：垃圾收集总时间。
- `LGCC`：最后一次垃圾收集的原因
- `GCC`：当前垃圾收集的原因

##### 2.2.1.6、jstat -gcnew

##### 2.2.1.7、jstat -gcnewcapacity
##### 2.2.1.8、jstat -gcoldcapacity
##### 2.2.1.9、jstat -gcmetacapacity
##### 2.2.1.10、jstat -printcompilation
#### 2.2.2、vmid

#### 2.2.3、interval
#### 2.2.4、count
### 2.3、jstatd

#### 2.3.1、安全策略

​	jstatd是监视具有适当的本机访问权限的JVM，因此，jstatd进程必须使用与目标jvm相同的用户运行。但是对于Linux系统中的root用户，其具有访问系统上任何JVM的权限，所以用root账号运行jstatd进程也可以监视所需的JVM，但会带来其他的安全问题，故不推荐。

​	jstatd服务器不提供远程客户端任何身份验证，因此，在启动jstatd进程之前，一定要考虑本地的安全策略。若未安装其他的安全管理器，jstatd会安装RMISecurityPolicy实例，因此需要指定安全策略文件，具体请参考[这里](http://docs.oracle.com/javase/8/docs/technotes/guides/security/PolicyFiles.html)。

​	在`JAVA_HOME/bin`目录下新建名为`jstatd.all.policy`的文件，并复制一下代码：

```shell
grant codebase "file:${java.home}/../lib/tools.jar" {   
    permission java.security.AllPermission;
};
```

​	运行命令`jstatd -J-Djava.security.policy=jstatd.all.policy`，即可启动本机的RMI服务。

#### 2.3.2、开启日志

`jstatd -J-Djava.security.policy=jstatd.all.policy -J-Djava.rmi.server.logCalls=true`

