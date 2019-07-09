{{indexmenu_n>6}}

# 产品价格

本文档中展示的价格以北京二可用区E为例，其他可用区的价格可以登录[UFlink控制台](https://console.ucloud.cn/uflink/create)查看。

## 1\. 计费方式

UFlink采用预付费的计费模式，根据集群所使用的计算资源的配置和数量线性叠加计费，支持按小时、按月、按年支付，其中：

按小时单价 = 按月单价 × 1.5 / (24 × 30)

按年单价 = 按月单价 × 10

## 2\. 各机型价格表

| 节点类型   | 机型                | CPU（核） | 内存（GB） | 价格（元/月） |
| ------ | ----------------- | ------ | ------ | ------- |
| Master | flink-g1m-large   | 2      | 8      | 660     |
| Master | flink-g1m-xlarge  | 4      | 16     | 1015    |
| Slave  | flink-g1s-large   | 2      | 8      | 660     |
| Slave  | flink-g1s-xlarge  | 4      | 16     | 1015    |
| Slave  | flink-g1s-2xlarge | 8      | 32     | 1750    |
| Slave  | flink-g1s-4xlarge | 16     | 64     | 3210    |
