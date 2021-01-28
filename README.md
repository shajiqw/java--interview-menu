# java--interview-menu
##MQ
[第一章节笔记](/mq/README.md)
### 电商系统如何防止库存超卖?

###### 方案一

使用Redis分布式锁或者Zookeeper分布式锁,加锁扣减库存操作，缺点是性能不高。

###### 方案二

基于Redis Lua脚本 原子化库存查询+库存扣减操作


###### 方案三

基于redis 队列(List)
通过pop扣减库存，缺点是如果秒杀商品数量过大，会消耗大量内存

###### 方案四

基于数据库Version乐观锁，先查询库存版本号，同过版本号去修改库存，失败轮训重试，缺点是会消耗大量CPU

---


### 分布式事务有哪些方案？

###### XA规范与2PC协议
 ![image](http://assets.processon.com/chart_image/6001a6e86376897ae07dd1fb.png)

###### 3PC
 ![image](http://assets.processon.com/chart_image/6001acbae401fd661aaf9414.png)
 
 ###### TCC
 ![image](http://assets.processon.com/chart_image/6001b1665653bb1b2eed0c99.png?_=1611759255640)


###### 本地消息表方案

![image](http://assets.processon.com/chart_image/6002d3e81e08534bec21ccd4.png)

###### 可靠消息最终一致性方案
![image](http://assets.processon.com/chart_image/6002d44de0b34d45d15b0e0f.png)

###### 最大努力通知方案
![image](http://assets.processon.com/chart_image/6002d482e401fd261bc53d26.png)


---

### 分布式搜索引擎的架构是怎么样的?为啥是分布式的？
---

### 分布式搜索引擎写入和查询的工作流程是什么样的？
---

### 分布式搜索引擎在几十亿数据量级的场景下如何优化查询性能？
---

### 你们公司生产环境的分布式搜索引擎是怎么部署的呢？
---

### 来聊聊redis的线程模型吧？为啥单线程还能有很高的效率？
---

### redis都有哪些数据类型？分别在哪些场景下使用比较合适呢？
---

### redis的过期策略能介绍一下？要不你再手写一个LRU？
---

### redis 如何通过读写分离来承载读请求QPS超过10万+ ？
---

### redis replication以及master持久化对主从架构的安全意义？
---

### redis主从复制原理、断点续传、无磁盘化复制、过期key处理
---

### redis主从架构下如何才能做到99.99%的高可用性？
---

### redis哨兵主备切换的数据丢失问题：异步复制、集群脑裂
---

### 总结：怎么保证redis是高并发以及高可用的？
---

###  redis持久化机制有哪些？
---

### 你能说说我们一般如何应对缓存雪崩以及穿透问题吗？
---

### 你能说说redis的并发竞争问题该如何解决吗？
---

### 你们公司生产环境的redis集群的部署架构是什么样的？
---

### JVM GC机制总结

![image](http://assets.processon.com/chart_image/5fe2ff321e08535fa5dd809f.png)



#### Mysql总结，解释说明一下MVCC多版本并发机制？undolog和redolog的作用是什么?索引的生效机制大概有哪些？

![image](http://assets.processon.com/chart_image/6006891563768934926a20c0.png)





