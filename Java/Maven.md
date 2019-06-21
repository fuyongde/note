## Maven

### 配置文件加载顺序

使用`Maven`过程中经常会有一个困惑，那就是`${user.home}/.m2/settings.xml`与`${maven.home}/conf/settings.xml`两个配置文件，到底是以哪个为准？

实际上`Maven`加载配置文件是有优先级的：

1. ${project}/pom.xml
2. ${user.home}/.m2/settings.xml
3. ${maven.home}/conf/settings.xml