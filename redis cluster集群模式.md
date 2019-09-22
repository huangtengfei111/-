# redis cluster集群模式

## 问题

```
1. 当某一master node发生宕机，它对应的hash solt 会迁移到其他的master node上，对应的数据也会跟着迁移过去吗
2. 04视频待补充
```



## redis的集群架构

```
redis cluster
	可以有多个master node，每个master node 可以有多个salve node

（1）自动将数据进行分片，每个master上放一部分数据
（2）提供内置的高可用支持，部分master不可用时，还是可以继续工作的

在redis cluster架构下，每个redis要放开两个端口号，比如一个是6379，另外一个就是加10000的端口号，比如16379

16379端口号是用来进行节点间通信的，也就是cluster bus的东西，集群总线。cluster bus的通信，用来进行故障检测，配置更新，故障转移授权

cluster bus用了另外一种二进制的协议，主要用于节点间进行高效的数据交换，占用更少的网络带宽和处理时间

```



 ![最老土的hash算法以及弊端](E:\U盘\视频\25_你能聊聊redis cluster集群模式的原理吗？\03_数据分布算法：hash+一致性hash+redis cluster的hash slot\最老土的hash算法以及弊端.png)

```
传统的hash取模弊端
 当有3个master node时，写数据时对3取模，当2号master宕机，所有数据查询时对2进行取模，导致大部分数据都找不到原来的数据，所有的数据与原来存入数据的node发生了错位
```







![redis cluster hash slot算法](E:\U盘\视频\25_你能聊聊redis cluster集群模式的原理吗？\03_数据分布算法：hash+一致性hash+redis cluster的hash slot\redis cluster hash slot算法.png)

```
redis cluster的hash slot算法

redis cluster有固定的16384个hash slot，对每个key计算CRC16值，然后对16384取模，可以获取key对应的hash slot

redis cluster中每个master都会持有部分slot，比如有3个master，那么可能每个master持有5000多个hash slot

hash slot让node的增加和移除很简单，增加一个master，就将其他master的hash slot移动部分过去，减少一个master，就将它的hash slot移动到其他master上去
当一个master发生宕机，可以很短时间内将该master的对应的hash slot迁移到其他的master node上

移动hash slot的成本是非常低的

客户端的api，可以对指定的数据，让他们走同一个hash slot，通过hash tag来实现
```





```

```



