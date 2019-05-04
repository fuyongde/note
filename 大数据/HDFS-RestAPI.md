### 创建文件

curl -i -L -XPUT -T /tmp/chenmiaoshan.json -H "content-type:application/octet-stream" "http://localhost:50070/webhdfs/v1/user/root/input/test.json?op=CREATE&user.name=root"

### 打开一个文件

curl -i http://localhost:50075/webhdfs/v1/user/root/input/test.json?op=OPEN\&user.name=root\&namenoderpcaddress=localhost:9000\&createflag=\&createparent=true\&overwrite=false