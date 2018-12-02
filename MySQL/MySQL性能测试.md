# MySQL性能测试

## 1.msqlslap

msqlslap是MySQL自带的数据库基准测试工具，可以模拟大量的客户端同事操作数据库的情况，通过结果信息来了解数据库的性能。

### 1.1.语法
`mysqlslap [OPTIONS]`
### 1.2.示例
```shell
[root@izwz9ezwhlhq4hy1wzw9oqz ~]# mysqlslap -uroot -pfuyongde --concurrency=100 --iterations=1 --auto-generate-sql --auto-generate-sql-load-type=mixed --auto-generate-sql-add-autoincrement --engine=innodb --number-of-queries=5000
mysqlslap: [Warning] Using a password on the command line interface can be insecure.
Benchmark
	Running for engine innodb
	Average number of seconds to run all queries: 0.693 seconds
	Minimum number of seconds to run all queries: 0.693 seconds
	Maximum number of seconds to run all queries: 0.693 seconds
	Number of clients running queries: 100
	Average number of queries per client: 50
```

上述示例中，参数的含义：

`--concurrency`：模拟查询的客户端的数量。

`--iterations`：测试的次数。

`--auto-generate-sql`：生成不由文件或命令行提供的SQL。

`--auto-generate-sql-load-type`：指定测试加载的类型：`mixed(默认)`、`update`、`write`、`key`、`read`。

`--auto-generate-sql-add-autoincrement`：对于自动生成的表，采用`AUTO_INCREMENT`

`--engine`：使用的数据库引擎。

`--number-of-queries`：限制每个客户端的查询次数（保证精确）。

上述示例中结果的意义：

```shell
Running for engine innodb #表示运行的是innodb存储引擎
Average number of seconds to run all queries: 0.693 seconds #查询平均耗时
Minimum number of seconds to run all queries: 0.693 seconds #查询最低耗时
Maximum number of seconds to run all queries: 0.693 seconds #查询最高耗时
Number of clients running queries: 100 #运行的客户端数量
Average number of queries per client: 50 #每个客户端平均运行50次查询(5000/100=50)
```