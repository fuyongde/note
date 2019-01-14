### 1.1、第三方客户端连接时报`2058`的错

原因：`MySQL8.0`默认采用`caching_sha_password`的加密方式，第三方客户端都不支持这种加密方式，自带的命令行支持。

解决方案：使用命令行进入MySQL，并修改对应用户的加密方式

```sql
-- 修改加密方式
ALTER USER 'root'@'%' IDENTIFIED BY 'root' PASSWORD EXPIRE NEVER;

-- 修改密码
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'fuyongde';

-- 刷新
FLUSH PRIVILEGES;

```