{{indexmenu_n>5}}

# 性能数据

### 吞吐量

> 测试为单Source以及非共享TaskSlot的单Map操作。Same Host表示两个TaskManager是否在同一台机器上运行。

|              |          |            |           |
| ------------ | -------- | ---------- | --------- |
| Message Size | Parallel | Throughput | Same Host |
| 1024         | 1        | 400mb/s    | True      |
| 1024         | 1        | 360mb/s    | False     |
| 1024 \* 1024 | 1        | 345 mb/s   | False     |
| 1024 \* 1024 | 1        | 278 mb/s   | False     |

### 延迟

> 测试为单Source以及非共享TaskSlot的单Map操作。Same Host表示两个TaskManager是否在同一台机器上运行。

|              |          |       |
| ------------ | -------- | ----- |
| Message Size | Parallel | 延迟中位数 |
| 4 byte       | 1        | 29 ms |

### Kafka Consumer

> 测试逻辑为单Kafka Source，后接共享TaskSlot的Map操作。

|              |             |                |              |                   |                    |            |
| ------------ | ----------- | -------------- | ------------ | ----------------- | ------------------ | ---------- |
| Message Size | Broker Size | Partition Size | Replica Size | Consumer Parallel | Checkpoint Enabled | Throughput |
| 1024         | 3           | 3              | 3            | 1                 | False              | 230mb/s    |
| 1024         | 3           | 3              | 3            | 3                 | False              | 370mb/s    |
| 1024         | 3           | 3              | 3            | 1                 | True               | 230mb/s    |
| 1024         | 3           | 3              | 3            | 3                 | True               | 370mb/s    |

### Kafka Consumer

> 测试逻辑为单字符串生成Source，后接共享TaskSlot的Kafka Sink。

|              |             |                |              |     |          |            |
| ------------ | ----------- | -------------- | ------------ | --- | -------- | ---------- |
| Message Size | Broker Size | Partition Size | Replica Size | Ack | Parallel | Throughput |
| 1024         | 3           | 3              | 3            | \-1 | 1        | 75mb/s     |
| 1024         | 3           | 3              | 3            | 1   | 1        | 80mb/s     |
| 100          | 3           | 3              | 3            | \-1 | 1        | 55mb/s     |
| 100          | 3           | 3              | 3            | 1   | 1        | 57mb/s     |

### HDFS/Rocksdb checkpoint吞吐量

> 测试逻辑为单Source，后接共享TaskSlot的Map操作；且该Source为有状态操作，其状态为给定大小的byte数组。

|                        |                     |                       |         |         |
| ---------------------- | ------------------- | --------------------- | ------- | ------- |
| Stated Source Parallel | Checkpoint Interval | Checkpoint state Size | hdfs    | rocksdb |
| 1                      | 10000 ms            | 200mb                 | 133mb/s | 100mb/s |
| 3                      | 10000 ms            | 200mb (each)          | 200mb/s | 128mb/s |
| 1                      | 10000 ms            | 100mb                 | 133mb/s | 100mb/s |
| 3                      | 10000 ms            | 100mb (each)          | 150mb/s | 150mb/s |

### Flink hdfs checkpoint 延迟

> 测试逻辑为单Source，后接共享TaskSlot的Map操作；且该Source为有状态操作，其状态为给定大小的byte数组。

|                        |                     |                       |                     |
| ---------------------- | ------------------- | --------------------- | ------------------- |
| Stated Source Parallel | Checkpoint Interval | Checkpoint state Size | AVG Checkpoint Time |
| 1                      | 5000 ms             | 5kb                   | 48 ms               |
| 3                      | 5000 ms             | 5kb (each state)      | 100 ms              |
