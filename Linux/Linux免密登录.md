# Linux免密登录

### 1.1、场景

A主机实现免密登录B主机

### 1.2、实现

1. A主机生成公私钥对`ssh-keygen`；
2. 将生成的公钥传输给B机器`scp /root/.ssh/id_rsp.pub remoteUser@remoteIp:remoteFolder`；
3. 登录B机器，将A机器的公钥追加到B主机用户目录下的`.ssh/authorized_keys`文件，`cat id_rsa.pub >> .ssh/.ssh/authorized_keys`。若没有`authorized_keys`文件，则需要创建，且其权限要为`600`。