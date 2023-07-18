## 1、Zookeeper的ZAB协议？
 - 角色：
   - Leader：负责整个Zookeeper集群工作机制中的核心，主要工作有以下两个：
     - 事务请求的唯一调度和和处理者，保证集群事务处理的顺序性
     - 集群内部各个服务器的调度者
   - Follower：他是Leader的追随者，主要工作有以下三个
     - 处理客户端的非事务请求，转发事务请求给Leader服务器
     - 参于事务请求Proposal的投票
     - 参与Leader的选举投票
   - Observer：是 zookeeper 自 3.3.0 开始引入的一个角色，它不参与事务请求 Proposal 的投票，也不参与 Leader 选举投票，只提供非事务的服务（查询），通常在不影响集群事务处理能力的前提下提升集群的非事务处理能力。
 - 三种状态：
   - LOOKING：处在这个状态的时候，会进入Leader选举状态
   - FOLLOWING：Follower服务器和Leader服务器保持同步时的状态。
   - LEADING：Leader服务器作为主进程领导者的状态。
 - 消息广播模式：ACK和事务提交！
 - 崩溃恢复：
 【参考文档：https://segmentfault.com/a/1190000037550497】

## 2、【https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html】


## 3、Zookeeper是什么？
 - 分布式的