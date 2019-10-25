

# UFlink开发注意事项

基于UFlink托管集群的Flink应用开发，和自建集群类似，但是仍然有几个地方需要注意。

### JobManager和TaskManager默认配置

1\. 托管集群默认指定JobManager以及TaskManager的堆大小为2GB，目前不支持进行更改；

2\. 单个TaskManager中的TaskSlot数量设置为1；

### 高可用配置

Flink应用的高可用由Yarn集群以及zookeeper共同保证。在Flink应用因各种原因出现问题时，会触发HA机制保证任务继续运行：

1\. 当TaskManager节点出问题之后，Flink的JobManager会向Yarn申请新的TaskManager资源，保证任务的运行；

2\.
当JobManager节点出现问题之后，Yarn会根据配置重启JobManager，而后者会从zookeeper中获取旧得运行数据，并回恢复运行，从而恢复运行。

> 由于Yarn集群基于2.7.7版本，仅支持对重启次数进行限定，目前设置的AM重启次数最大为5次，即yarn.resourcemanager.am.max-attempts设置为5。

### Checkpoint/Savepoint配置

Flink集群的运行时状态保存在hdfs的指定目录中。

1\. Checkpoint的默认目录为/flink-cluster/checkpoints；

2\. Savepoint的默认目录为/flink-cluster/savepoints；

3\. Statebackend默认为filesystem，state.backend.incremental设置为true；

> 注意：在自定义checkpoint或者savepoint的目录时，请尽量使用默认的值。其它目录不保证用户有权限写入，而且不保证文件的持久性。
