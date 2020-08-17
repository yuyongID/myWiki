## Python垃圾回收机制

Python GC主要使用引用计数（reference counting）来跟踪和回收垃圾。在引用计数的基础上，通过“标记-清除”（mark and sweep）解决容器对象可能产生的循环引用问题，通过“分代回收”（generation collection）以空间换时间的方法提高垃圾回收效率。

### 1 引用计数

PyObject是每个对象必有的内容，其中`ob_refcnt`就是做为引用计数。当一个对象有新的引用时，它的`ob_refcnt`就会增加，当引用它的对象被删除，它的`ob_refcnt`就会减少.引用计数为0时，该对象生命就结束了。

优点:

1. 简单
2. 实时性

缺点:

1. 维护引用计数消耗资源
2. 循环引用

### 2 标记-清除机制

基本思路是先按需分配，等到没有空闲内存的时候从寄存器和程序栈上的引用出发，遍历以对象为节点、以引用为边构成的图，把所有可以访问到的对象打上标记，然后清扫一遍内存空间，把所有没标记的对象释放。

### 3 分代技术

分代回收的整体思想是：将系统中的所有内存块根据其存活时间划分为不同的集合，每个集合就成为一个“代”，垃圾收集频率随着“代”的存活时间的增大而减小，存活时间通常利用经过几次垃圾回收来度量。

Python默认定义了三代对象集合，索引数越大，对象存活时间越长。

## reids 数据类型
| 数据类型 | 最大存储 | 特性 | 应用场景 |
|:---|:---|:---|:---|
| string【SET runoobkey redis 】 | 512MB | 可以包含任何数据,比如jpg图片或者序列化的对象,一个键最大能存储512M | 常规key-value缓存应用。常规计数: 微博数, 粉丝数。|
| hash 【HMSET runoobkey name "redis tutorial" description "redis basic commands for caching" likes 20 visitors 23000】 | 每个 hash 可以存储 2^32 -1 键值对（40多亿） | 适合存储对象,并且可以像数据库中update一个属性一样只修改某一项属性值(Memcached中需要取出整个字符串反序列化成对象修改完再序列化存回去) | 存储、读取、修改用户属性。用户对象包含姓名，年龄，生日等信息 |
| list 【LPUSH runoobkey redis】| 列表最多可存储 2^32 - 1 元素（40多亿） | 增删快,提供了操作某一段元素的API |1、最新消息排行等功能(比如朋友圈的时间线) 2、消息队列 |
| set 【SADD runoobkey redis】| 集合中最大的成员数为 232 - 1 | 1、添加、删除、查找的复杂度都是O(1)  2、为集合提供了求交集、并集、差集等操作 |1、共同好友 2、利用唯一性,统计访问网站的所有独立ip 3、好友推荐时,根据tag求交集,大于某个阈值就可以推荐 |
| zset 【ZADD runoobkey 1 redis】| | 数据插入集合时,已经进行天然排序 | 1、排行榜 2、带权重的消息队列 |

## 压测

### 线下环境压测的意义

缩比的压力测试环境压测出来的数据，完全不能用做生产环境的参考。但是这样的测试环境在研发阶段有其重要价值：

1. 上线前发现性能瓶颈：链接异常为释放，线程数过多等；
2. 应用基线数据：每次上线前压一次，对比历史数据即可发现是否有提升；
3. 快速吸能调优及验证；

### 压测制定的考虑

1. 流量模型的去定。可以基于业务的一段时间各接口的峰值，最后拼装成流量模型；
2. 脏数据问题。有专用测试账户和单独请求头部两种方式，最终数据落到影子库和影子表里。仿真环境要多测试，数据要有多重机制保障；
3. 监控覆盖度要足够；
4. 压测扩展。验证其他功能，类似限流，熔断，降级；
5. 参数调优。nginx配置，linux内核参数快速回收等；
6. 缓存和数据库。重要业务有缓存，reids cpu 高是否有模糊匹配，高时间复杂度的指令，mysql 的索引添加，慢查询；
7. mock服务。计费，短信；
8. cpu阈值。50%～70%，响应时间1s为宜；

----

### 分布式锁

常用的分布式锁的实现有三种方式。

- 基于redis实现（利用redis的原子性操作setnx来实现）

  1. 每次进来先检测一下这个key是否实现。如果失效了移除失效锁
  2. 使用setnx原子命令争抢锁。Redis Setnx（**SET** if **N**ot e**X**ists） 命令在指定的 key 不存在时，为 key 设置指定的值，确认返回值为 1。属于 string 的数据类型。
  3. 抢到锁的设置过期时间。EXPIRE key 10086 设置 key 生存时间为 10086 秒

  > 优点：redis本身是内存操作、并且通常是多片部署，因此有这较高的并发控制，可以抗住大量的请求。
  >
  > 缺点：redis本身是缓存，有一定概率出现数据不一致请求。

- 基于 mysql 实现（利用 mysql 的 innodb 的行锁来实现，有两种方式， 排他锁与共享锁）

  - `SELECT ... FOR UPDATE` 是排他锁，其他事物不能读取，也不能写。

  > 为选择的行添加排它锁(X锁)，保证查询到的数据是最新的数据，允许其它事务对该数据加上共享锁(S锁)，但不能修改，只有当前事务可以修改，其它事务需要等当前事务 commit 或 rollback 之后才可以修改加锁的行;

  - `SELECT ... LOCK IN SHARE MODE`是共享锁，其他事务可以读，但不能写。

  > 为选择的行添加共享锁(S锁)，其它事务也可以对该行数据添加S锁，它保证了读取到的是最新的数据，并且不允许别人修改，但是自己也 **不一定** 能够修改，因为可能别的事务也对这个数据加了S锁;

- 基于Zookeeper实现（利用zk的临时顺序节点来实现）

### 乐观锁和悲观锁

- **乐观锁(Optimistic Lock)**：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性。 乐观锁不能解决脏读的问题。

- **悲观锁(Pessimistic Lock)**：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作。





自动化测试用例各种数据，网络和环境异常的问题

https://tech.meituan.com/2018/01/09/lego-api-test.html



线上问题的处理流程



### 监控的建议和建树

#### 监控对象

- 硬件监控
  - 电源状态、CPU 状态、机器温度、风扇状态、物理磁盘、raid 状态、内存状态、网卡状态

- 服务器基础监控

  - **CPU：**单个 CPU 以及整体的使用情况。
  - **内存：**已用内存、可用内存。
  - **磁盘：**磁盘使用率、磁盘读写的吞吐量。
  - **网络：**出口流量、入口流量、TCP 连接状态。

- 数据库监控

  - 数据库连接数、QPS、TPS、并行处理的会话数、缓存命中率、主从延时、锁状态、慢查询

- 中间件监控

  - **Nginx：**活跃连接数、等待连接数、丢弃连接数、请求量、耗时、5XX 错误率。
  - **Tomcat：**最大线程数、当前线程数、请求量、耗时、错误量、堆内存使用情况、GC 次数和耗时。
  - **缓存：**成功连接数、阻塞连接数、已使用内存、内存碎片率、请求量、耗时、缓存命中率。
  - **消息队列：**连接数、队列数、生产速率、消费速率、消息堆积量。

- 应用监控

  - **HTTP 接口：**URL 存活、请求量、耗时、异常量。
  - **RPC 接口：**请求量、耗时、超时量、拒绝量。
  - **JVM：**GC 次数、GC 耗时、各个内存区域的大小、当前线程数、死锁线程数。
  - **线程池：**活跃线程数、任务队列大小、任务执行耗时、拒绝任务数。
  - **连接池：**总连接数、活跃连接数。
  - **日志监控：**访问日志、错误日志。
  - **业务指标：**视业务来定，比如 PV、订单量等。

  

质量管理的建树



小工具的开发目的



CI 过程的检查点和退出时机



大流量活动容量的预估策略

https://tech.meituan.com/2016/09/28/stress-test-before-promotion.html



## 测试理论

### 黑盒测试方法

等价类划分法；边界值分析法；因果图法；场景法；正交实验设计法；判定表驱动分析法；错误推测法；功能图分析法。

### 设计用例的方法






