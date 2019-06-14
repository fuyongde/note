### 序列化

#### 提示生成Java序列化id
Settings-Editor-Inspections-Java-Serialization issues-Serializable class without 'seriaVersionUID'
Settings-Editor-Inspections-Java-Serialization issues-'serialVersionUID' field not declared 'private static final long'

### 硬盘

#### 解决硬盘速度慢导致的IDEA卡顿问题

`ctrl` + `shift` + `a` 输入`Synchronize file on frame activation`，关闭相关设置
`ctrl` + `shift` + `a` 输入`Save files on framedeactivation`，关闭相关设置
`Settings` - `System Settings` - `Synchronization` - `Save files automatically if application is idle for` 修改为60s