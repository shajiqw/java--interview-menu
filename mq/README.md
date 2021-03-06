### 为什么要使用消息队列？  
 
先说一下消息队列的使用场景吧，比较核心的有个作用解耦、异步、削峰
###### 解耦
服务A要调用BCD三个服务，通过接口调用如果后来又加了一个服务E服务要调用接口，又需要在A服务业务代码中加入调用服务E接口的逻辑，如果后来C服务不需要A服务调用了，又需要在A服务业务代码中删除相关的逻辑，这就导致了服务A与服务BCDE耦合在了一起，可以使用MQ发送消息，服务BCDE接受到消息后各自进行业务处理，起到解耦的作用。


###### 异步
服务A要调用BCD三个服务,服务A本身业务逻辑只需要执行100ms,B,C,D三个服务总耗时900ms导致用户调用服务A需要将近1s才能收到返回，很慢。可以使用MQ发送消息，服务BCDE接受到消息后各自进行业务处理，起到异步的作用。

###### 削峰

比方说，有个系统A 每天0点到11点，风平浪静，每秒并发请求100个。结果每次一到11点-13点，每秒并发请求数量突然暴增到了1W条。但是系统最大的处理能力就只能是每秒1000个请求，这就尴尬了，系统可能直接宕机，这时候就可以让请求先发送到Mq,系统A从MQ中拉取消息处理请求。


---
### 消息队列有什么优点和缺点？
###### 优点  
- 就是上面描述的三个


###### 缺点

- 系统可用性降低：一旦MQ发生故障，系统A就没法发送消息到MQ了,BCD也没法消费到消息了，整个系统就没发运转了，宕机
- 2、系统复杂性提高：引入MQ导致需要考虑的问题变多，比如消息重复发送、消息重复消费、消息丢失、消息顺序、消息积压
- 3、一致性问题:给系统A发请求，本来要求服务ABCD四个服务接口都调用成功，后才能返回，使用mq后会导致ABC成功，D失败但服务A返回了成功，但实际执行结果是失败的。


---
### kafka、activemq、rabbitmq、rocketmq都有什么区别以及使用哪些场景？  

特性 | activemq | rabbitmq |  rocketmq | kafka |
------|---|---|---|---|
单机吞吐量| 万级吞吐量比rocketmq和kafka要低了一个数量级|万级吞吐量比rocketmq和kafka要低了一个数量级|10万级，rokcetmq也是可以支持高吞吐的一种mq|10万级，这是kafka最大的优点，就是吞吐量高，一般配合大数据类的系统来进行实时数据计算、日志采集|
topic数量对吞吐量的影响 || |topic可以到达几百，几千个的级别，吞吐量会有小幅度的下降,可支持大量topic是一大优势。|topic从几十个到几百个的时候，吞吐量会大幅度下降。所以在同等机器下，kafka尽量保证topic数量不要过多。如果要支撑大规模topic，需要增加更多的机器资源|
时效性|ms级|微秒级，延时低是一大特点|ms级|ms级|
可用性|高，基于主从架构实现高可用性|高，基于主从架构实现高可用性|非常高，分布式架构|非常高，kafka是分布式的，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用
消息可靠性|有较低的概率丢失数据||经过参数优化配置，消息可以做到0丢失|经过参数优化配置，消息可以做到0丢失|
功能支持|MQ领域的功能极其完备|基于erlang开发，所以并发能力很强，性能极其好，延时很低|MQ功能较为完善，还是分布式的，扩展性好|功能较为简单，主要支持简单的MQ功能，在大数据领域的实时计算以及日志采集被大规模使用|
总结|非常成熟，功能强大，在早些年业内大量的公司以及项目中都有应用,偶尔会有较低概率丢失消息,现在社区以及国内应用都越来越少，官方社区现在对ActiveMQ,5.x维护越来越少，几个月才发布一个版本,主要是基于解耦和异步来用的，较少在大规模吞吐的场景中使用|erlang语言开发，性能极其好，延时很低,吞吐量到万级，MQ功能比较完备开源提供的管理界面非常棒，用起来很好用,社区相对比较活跃，几乎每个月都发布几个版本分,在国内一些互联网公司近几年用rabbitmq也比较多一些   但是问题也是显而易见的，RabbitMQ确实吞吐量会低一些，这是因为他做的实现机制比较重。  erlang开发，很难去看懂源码，基本职能依赖于开源社区的快速维护和修复bug。rabbitmq集群动态扩展会很麻烦，不过这个我觉得还好。其实主要是erlang语言本身带来的问题。很难读源码，很难定制和掌控。|接口简单易用，可以做到大规模吞吐，性能也非常好，分布式扩展也很方便，社区维护还可以，可靠性和可用性都是ok的，还可以支撑大规模的topic数量，支持复杂MQ业务场景,而且一个很大的优势在于，源码是java，我们可以自己阅读源码，定制自己公司的MQ，可以掌控 |kafka的特点其实很明显，就是仅仅提供较少的核心功能，但是提供超高的吞吐量，ms级的延迟，极高的可用性以及可靠性，而且分布式可以任意扩展 ,同时kafka最好是支撑较少的topic数量即可，保证其超高吞吐量,　kafka唯一的一点劣势是有可能消息重复消费，那么对数据准确性会造成极其轻微的影响，在大数据领域中以及日志采集中，这点轻微影响可以忽略  

---

### 如何保证消息不被重复消费？如何保证消息消费的幂等性？


-  如果你拿个数据要写库，你先根据主键查一下，如果这数据都有了，你就别插入了，update 一下好吧。

-  如果你拿到这个消息做 Redis的set的操作， 无论set几次结果都是一样的，set操作本来就算幂等操作。

- 如果你不是上面两种场景，那做的稍微复杂一点，准备一个第三方介质，来做消费记录。以redis为例，你需要让生产者发送每条数据的时候，里面加一个全局唯一的 id，只要消费过该消息，就将id和消息数据写入redis，消费者开始消费前，先根据这个id查redis中有没有消费记录，消费过了，就别处理了，保证别重复处理相同的消息即可。

- 数据库的唯一键约束也可以保证不会重复插入多条，因为重复插入多条只会报错，不会导致数据库中出现脏数据。
---

### rocketmq如何保证消息不丢失?

###### 生产者-->MQ消息不丢失方案
1. 发送half消息到MQ，试探一下MQ是否正常
2. 如果发送half消息失败，说明mq可能挂了，或者网络故障了，所以导致half消息都没有发送成功，就不用进行接下来的业务逻辑了,如果成功了，则commit这条half消息
3. 如果commit失败 rocketmq会回调一个接口询问这条消息是否要commit或者是rollback  

###### MQ本身消息不丢失方案
1. 调整刷盘模式为同步刷盘
2. 使用主从同步架构

###### 消费者消息不丢失方案
1. 手动提交offset(ack)
2. 做好防止重复消费的机制(幂等性)
---

### rocketmq消息如何按顺序消费？  
1. 让需要顺序消费的消息按顺序进入同一个MessageQueue,因为一个MessageQueue只能交给一个消费者来处理
2. 设置自己的消息处理失败方案，也就是不能让失败的消息进入重试队列，而是等待一会儿继续处理这批消息
---