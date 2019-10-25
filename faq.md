

# FAQs

### 取消Flink的应用之后，为什么Flink的JobManager一直不退出？

因为Flink 1.6.4自身逻辑的原因，如果在取消应用之后，没有退出Flink Jobmanager
UI，会导致JobManager不能立即退出，而是等待一段时间。用户在取消应用之后，需要及时退出JobManager
UI；或者在Yarn的ResourceManager UI中进行处理。

### 提交应用时，任务提交日志显示无法下载资源

可以从以下几个方面检查集群创建时选取的UFile Bucket/Token是否有效：

1\. Token是否具有下载权限；

2\. Token的是否过期；

3\. 文件资源是否上传到创建集群时所指定的UFile Bucket；
