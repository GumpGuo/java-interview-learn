# 1、Kafka是什么 #
1. kafka是一个可扩容弄的、容错的、分布式的、基于分区的、多副本的，基于Zookeeper框架的发布-订阅消息系统，是由Scala语言开发的，他的Java版本称为Jafka。

# 2、Kafka的设计
 - Kafka中的重要组件
    - 1、Producer：消息生产者
    - 2、Broker：一个Kafka节点就是一个Broker，多个Kafka可组成一个集群。
    - 3、Topic：消息主题。每条发布到Kafka集群的消息都会归集于此。
    - 4、Partition：分区Partition