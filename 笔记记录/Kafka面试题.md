# 1、Kafka是什么 #
1. kafka是一个可扩容弄的、容错的、分布式的、基于分区的、多副本的，基于Zookeeper框架的发布-订阅消息系统，是由Scala语言开发的，他的Java版本称为Jafka。

# 2、Kafka的设计
 - Kafka中的重要组件
    - 1、Producer：消息生产者
    - 2、Broker：一个Kafka节点就是一个Broker，多个Kafka可组成一个集群。
    - 3、Topic：消息主题。每条发布到Kafka集群的消息都会归集于此。
    - 4、Partition：分区Partition
    - 5、Consumer：从kafka集群中消费消息的终端或服务
    - 6、Consumer Group：每个Consumer都属于一个ConsumerGroup，每条消息都只能被ConsumerGroup中的一个Consumer消费，但可以被多个ConsumerGroup消费。
    - 7、Replica：Partition的副本，用来保障Partition的高可用性。
    - 8、Controller：Kafka集群中的其中一个服务器，用来Leader-election以及各种Failover操作。
    - 9、Zookeeper：kafka通过Zookeeper来存储集群中的meta消息。
   
# 3、Kafka高性能的原因：
 - 利用了PageCache
      - 读数据时。不直接读取磁盘数据，而是先通过读取PageCache数据，查看是否有数据，如果有则直接返回。如果没有，则查询磁盘数据，再将数据放到PageCache中。
      - 写数据时，不直接写入磁盘，而是写入PageCache中。页缓存数据同步到磁盘文件是有操作系统来控制的。即操作系统通过一个内核后台线程，没5秒检查一次是否需要将缓存数据同步到磁盘，如果超过指定的时间，或者超过指定的大小，则将页缓存数据同步到磁盘。
 - 零拷贝技术：利用了DMA技术。 减少数据拷贝   Read Buffer > WebApplicationBuffer >  Socket Buffer  ..x  Read Buffer > NICBuffer 
 - 磁盘顺序写: 
      - 当broker接收到producer发送过来的消息是，需要根据消息的主题和分区信息，将该消息写入到该分区的当前最后segment文件中，文件的写入方式是追加写。
      - 由于是对segment文件追加写，故实现了对磁盘文件的顺序写，避免磁盘随机写时的磁盘寻道的开销，同时由于追加写，故写入的速度与磁盘文件大小无关。
 - pull拉模式：Kafka消费者采用pull拉模式来消费消息。

# 4、Kafka文件搞笑存储设计原理
 - 1、kafka把topic中一个Partition大文件分成多个小文件分段，就容易删除已经消费完成的文件，减少磁盘占用。
 - 2、通过索引信息可以快速定位到Message和确定Response的大小 
 - 3、通过索引文件全部映射到memory，可以避免Segment问价的磁盘I/O操作。
 - 4、通过索引文件稀疏存储，可以大幅减少索引文件元数据占用空间大小。
   
# 5、Kafka的优缺点
 - 优点 
   - 高性能、高吞吐量、低延迟
   - 高可用
   - 高并发
   - 容错性
   - 高扩扎性
 - 缺点：
   - 没有完整的监控工具
   - 不支持通配符主题选择。
   
# 6、Kafka的应用场景
 - 日志聚合
 - 消息系统
 - 系统解耦
 - 流量削峰
 - 异步处理

# 7、Kafka中分区的原则
 - 指明分区的情况下，直接发送到指定分区
 - 不指明分区，但指定key的情况下，根据key计算hash值。然后用分区数取余得到分区
 - 不指明分区，也不指定key的情况下，第一次调用时随机生成一个整数（后面每次调用在这个整数上递增），将这个数和分组总数取余得到分区值。就是常说的round-robin算法。

# 8、为什么把消息分区？
 - 方便在集群中扩展
 - 提高并发能力

# 9、Kafka中生产者运行流程
 -  1、一条消息首先被封装成ProduceRecord对象。
 -  2、对该对象进行序列化处理（可以使用默认，也可以自定义序列化）
 -  3、对消息进行分区，分区时候需要获取集群的元数据，决定这个消息送到哪个主题的哪个分区
 -  4、分区好的消息不会直接发送到服务端，而是放入生产者的缓存区，多条消息会被封装成一个批次（Batch）。默认一个批次的大小是16kb
 -  5、Sender线程启动以后会从缓存里面获取可以发送的批次。
 -  6、Sender线程把一个一个批次发送到服务端。

# 10、Kafka中的消息封装
 - 在Kafka中Producer可以Batch的方式推送数据达到提高效率的作用。KafkaProducer可以将消息在内存中累积到一定数量后作为一个Batch发送请求。Batch的数量大小可以通过Producer的参数进行控制，可以从三个维度进行控制。
   - 累计消费的数量
   - 累计的时间间隔
   - 累计的数据大小
 - 通过增加Batch的大小，可以减少网络请求和I/O的频次。  

# 11、 Kafka的消费模式
 - Kafka采用Pull拉取的模式。
 - 采用Pull模式的好处是，消费者可以自主决定是否批量的从Broker拉取数。Pull有个缺点是，如果Broker没有可提供消费的消息，将导致consumer不断在循环中轮询，知道消息到达。为了避免这一点。Kafka有个参数可以让消费者阻塞直到新消息到达。

# 12、 Kafka的负载均衡
 - 分区replica负载均衡：
   - 创建副本的过程：
      - 随机挑选一个startIndex
      - 从startIndex开始按照broker顺序，生成第一个副本
      - 下一轮从start-Index开始，顺序生成第二个副本。
   - 这里的随机挑选也是负载均衡的一部分。让分区尽肯能打散到各个broker
   
 - leader均衡
   - 基于优先副本。也就是挑选AR列表中的第一个brokerId上的副本成为leader
   - 提供了分区自动平衡的功能。auto.leader.rebalance.enable.默认为true。
      - 开启之后，controller会启动一个定时任务，每隔一段时间去轮询所有的broker，计算每个broker节点的分区不平衡率。查看是否超过了设定的阈值，如果超过了自动进行分区迁移。
   
 - consumerGroup负载均衡
   - RangeAssignor：在保证均衡的前提下，将连续的分区分配给消费者。按照topic对应的每个区段分配给Consumer实例。 造成先分配分区的Consumer实例的任务过重。
   - RoundRobinAssignor：在保证均衡的前提下，轮询分配。拿到组内所有的Consumer Topic Partition。按照顺序分发给Consumer。可能造成分区分配的倾斜。
   - StickAssignor：
      - 主要实现了两个功能：
         - 1、主题分区的分配要尽可能的均匀。
         - 2、当Rebalance发生时，尽可能保持上一次的分配方案。

# 13、 Kafka的故障转移（failover）
 - 故障转移指的是，当运行中的容器突然宕机或者意外终止时，kafka能够快速地感知到，并立即启用备用控制器来代替之前失败的控制器。这个过程就是Failover，该过程是自动完成的，无需手动干预。
   -最开始时，Broker 0 是控制器。当 Broker 0 宕机后，ZooKeeper 通过 Watch 机制感知到并删除了 /controller 临时节点。 之后，所有存活的 Broker 开始竞选新的控制器身份。Broker 3 最终赢得了选举，成功地在 ZooKeeper 上重建了 /controller 节点。之后，Broker 3 会从 ZooKeeper 中读取集群元数据信息，并初始化到自己的缓存中。 至此，控制器的 Failover 完成，可以行使正常的工作职责了。
   
# 14、 Zookeeper在Kafka中的作用。
 - Kafka 是一个使用 Zookeeper 构建的分布式系统。Kafka 的各 Broker 在启动时都要在Zookeeper上注册，由Zookeeper统一协调管理。如果任何节点失败，可通过Zookeeper从先前提交的偏移量中恢复，因为它会做周期性提交偏移量工作。同一个Topic的消息会被分成多个分区并将其分布在多个Broker上，这些分区信息及与Broker的对应关系也是Zookeeper在维护。

# 15、Kafka中消息偏移的作用。
 - 生产过程中给消息提供一个顺序ID号，称之为偏移量，偏移量主要作用是惟一的区别分区中的没条消息。Kafka的存储文件都是按照offset.kafka来命名的。
   
# 16、Kafka中Replica、Leader、Follower的概念。
 - Replica： Kafka中的Partition是有序消息日志，为了实现高可用性。需要采用备份机制。将相同的数据复制到多个Broker上，这些备份日志就是Replica，目的是为了防止数据丢失。 
 - Leader：副本中的领导者。负责对外提供服务。
 - Follower：副本中的追随者，被动地追随leader，不能与外界进行交付。只能向leader发送消，请求leader把最新的消息发送给他。从而保持同步。

# 17、Kafka中的Geo-Replication
 - Geo-Replication：异地数据同步技术
   - Kafka官方提供了MirrorMaker组件，作为跨集群的流数据同步方案。借助MirrorMaker，消息可以跨多个数据中心或云区域进行复制。您可以在主动/被动场景中将其用于备份和恢复，或者在主动/主动方案中将数据放置得更靠近用户，或支持数据本地化要求。
   - 它的实现原理比较简单，就是通过从源集群消费消息，然后将消息生产到目标集群，即普通的消息生产和消费。用户只要通过简单的Consumer配置和Producer配置，然后启动Mirror，就可以实现集群之间的准实时的数据同步.

# 22、Kafka中AR、ISR、OSR的概念
 - AR：分区中所有的副本
 - ISR：所有与副本保持一定程度同步的副本（包括主副本）
 - OSR：与主副本滞后过多的副本组成OSR
   
# 23、分区中的副本什么情况下会从ISR中剔除。
 - Leader会维护一个与自己基本保持同步的Replica列表，称为ISR。每个分区都有一份ISR，由Leader动态维护。所谓动态维护就是指如果一个Follower比一个Leader落后太，或者超过一定时间没有发起数据复制请求，则leader将follower从ISR中剔除。

# 24、分区中副本的Leader如果宕机但ISR为空该如何处理？
 - unclean.leader.election参数
   - true：允许OSR成为leader。
   - false：一直等待旧leader恢复。
   
# 25、如何判断一个Broker是否有效？
 - Broker必须可以维护和zookeeper的连接。zookeeper通过心跳机制检查每个节点的连接。
 - 如果broker是个follower，他必须能及时同步leader的消息，延迟不能太久。

# 26、Kafka可以接收的消息最大默认是多少？如何修改
 - 默认是100_0000字节。
 -  Message.max.bytes。
   
# 27、Kafka的ACK机制？
 - Kafka的ACK机制指的是 Kafka发送者发送消息给服务端的发送确认机制。
 - kafka有三种ACK机制。对应的值分别是 -1、0、1
   - 0：相当于异步操作，Producer不需要Leader给予回复，发送完就认为成功。继续发送下一条。
   - 1：Kafka设置的默认值，表示Producer要Leader确认已经成功收到数据才发送下一条。
   - -1：Leader接收到消息后，还必须要求ISR列表里跟Leader保持同步的那些Follower都确认消息已同步，Producer 才发送下一条（批）Message。此机制持久性可靠性最好，但延时性最差。


# 28、Kafka的日志保留与数据清理策略
 - 概念：保留期内保留所有的已经发布的消息，超过保期的数据将被按清理策略进行清理。
   - 清理策略；
      - delete：
      - 压缩：
   
# 29、Kafka的message是什么格式的？
 - Kafka的messag

