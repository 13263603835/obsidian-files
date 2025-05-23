# 主题操作
>`kafka-topics.sh`：可以执行大部分与主题相关的操作 （增删改查）
## 创建topic
-  `--topic`：主题名称
	- 可以使用下划线、破折号、点号
	- 但是不建议使用点号，Kafka内部会将`.`改为`_`，例如`topic.1`->`topic_1`这会导致主题名称冲突
-  `--replication-factor`：副本数
-  `--partitions`：分区数
- `--if-not-exists`：主题不存在执行创建，如果存在不执行
```
kafka-topics.sh --bootstrap-server localhost:9092 --create --topic my_topic --replication-factor 2 --partitions 2 --if-not-exists
```
## 列出所有topic
```
kafka-topics.sh --list --zookeeper localhost:2181
kafka-topics.sh --bootstrap-server localhost:9092 --list
```
## 查看主题详情
```
#查看主题详情
kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic my_topic
#列出ISR数低于配置的分区
kafka-topics.sh --bootstrap-server localhost:9092 --describe --at-min-isr-partitions
```

- `--topics-with-overrides`：列出配置参数与集群默认值不同的主题
- `--exclude-internal`：过滤调名称以双下划线开头的主题
- `--under-replicated-partitions`：找出一个或多个副本与首领不同步的分区
- `--at-min-isr-partitions`：列出副本数量与配置最少同步副本一致的分区
- `--under-min-isr-partitions`：列出ISR数小于配置的分区
- `--unavailable-partitions`：查找没有首领的分区
## 增加分区
> 增加分区是为了通过降低单个分区的吞吐量来扩展主题容量


```
kafka-topics.sh --bootstrap-server localhost:9092 --alter --topic my_topic --partitions 16
```

## 减少分区
不支持分区的减少，因为如果减少分区，意味着要删除分区中的数据，所以如果觉得分区不合理
- 删除整个topic重新创建
- 创建新的topic，将流量重定向至新的topic中
## 删除topic
- 如果一个topic确定已经不再使用，要进行删除，需要确认`delete.topic.enable=true`，否则即使发送了删除请求也会被忽略。
- 删除是异步的，发送请求后，topic被打上了删除标签，，但可能不会立即删除，取决于数据量和清理策略。
```
kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic my_topic
```

# 消费者组

> 消费者组用于协调消费者从多个主题或单个主题的分区中读取消息。
> 使用`kafka-consumer-groups.sh`来进行操作。

## 查看消费者组列表
```
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list
```

## 查看消费者组详情
```
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe  --group ${消费者组名}
```
## 删除消费者组
```
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --delete --group ${消费者组名}
```
删除消费者组会将已保存的偏移量也一起删除。
删除之前需要确保所有的消费者已经关闭，如果删除非空的消费组会抛出异常。

## 偏移量的管理
当消费者因为某些原因需要重新读取消息或因为无法正常处理某些消息需要跳过这些时，可以进行偏移量重置

### 导出偏移量
将消费者组的偏移量导出到CSV文件。在需要的时候导入或回滚偏移量

```
# 导出将topic 偏移量导出到一个offsets.csv文件中
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --export --group my_consumer --topic my_topic --reset-offsets --to-current --dry-run > offsets.csv
```
### 导入偏移量
将导出的文件中偏移量修改为想要的值，然后再导入进去
导入偏移量之前，需要关闭所有消费者。

```
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --reset-offsets --group my_consumer --from-file offsets.csv --execute
```


# 动态配置变更
> 使用 `kafka-config.sh`可以在主题、客户端、broker等运行时动态更新配置参数。
## 覆盖topic的配置
```
#格式
kafka-configs.sh --bootstrap-server localhost:9092 --alter --entity-types topics --entity-name my_topic --add-config <key>=<value>,<key>=<value>

#将主题数据保留设置为1小时
kafka-configs.sh --bootstrap-server localhost:9092 --alter --entity-types topics --entity-name my_topic --add-config retention.ms=3600000
```

### 覆盖broker的默认配置
- `min.insync.replicas`：用于确认消息最少被几个副本写入（ISR数）
- `unclean.leader.election.enable`：允许一个不同步副本被选举为首领，这样可能会导致数据丢失，但是可以快速恢复Kafka集群
- `max.connections`：broker的最大连接数
### 查看被覆盖的配置
```
kafka-configs.sh --bootstrap-server localhost:9092 --describe --entity-type topics --entity-name my_topic
```
### 移除动态配置
```
kafka-configs.sh --bootstrap-server localhost:9092 --alter --entity-type topics --entity-name my_topic --delete-config retention.ms
```

# 生产和消费

## 控制台生产者
```
kafka-console-producer.sh --topic ${topic} --broker-list localhost:9092
kafka-console-producer.sh --topic ${topic} --bootstrap-server localhost:9092
```

## 控制台消费者

```

kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic ${topic}
#回放所有数据
kafka-console-consumer.sh --bootstrap-server localhost:9091 --topic ${topic} --from-beginning
# 匹配主题
kafka-console-consumer.sh --bootstrap-server localhost:9092 --whitelist 'my.*'
```

### 配置参数
> 通过使用 `--consumer.config <config-file>`执行消费者配置文件
> 通过使用`--consumer-property <key>=<value>`，key是参数名 value是参数值

- `--formatter`：执行消息解码的类名 
	- `kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic ${topic} --formatter kafka.tools.ChecksumMessageFormatter`
- `--from-beginning`：指定从旧偏移量开始读取，如果不指定，从最开始读取
- `--max-messages`：指定退出前最多读取多少条消息
- `--parrention`：读取指定分区的数据
- `--offset`：
	- 整数：从指定位置读取
	- earliest：从起始位置读取
	- latest：从最近位置读取
- `--skip-message-on-error`：出现错误就跳过消息

### 读取偏移量主题
>想要了解某个消费者群组是否在提交偏移量，或者提交的频率，可以通过控制台消费者读取`__consumer_offsets`这个topic来查看

```
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic __consumer_offsets --from-beginning --max-messages 1 --formatter "kafka.coordinator.group.GroupMetadataManager\$OffsetsMessageFormater" --consumer-property exclude.internal.topics = false
```

# 分区管理
- 重新选举首领
- 将分区分配给指定的broker
## 首领选举
```
#给所有主题启动一个首选首领选举
kafka-leader-election.sh --bootstrap-server localhost:9092 --election-type PREFERRED --all-topic-partitions
```
- `--topic`：指定具体topic名称
- `--partition`：指定具体的分区

## 修改分区副本
- `broker`的负载分布不均衡，自动首领选举无法解决
- `broker`离线，造成分区分布不均匀
- 新加了broker，想要快速分区

假设现在有一个包含4个broker的集群，新添加了2个broker，希望将两个topic迁移到 broker5和broker6中
- 创建包含topic清单的JSON文件
```
topics.json
{
	"topics":[
		{
			"topic":"foo1"
		},
		{
			"topic":"foo2"
		}
	],
	"version":1
}
```
- 执行命令
```
kafka-reassign-partitions.sh  --bootstrap-server localhost:9092 --topics-to0move-json-file topics.json --broker-list 5,6 --generate
```

### 移除某个borker
- 可以利用 `Cruise Control`提供的broker的降级功能来处理，可以安全的将broker的领导全转移出去

## 转储日志
查看日志片段中的数据

```
kafka-dump-log.sh --files /tmp/kafka-logs/my_topic-0/00000000000000000000.log
kafka-dump-log.sh --files /tmp/kafka-logs/my_topic-0/00000000000000000000.log --print-data-log   
```