# Springboot应用监控

Springboot提供了运行时的应用监控和管理的功能，我们可以通过HTTP、JMX、SSH协议来对我们的应用进行监控以及管理操作。

### 1、监控端点

Springboot提供了丰富的监控和管理的端点

`actuator`：所有EnPoint的列表，需要加入`spring HATEOAS`支持

`autoconfig`：当前应用的所有自动配置

`beans`：当前应用中所有Bean信息

`configprops`：当前应用中所有的配置属性

`dump`：显示当前应用线程状态信息

`env`：显示当前应用当前环境信息

`health`：显示当前应用健康状况

`info`：显示当前应用信息

`metrics`：显示当前应用的各项指标信息

`mappings`：显示所有的`@RequestMapping`映射的路径

`shutdown`：关闭当前应用（默认关闭）

### 2、HTTP

若应用服务为`web`方式提供的服务，可以通过HTTP的方式来监控服务的状态以及对服务进行管理。

#### 2.1、依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.hateoas</groupId>
  <artifactId>spring-hateoas</artifactId>
</dependency>
```

#### 2.2、所有的端点

```shell
curl http://localhost:8080/actuator
```

结果：

```json
{
    "_links": {
        "self": {
            "href": "http://localhost:8080/actuator",
            "templated": false
        },
        "auditevents": {
            "href": "http://localhost:8080/actuator/auditevents",
            "templated": false
        },
        "beans": {
            "href": "http://localhost:8080/actuator/beans",
            "templated": false
        },
        "caches-cache": {
            "href": "http://localhost:8080/actuator/caches/{cache}",
            "templated": true
        },
        "caches": {
            "href": "http://localhost:8080/actuator/caches",
            "templated": false
        },
        "health-component-instance": {
            "href": "http://localhost:8080/actuator/health/{component}/{instance}",
            "templated": true
        },
        "health-component": {
            "href": "http://localhost:8080/actuator/health/{component}",
            "templated": true
        },
        "health": {
            "href": "http://localhost:8080/actuator/health",
            "templated": false
        },
        "conditions": {
            "href": "http://localhost:8080/actuator/conditions",
            "templated": false
        },
        "configprops": {
            "href": "http://localhost:8080/actuator/configprops",
            "templated": false
        },
        "env": {
            "href": "http://localhost:8080/actuator/env",
            "templated": false
        },
        "env-toMatch": {
            "href": "http://localhost:8080/actuator/env/{toMatch}",
            "templated": true
        },
        "info": {
            "href": "http://localhost:8080/actuator/info",
            "templated": false
        },
        "loggers": {
            "href": "http://localhost:8080/actuator/loggers",
            "templated": false
        },
        "loggers-name": {
            "href": "http://localhost:8080/actuator/loggers/{name}",
            "templated": true
        },
        "heapdump": {
            "href": "http://localhost:8080/actuator/heapdump",
            "templated": false
        },
        "threaddump": {
            "href": "http://localhost:8080/actuator/threaddump",
            "templated": false
        },
        "metrics": {
            "href": "http://localhost:8080/actuator/metrics",
            "templated": false
        },
        "metrics-requiredMetricName": {
            "href": "http://localhost:8080/actuator/metrics/{requiredMetricName}",
            "templated": true
        },
        "scheduledtasks": {
            "href": "http://localhost:8080/actuator/scheduledtasks",
            "templated": false
        },
        "httptrace": {
            "href": "http://localhost:8080/actuator/httptrace",
            "templated": false
        },
        "mappings": {
            "href": "http://localhost:8080/actuator/mappings",
            "templated": false
        }
    }
}
```

#### 2.3、健康端点

```shell
curl http://localhost:8080/actuator/health
```

结果：

```json
{
    "status": "UP",
    "details": {
        "db": {
            "status": "UP",
            "details": {
                "database": "MySQL",
                "hello": 1
            }
        },
        "mail": {
            "status": "UP",
            "details": {
                "location": "smtp.126.com:-1"
            }
        },
        "diskSpace": {
            "status": "UP",
            "details": {
                "total": 127389396992,
                "free": 20913967104,
                "threshold": 10485760
            }
        }
    }
}
```

#### 2.4、其他端点

其他端点不一一赘述，可根据2.1中的结果集访问对应的端点，获取对应的信息。

### 3、JMX

我们可以通过使用`jConsole`、`Java Mission Control`或自己编写应用连接应用所暴漏的JMX服务来对应用服务进行监控。

#### 3.1、开启JMX服务

应用服务的启动参数添加以下参数，即可开启`JMX`服务，无需引入多余的包

```shell
-Djava.rmi.server.hostname=127.0.0.1
-Dcom.sun.management.jmxremote
-Dcom.sun.management.jmxremote.port=7099
-Dcom.sun.management.jmxremote.ssl=false
-Dcom.sun.management.jmxremote.authenticate=false
```

#### 3.2、使用`jConsole`或`Java Mission Control `来访问`JMX`服务

配置对应的连接，如`service:jmx:rmi:///jndi/rmi://localhost:7099/jmxrmi`，即可连接对应的服务，在监控界面，我们可以获取各个端点（或`MBean`）的信息。

#### 3.3、编写应用程序访问`JMX`服务

核心代码如下：

```java
String jmxUrl = "service:jmx:rmi:///jndi/rmi://localhost:7099/jmxrmi";
JMXServiceURL serviceUrl = new JMXServiceURL(jmxUrl);
JMXConnector jmxConnector = JMXConnectorFactory.connect(serviceUrl, null);
MBeanServerConnection mbeanConn = jmxConnector.getMBeanServerConnection();
Object o = mbeanConn.getAttribute(ObjectName.getInstance("org.springframework.boot:type=Endpoint,name=healthEndpoint"), "Data");
String s = JSON.toJSONString(o);
```

### 4、SSH

我们还可以通过SSH或TELNET监控和管理我们的应用，这一点spring boot是借助[CraSH](http://www.crashub.org)来实现。需要注意的是，__springboot2.0之后的版本已经废弃该用法__。

#### 4.1、依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-remote-shell</artifactId>
</dependency>
```

#### 4.2、配置

常用配置如下，不再赘述，更多配置也可以参考官方文档

```properties
management.shell.auth.simple.user.name=user
management.shell.auth.simple.user.password=password
management.shell.telnet.enabled=true
management.shell.telnet.port=5000
management.shell.ssh.port=2000
```

#### 4.3、常见命令

| command    | desc                                                      |
| ---------- | --------------------------------------------------------- |
| autoconfig | Display auto configuration report from ApplicationContext |
| beans      | Display beans in ApplicationContext                       |
| cron       | manages the cron plugin                                   |
| dashboard  | a monitoring dashboard                                    |
| egrep      | search file(s) for lines that match a pattern             |
| endpoint   | Invoke actuator endpoints                                 |
| env        | display the term env                                      |
| filter     | a filter for a stream of map                              |
| java       | various java language commands                            |
| jmx        | Java Management Extensions                                |
| jul        | java.util.logging commands                                |
| jvm        | JVM informations                                          |
| less       | opposite of more                                          |
| mail       | interact with emails                                      |
| man        | format and display the on-line manual pages               |
| metrics    | Display metrics provided by Spring Boot                   |
| shell      | shell related command                                     |
| sleep      | sleep for some time                                       |
| sort       | sort a map                                                |
| system     | vm system properties commands                             |
| thread     | JVM thread commands                                       |
| help       | provides basic help                                       |
| repl       | list the repl or change the current repl                  |

#### 4.4、常见的使用

##### 4.4.1、查询所有端点

`endpoint list`

结果：

```
requestMappingEndpoint
environmentEndpoint
healthEndpoint
beansEndpoint
infoEndpoint
loggersEndpoint
metricsEndpoint
traceEndpoint
dumpEndpoint
autoConfigurationReportEndpoint
configurationPropertiesReportEndpoint
```

##### 4.4.2、查看健康端点信息

`endpoint invoke healthEndpoint`

结果：

```
{status=UP, dubbo={status=UNKNOWN, memory={source=management.health.dubbo.status.defaults, status={level=OK, message=max:5436M,total:998M,used:636M,free:362M, description=null}}, load={source=management.health.dubbo.status.defaults, status={level=UNKNOWN, message=cpu:4, description=null}}, server={source=dubbo@ProtocolConfig.getStatus(), status={level=OK, message=/192.168.0.88:50191(clients:3), description=null}}}, jms={status=UP, provider=ActiveMQ}, diskSpace={status=UP, total=127389396992, free=20854661120, threshold=10485760}, redis={status=UP, version=3.2.12}, db={status=UP, database=MySQL, hello=1}}
```

##### 4.4.2、查询gc信息

`jvm gc`

结果：

```result
GARBAGE COLLECTION
name :PS Scavenge
name              value
------------------------------------                                                   
collection count  13                                                                   
collection time   207                                                                   
mpool name        PS Eden Space                                                         
mpool name        PS Survivor Space     

name :PS MarkSweep
name              value                                                                 
------------------------------------                                                   
collection count  3                                                                     
collection time   414                                                                   
mpool name        PS Eden Space                                                         
mpool name        PS Survivor Space                                                     
mpool name        PS Old Gen    
```