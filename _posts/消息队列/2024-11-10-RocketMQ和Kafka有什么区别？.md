---
layout:     post
title:      2024-11-10-RocketMQ和Kafka有什么区别？
subtitle:   2024-11-10-RocketMQ和Kafka有什么区别？
date:       2024-11-10 09:59
author:     lily
header-img: img/消息队列.jpg
catalog:    true
tags:
    - 消息队列
---

作为一个程序员，假设你有 A、B 两个服务，A 服务发出消息后，不想让 B 服务**立马**处理到。而是要**过半小时**才让 B 服务处理到，该怎么实现？

这类延迟处理消息的场景非常常见，举个例子，比如我每天早上到公司后都会点个外卖，我希望外卖能在中午送过来，而不是立马送过来，这就需要将外卖消息经过延时后，再投递到商家侧。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096923593-72edcc39-dbc5-4f36-a9b4-783e308cc469.webp)

那么问题就来了，有没有优雅的解决方案？当然有，**没有什么是加一层中间层不能解决的，如果有，那就再加一层**。这次我们要加的中间层是消息队列 **RocketMQ**。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096923572-4406cd62-cfef-4f14-8897-d720736ff5dd.webp)

## RocketMQ 是什么？

RocketMQ 是阿里自研的国产**消息队列**，目前已经是 Apache 的顶级项目。和其他消息队列一样，它接受来自**生产者**的消息，将消息分类，每一类是一个 **topic**，**消费者**根据需要订阅 topic，获取里面的消息。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096923641-562b00bf-14bf-4910-9b33-f02f8563748d.webp)

是不是很像我们上篇文章里提到的消息队 Kafka，那么问题很自然就来了，**既然都是消息队列，那它们之间有什么区别呢**？

## RocketMQ 和 Kafka 的区别

RocketMQ 的架构其实参考了 Kafka 的设计思想，同时又在 Kafka 的基础上做了一些调整。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096923568-0a15c003-6947-4e16-9f65-989e646a8f62.webp)

这些调整，用一句话总结就是，"**和 Kafka 相比，RocketMQ 在架构上做了减法，在功能上做了加法**"。我们来看下这句话的含义。

## 在架构上做减法

我们来简单回顾下消息队列 Kafka 的架构。kakfa 也是通过多个 `topic` 对消息进行分类。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096923574-a22b09a6-26b8-47d7-b934-f3d501b44b45.webp)

- 为了提升单个 topic 的并发**性能**，将**单个 topic** 拆为多个 `partition`。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924070-602e2fd8-2491-46bd-b0de-95a019d87825.webp)

- 为了提升系统**扩展性**，将多个 partition 分别部署在不同 `broker` 上。
- 为了提升系统的**可用性**，为 partition 加了多个副本。
- 为了协调和管理 Kafka 集群的数据信息，引入`Zookeeper`作为协调节点。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924094-119aab07-f0e9-41c5-8d33-cc3a189822dc.webp)

Kafka 已经是非常强的消息队列了，我们来看下 RocketMQ 在 Kafka 架构的基础上，还能玩出什么花样来。

### 简化协调节点

`Zookeeper` 在 Kafka 架构中会和 broker 通信，维护 Kafka 集群信息。**一个新的 broker 连上 Zookeeper 后**，其他 broker 就能立马感知到它的加入，像这种能在分布式环境下，让多个实例同时获取到同一份信息的服务，就是所谓的**分布式协调服务**。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924058-89c02cc6-c396-4639-a953-599d4762b45f.webp)

但 Zookeeper 作为一个**通用的**分布式协调服务，它不仅可以用于**服务注册与发现**，还可以用于分布式锁、**配置管理**等场景。Kafka 其实只用到了它的部分功能，多少有点**杀鸡用牛刀**的味道。**太重了**。

所以 RocketMQ 直接将 Zookeeper 去掉，换成了 **nameserver**，用一种更轻量的方式，管理消息队列的集群信息。生产者通过 nameserver 获取到 topic 和 broker 的路由信息，然后再与 broker 通信，实现**服务发现**和**负载均衡**的效果。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924126-16c53341-38fe-4791-8c78-09e9573391bb.webp)

当然，开发 Kafka 的大佬们后来也意识到了 Zookeeper 过重的问题，所以从 2.8.0 版本就支持将 Zookeeper 移除，通过 **在 broker 之间加入一致性算法 raft 实现同样的效果**，这就是所谓的 **KRaft** 或 **Quorum** 模式。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924063-4e06f660-9ba3-4bbe-bf37-aa7ef056fbd9.webp)

### 简化分区

我们知道，Kafka 会将 topic 拆分为多个 partition，用来提升**并发性能**。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924404-d6930ad9-c389-425d-9c6e-35f351c63c76.webp)

在 RocketMQ 里也一样，**将 topic 拆分成了多个分区**，但换了个名字，叫 **Queue**,也就是"**队列**"。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924460-d8ea64eb-6fb2-4feb-b080-3ed7bcf59d17.webp)

Kafka 中的 partition 会存储**完整**的消息体，而 RocketMQ 的 Queue 上却只存一些**简要**信息，比如**消息偏移 offset**，而消息的完整数据则放到"一个"叫 `commitlog` 的文件上，通过 offset 我们可以定位到 commitlog 上的某条消息。

Kafka 消费消息，broker 只需要直接从 partition 读取消息返回就好，也就是读第**一次**就够了。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924478-0c05aae8-87a2-45ea-8bb5-17101dfe869c.webp)

而在 RocketMQ 中，broker 则需要先从 Queue 上读取到 offset 的值，再跑到 **commitlog 上将完整数据读出来**，也就是需要读**两次**。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924505-19015c74-f29d-48b8-af21-9c5526fccc7e.webp)

那么问题就来了，看起来 Kafka 的设计更高效？为什么 RocketMQ 不采用 Kafka 的设计？这就不得说一下 Kafka 的**底层存储**了。

### Kafka 的底层存储

Kafka 的 **partition 分区，其实在底层由很多****段****（****segment****）组成**，每个 segment 可以认为就是个**小文件**。将消息数据写入到 partition 分区，本质上就是将数据写入到某个 segment 文件下。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924513-99ee7966-0a19-4b6b-801b-dfe5d95059b4.webp)

我们知道，操作系统的机械磁盘，**顺序写**的性能会比**随机写**快很多，差距高达几十倍。为了提升性能，**Kafka 对每个小文件都是顺序写**。如果只有**一个** segment 文件，那写文件的性能会很好。

但当 topic 变多之后，topic 底下的 partition 分区也会变多，对应的 partition 底下的 segment 文件也会变多。同时写**多个** topic 底下的 partition，就是同时**写多个文件**，虽然每个文件内部都是顺序写，但多个**文件存放在磁盘的不同地方**，原本**顺序写磁盘就可能劣化变成了随机写**。于是写性能就降低了。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924864-f9314ed5-3237-405d-a0a5-be4797524479.webp)

那问题又又来了，究竟多少 topic 才算多？这个看实际情况，但打太极从来不是我的风格。我给一个经验值**仅供参考**，**8 个分区的情况下，超过 64 topic, Kafka 性能就会开始下降**。

### RocketMQ 的底层存储

为了缓解同时写多个文件带来的随机写问题，RocketMQ 索性将单个 broker 底下的多个 topic 数据，全都写到"**一个**"逻辑文件 `CommitLog` 上，这就消除了**随机写多文件**的问题，将所有写操作都变成了顺序写。大大提升了 RocketMQ 在多 topic 场景下的写性能。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924857-216d9a6e-601e-4afe-acf1-ec9a5c093e28.webp)

注意上面提到的"**一个**"是带引号的，虽然逻辑上它是一个大文件，但实际上这个 CommitLog 由多个小文件组成。每个文件的大小是固定的，当一个文件被写满后，会创建一个新的文件来继续存储新的消息。这种方式可以方便地管理和清理旧的消息。

### 简化备份模型

我们知道，Kafka 会将 partiton 分散到多个 broker 中，**并为 partiton 配置副本**，将 partiton 分为 `leader`和 `follower`，也就是**主和从**。**broker 中既可能有 A topic 的主 partiton，也可能有 B topic 的从 partiton**。主从 partiton 之间会建立数据同步，本质上就是同步 partiton 底下的 segment 文件数据

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924893-830868bf-636d-4671-b8f2-c68cc36ad8d1.webp)

RocketMQ 将 broker 上的所有 topic 数据到写到 CommitLog 上。如果还像 Kafka 那样给每个分区单独建立同步通信，就还得将 CommitLog 里的内容**拆开**，这就还是退化为**随机读**了。于是 RocketMQ 索性**以 broker 为单位区分主从**，**主从之间同步 CommitLog 文件**，保持高可用的同时，也大大简化了备份模型。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924875-fe9cb412-97c0-4a9b-9668-0051857d552c.webp)

好了，到这里，我们熟悉的 Kafka 架构，就成了 RocketMQ 的架构。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096924887-aa6333e9-1498-4d38-b7f1-6ab7af79f040.webp)

是不是跟 Kafka 的很像但又简化了不少？

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096925251-00ed9002-545c-482c-b7d0-497a62926f5c.webp)

## 在功能上做加法

虽然 RocketMQ 的架构比 Kafka 的简单，但功能却比 Kafka 要更丰富，我们来看下。

### 消息过滤

我们知道，Kafka 支持通过 topic 将数据进行分类，比如订单数据和用户数据是两个不同的 topic，但如果我还想**再进一步分类**呢？比如同样是用户数据，还能根据 vip 等级进一步分类。假设我们只需要获取 vip6 的用户数据，在 Kafka 里，消费者需要消费 topic 为用户数据的**所有消息**，再将 vip6 的用户过滤出来。

而 RocketMQ 支持对消息打上**标记**，也就是打 **tag**，消费者能**根据 tag 过滤所需要的数据**。比如我们可以在部分消息上标记 tag=vip6，这样消费者就能**只获取**这部分数据，**省下了消费者过滤数据时的资源消耗**。

相当于 RocketMQ 除了支持通过 topic 进行一级分类，还支持通过 tag 进行二级分类。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096925335-f28c8123-a05a-4587-9d19-65558a0503be.webp)

### 支持事务

我们知道 Kafka 支持事务，比如**生产者发三条消息 ABC，这三条消息要么同时发送成功，要么同时发送失败。**

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096925313-90d06d43-ddeb-49ef-a4e5-10c861af6a99.webp)

是，这确实也叫事务，但**跟我们要的不太一样**。

写业务代码的时候，我们更想要的事务是，"**执行一些自定义逻辑**"和"**生产者发消息**"这两件事，要么同时成功，要么同时失败。

而这正是 RocketMQ 支持的事务能力。

![](https://cdn.nlark.com/yuque/0/2024/webp/33597641/1730096925335-af513e63-9b0f-4016-be8d-beb14d8f7161.webp)

### 加入延时队列

如果我们希望消息投递出去之后，消费者不能立马消费到，而是过个一定时间后才消费，也就是所谓的**延时消息**，就像文章开头的定时外卖那样。如果我们使用 Kafka， 要实现类似的功能的话，就会很费劲。但 RocketMQ 天然支持**延时队列**，我们可以很方便实现这一功能。

### 加入死信队列

消费消息是有可能失败的，失败后一般可以设置**重试**。如果多次重试失败，RocketMQ 会将消息放到一个专门的队列，方便我们**后面单独处理**。这种专门存放失败消息的队列，就是**死信队列**。**Kafka 原生不支持这个功能，需要我们自己实现。**

### 消息回溯

Kafka 支持通过**调整 offset** 来让消费者从某个地方开始消费，而 RocketMQ，除了可以调整 offset, 还支持**调整时间**（kafka在0.10.1后支持调时间）

所以**不那么严谨**的说， **RocketMQ 本质就是在架构上做了减法，在功能上做了加法的 Kafka**。这个总结是不是特别精辟。现在大家通了吗？

最后遗留一个问题。现在看起来，RocketMQ 好像各方面都比 Kafka 更能打。但 Kafka 却一直没被淘汰，说明 RocketMQ 必然是有着不如 Kafka 的地方。是啥呢？**性能**，严格来说是**吞吐量**。

这就很奇怪了，**为什么 RocketMQ 参考了 Kafka 的架构，性能却还不如 Kafka**？这个问题，我们下期聊聊。

## 总结

- RocketMQ 和 Kafka 相比，在架构上做了减法，在功能上做了加法
- 跟 Kafka 的架构相比，RocketMQ 简化了协调节点和分区以及备份模型。同时增强了消息过滤、消息回溯和事务能力，加入了延迟队列，死信队列等新特性。