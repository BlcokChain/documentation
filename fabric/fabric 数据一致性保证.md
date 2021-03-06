## fabric 数据一致性保证


#### 存储数据结构
* 存储数据结构，区块链大部分数据结构都是每个存储单元包含上一存储单元的hash值，把所有数据连接在一起用来保障数据的前后依赖达到可追溯的特点

![](./images/fabric.consistency.block)	

#### 网络协议

* Gossip是一个带冗余的容错算法，更进一步，Gossip是一个最终一致性算法。虽然无法保证在某个时刻所有节点状态一致，但可以保证在”最终“所有节点一致。因为图中如果同一个channel的peer很多的话，单点leader负载会很高，所以这里面采用分散排序节点和leader的压力来用gossip保证数据的最终一致性
![](./images/fabric.consistency.gossip)

* fabric是中心化的架构，各个org节点选举出leader订阅order节点的消息然后leader再将消息散播给组内其他成员。也就是说整个组织是完全信任leader订阅的消息，如果这里作恶是没有办法防范的，这个是因为联盟链首先是建立在节点都是可信赖的基础上而不像公链建立在不可信的基础上
* 其他节点如果恶意篡改数据，block文件有hash链支撑，如果有暴力编辑修改，事后可以检查出不一致，VSCC会验证交易读操作与状态匹配，peer node一重新启动，会重构stateDb
* peer定期pull from 其他相邻peer，比较差异，如发现本地block-seqno小于其他，则发StateRequestMsg广播请求差异块，放本地缓冲 --> 排序，等待连续后，连续追加到链尾。由于是个相对封闭的网络，所以冗余校验浪费的成本可以忽略不计。

