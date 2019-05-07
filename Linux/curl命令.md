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

##### 访问多个URL

```shell
# 访问百度、Bing（顺序是百度->Bing），默认情况下使用GET请求
curl https://www.baidu.com/ https://cn.bing.com/
# 此时会发送同样的参数到两个URL，由于使用了--data，故该请求会采用POST的方式
curl --data "name=fuyongde&age=18" https://www.baidu.com/ https://cn.bing.com/
```

##### --get

`--get` 以`GET`方法请求含`-d data`参数的URL

```shell
# 该示例会以GET的方式访问/api/hello，并将参数传递给对应的服务
curl --data "name=fuyongde&age=18" --get http://192.168.0.88:8080/api/hello
```

##### --data、--data-ascii、--data-binary、--data-urlencode

`--data`：使用`POST`方法发送参数
`--data-ascii`：使用`POST`方法发送ASCII编码的参数
`--data-binary`：使用`POST`方法发送二进制参数
`--data-urlencode DATA`：使用`POST`方式发送编码过的URL参数

```shell
# 使用POST方式访问/api/hello，并将参数传递给对应的服务
curl --data name=fuyongde http://192.168.0.88:8080/api/hello
```

##### --trace、--trace-ascii、--trace-time

`--trace FILE` ：将调试跟踪信息写入到文件
`--trace-ascii FILE `：将调试跟踪信息已HEX编码写入到文件
`--trace-time`：在跟踪信息上输入时间信息

```shell
# 将跟踪的过程存储到文件debugdump.txt
curl --trace-ascii debugdump.txt https://www.baidu.com/
# 在跟踪的过程中添加时间
curl --trace-ascii debugdump.txt --trace-time https://www.baidu.com/
```

##### 保存访问的网页

```powershell
# 使用Linux的的重定向功能保存
curl https://www.baidu.com/ >> baidu.html
# 使用curl内置的-o参数保存网页
curl -o baidu.html https://www.baidu.com/
# 使用-O来保存网页中的静态资源
curl -O https://ss0.bdstatic.com/5aV1bjqh_Q23odCf/static/superman/img/copy_rignt_24.png
```

##### 指定HTTP-Header

```shell
# POST json 
curl -H "Content-Type:application/json" -X POST --data '{"name":"fuyongde"}' http://localhost:8080/api/hello

# 指定多个Header
curl -H "Content-Type:application/json" -H "Token:abc" -X POST --data '{"name":"fuyongde"}' http://localhost:8080/api/hello
```