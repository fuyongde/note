# curl命令

`curl`命令可以用来访问多种协议的URI，这里简单列举`curl`命令在访问`http/https`协议时的一些常见用法。

### 语法

`curl [options...] <url>`

### 参数

参数过多，在这里不一一列举，可以执行`curl -h`来查看。

### 常见用法
##### 访问一个URL

```shell
# 访问百度
curl https://www.baidu.com/
```

#####  问一个URL，并将跟踪整个过程存储到文件

```shell
# 将跟踪的过程存储到文件debugdump.txt
curl --trace-ascii debugdump.txt https://www.baidu.com/
# 在跟踪的过程中添加时间
curl --trace-ascii debugdump.txt --trace-time https://www.baidu.com/
```

