Kafka的生产者在发送数据到`broker`，如果`broker`在收到消息后崩溃或者断开连接，导致生产者没有接收到成功响应，这时会触发重试机制，这种场景下回导致消息的重复。

## 冥等性
>如果一个操作被执行多次，结果与执行一次相同，那么这个操作就是冥等的。

## 冥等生产者
### 冥等生产者的工作原理

- 启用了冥等生产者，那么每条消息都将包含**生产者的ID(PID)和序列号**。信息将与**目标的topic和分区**组合在一起，用于唯一表示一条消息。
- `broker`收到了之前已经收到的消息，那么将拒绝接收该消息，并且返回错误信息。生产者记录该错误，反应在指标中，但是不抛出异常，也不触发告警。



### 生产者故障时冥等是怎样的？
- **生产者重启**：当一个生产者发生故障时，我们通常会创建新的生产者来代替他——手动重启或者自动拉起等，这是生产者在连接`borker`时会生成生产者ID，每次初始化时，都会产生一个新的id，这意味着如果一个生产者发生故障后，重启后的生产者发送了一条旧生产者已经发送过的数据，那么broker无法检测到重复。

- **broker发生故障**：当一个`broker`发生故障，控制器将会为首领副本在该`broker`上的分区重新选举首领。
	- 每次生成新消息时，首领副本会用最后5个序列号更新内存中生产者状态，跟随者副本在复制消息时，会将该信息更新到自己的内存，所以当跟随者成为新首领时，已经有了最新的序列号来验证新消息的生成。
	- `broker`发生崩溃，但是没有更新最后一个快照时，由于生产者ID和序列号也是Kafak消息格式的一部分，在进行故障恢复时，通过旧快照+分区最新日志，可以恢复生产者的状态，等故障恢复完成之后，一个新的快照就保存好了。
	- **不彻底的首领选举**：`broker`期望接收**消息2**后面就跟随的**消息3**，但是接收到了**消息27**，这时`broker`会抛出“乱序”的错误，如果使用了不带**事务**的冥等生产者，这个错误可能会被忽略。该场景下 有可能消息3-消息26发生了丢失，需要检查是否发生了**不彻底的首领选举**。
### 冥等生产者的局限性
- 仅能防止由生产者内部重试逻辑引起的消息重复。
- 不能防止代码中重复发送逻辑，因为这种对于kafka来说是两个消息
- 使用过程中建议使用生产者内置的重试机制，而不是在应用程序中自行进行重试。
### 使用冥等生产者
- `enable.idempotence`：生产者配置设置该值为true，在`acks=all`的配置下，性能基本不会有差异
- `transactional.id`：可选参数，主要用于事务
#### 启用冥等性后生产者的变化
 - 生产者启动时会额外调用一个API来获取生产者ID
 - 每个消息批次中第一条消息包含生产者ID和序列号（批次中其他消息基于第一条消息递增）
 - broker会验证每一个生产者实例的序列号，保证没有重复消息
 - 每个分区的消息顺序都将得到保证。

## 总结
通过启用Kafka的冥等生产者，可以保证每条消息在分区中仅被写入一次，即使触发了生产者重试机制也不会导致消息重复。