

> kafka是一套消息引擎系统，也是分布式流处理平台



##### kafka在设计之初就旨在提供三个方面特性

- 提供一套API实现生产者和消费者
- 降低网络传输和磁盘存储开销
- 实现高伸缩性架构

kafka版本号

- 0.7

  我们先从 0.7 版本说起，实际上也没什么可说的，这是最早开源时的“上古”版本了，以至于我也从来都没有接触过。这个版本只提供了最基础的消息队列功能，甚至连副本机制都没有，我实在想不出有什么理由你要使用这个版本，因此一旦有人向你推荐这个版本，果断走开就好了。

- 0.8

  Kafka 从 0.7 时代演进到 0.8 之后正式引入了副本机制，至此 Kafka 成为了一个真正意义上完备的分布式高可靠消息队列解决方案。有了副本备份机制，Kafka 就能够比较好地做到消息无丢失。那时候生产和消费消息使用的还是老版本的客户端 API，所谓的老版本是指当你用它们的 API 开发生产者和消费者应用时，你需要指定 ZooKeeper 的地址而非 Broker 的地址。

  如果你现在尚不能理解这两者的区别也没关系，我会在专栏的后续文章中详细介绍它们。老版本客户端有很多的问题，特别是生产者 API，它默认使用同步方式发送消息，可以想见其吞吐量一定不会太高。虽然它也支持异步的方式，但实际场景中可能会造成消息的丢失，因此 0.8.2.0 版本社区引入了新版本 Producer API，即需要指定 Broker 地址的 Producer。

  据我所知，国内依然有少部分用户在使用 0.8.1.1、0.8.2 版本。**我的建议是尽量使用比较新的版本。如果你不能升级大版本，我也建议你至少要升级到 0.8.2.2 这个版本，因为该版本中老版本消费者 API 是比较稳定的。另外即使你升到了 0.8.2.2，也不要使用新版本 Producer API，此时它的 Bug 还非常多。**

- 0.9

  

- 0.10

  0.10.0.0 是里程碑式的大版本，因为该版本引入了 Kafka Streams。从这个版本起，Kafka 正式升级成分布式流处理平台，虽然此时的 Kafka Streams 还基本不能线上部署使用。0.10 大版本包含两个小版本：0.10.1 和 0.10.2，它们的主要功能变更都是在 Kafka Streams 组件上。如果你把 Kafka 用作消息引擎，实际上该版本并没有太多的功能提升。不过在我的印象中自 0.10.2.2 版本起，新版本 Consumer API 算是比较稳定了。**如果你依然在使用 0.10 大版本，我强烈建议你至少升级到 0.10.2.2 然后使用新版本 Consumer API。还有个事情不得不提，0.10.2.2 修复了一个可能导致 Producer 性能降低的 Bug。基于性能的缘故你也应该升级到** 0.10.2.2。

- 0.11

  在 2017 年 6 月，社区发布了 0.11.0.0 版本，引入了两个重量级的功能变更：一个是提供幂等性 Producer API 以及事务（Transaction） API；另一个是对 Kafka 消息格式做了重构。

  

  前一个好像更加吸引眼球一些，毕竟 Producer 实现幂等性以及支持事务都是 Kafka 实现流处理结果正确性的基石。没有它们，Kafka Streams 在做流处理时无法向批处理那样保证结果的正确性。当然同样是由于刚推出，此时的事务 API 有一些 Bug，不算十分稳定。另外事务 API 主要是为 Kafka Streams 应用服务的，实际使用场景中用户利用事务 API 自行编写程序的成功案例并不多见。

  第二个重磅改进是消息格式的变化。虽然它对用户是透明的，但是它带来的深远影响将一直持续。因为格式变更引起消息格式转换而导致的性能问题在生产环境中屡见不鲜，所以你一定要谨慎对待 0.11 版本的这个变化。不得不说的是，这个版本中各个大功能组件都变得非常稳定了，国内该版本的用户也很多，应该算是目前最主流的版本之一了。也正是因为这个缘故，社区为 0.11 大版本特意推出了 3 个 Patch 版本，足见它的受欢迎程度。我的建议是，如果你对 1.0 版本是否适用于线上环境依然感到困惑，那么至少将你的环境升级到 0.11.0.3，因为这个版本的消息引擎功能已经非常完善了。

- 1.0
- 1.2





#### Kafka重要参数

- borker设置参数

log.dirs

log.dir

zookeeper.connect ：zk1:2181,zk2:2181,zk3:2181/kafka1



auto.create.topics.enable：是否允许自动创建 Topic。

unclean.leader.election.enable：是否允许 Unclean Leader 选举。

auto.leader.rebalance.enable：是否允许定期进行 Leader 选举。



log.retention.{hours|minutes|ms}：这是个“三兄弟”，都是控制一条消息数据被保存多长时间。从优先级上来说 ms 设置最高、minutes 次之、hours 最低。

log.retention.bytes：这是指定 Broker 为消息保存的总磁盘容量大小。

message.max.bytes：控制 Broker 能够接收的最大消息大小。

- topic参数

  retention.ms：规定了该 Topic 消息被保存的时长。默认是 7 天，即该 Topic 只保存最近 7 天的消息。一旦设置了这个值，它会覆盖掉 Broker 端的全局参数值。

  retention.bytes：规定了要为该 Topic 预留多大的磁盘空间。和全局参数作用相似，这个值通常在多租户的 Kafka 集群中会有用武之地。当前默认值是 -1，表示可以无限使用磁盘空间。

  max.message.bytes 一次发送大小  



bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic transaction --partitions 1 --replication-factor 1 --config retention.ms=15552000000 --config max.message.bytes=5242880

bin/kafka-configs.sh --zookeeper localhost:2181 --entity-type topics --entity-name transaction --alter --add-config max.message.bytes=10485760



- JVM参数：

  1.7

  1.8

堆大小调到6G  

使用G1收集器

> export KAFKA_HEAP_OPTS=--Xms6g  --Xmx6g$> export KAFKA_JVM_PERFORMANCE_OPTS= -server -XX:+UseG1GC -XX:MaxGCPauseMillis=20 -XX:InitiatingHeapOccupancyPercent=35 -XX:+ExplicitGCInvokesConcurrent -Djava.awt.headless=true$> bin/kafka-server-start.sh config/server.properties

- OS参数

  文件描述符限制: ulimit -n 1000000

  文件系统类型: ext3 ext4 xfs

  Swappiness

  提交时间

  > 最后是提交时间或者说是 Flush 落盘时间。向 Kafka 发送数据并不是真要等数据被写入磁盘才会认为成功，而是只要数据被写入到操作系统的页缓存（Page Cache）上就可以了，随后操作系统根据 LRU 算法会定期将页缓存上的“脏”数据落盘到物理磁盘上。这个定期就是由提交时间来确定的，默认是 5 秒。一般情况下我们会认为这个时间太频繁了，可以适当地增加提交间隔来降低物理磁盘的写操作。当然你可能会有这样的疑问：如果在页缓存中的数据在写入到磁盘前机器宕机了，那岂不是数据就丢失了。的确，这种情况数据确实就丢失了，但鉴于 Kafka 在软件层面已经提供了多副本的冗余机制，因此这里稍微拉大提交间隔去换取性能还是一个合理的做法。





#### Kafk压缩算法

kafka在0.11.0版本中优化消息存储（V2版消息）

V1版本消息

V2版本消息

v1和V2消息组成都是由：消息集合（message Set）以及消息（Message）组成。

优化：

- 将消息中的共同部分提取出放到消息集合中，使消息体变小。
- 由原来压缩多条消息改为压缩消息集合

精华

- 在Kafka中，压缩可能发生在两个地方：生产者端和Broker端。
- 让Broker端重新压缩消息的2种例外情况：Broker端指定了和Producer端不同的压缩算法；Broker端发生了消息格式转换。（Broker端解压缩/压缩会造成Zero Copy的失效）（造成Broker端解压/重压缩可能是为兼容老版Consumer的消息格式；Broker会建压缩进行校验消息）
- Producer端压缩---->Broker端保持---->Consumer端进行解压缩
- 对Kafka而言：
  - 在压缩/解压缩吞吐量方面:  LZ4 > Snappy > ZStandard  和GZIP；(每秒压缩/解压缩 大小)
  - 在压缩比方面: ZStandard >LZ4>GZIP>Snappy （压缩前大小/压缩后大小）



#### 无消息丢失配置

