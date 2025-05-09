---
layout:     post
title:      2024-11-10-浅谈Zookeeper
subtitle:   2024-11-10-浅谈Zookeeper
date:       2024-11-10 09:43
author:     lily
header-img: img/中间件.jpg
catalog:    true
tags:
    - 中间件
---

[聊聊分布式锁 (qq.com)](https://mp.weixin.qq.com/s/-N4x6EkxwAYDGdJhwvmZLw)---字节跳动谈分布式锁

## ZK实现分布式锁

ZooKeeper 分布式锁是基于 **临时顺序节点** 和 **Watcher（事件监听器）** 实现的。

获取锁：

1. 首先我们要有一个持久节点`/locks`，客户端获取锁就是在`locks`下创建临时顺序节点。
2. 假设客户端 1 创建了`/locks/lock1`节点，创建成功之后，会判断 `lock1`是否是 `/locks` 下最小的子节点。
3. 如果 `lock1`是最小的子节点，则获取锁成功。否则，获取锁失败。
4. 如果获取锁失败，则说明有其他的客户端已经成功获取锁。客户端 1 并不会不停地循环去尝试加锁，而是在前一个节点比如`/locks/lock0`上注册一个事件监听器。这个监听器的作用是当前一个节点释放锁之后通知客户端 1（避免无效自旋），这样客户端 1 就加锁成功了。

释放锁：

1. 成功获取锁的客户端在执行完业务流程之后，会将对应的子节点删除。
2. 成功获取锁的客户端在出现故障之后，对应的子节点由于是临时顺序节点，也会被自动删除，避免了锁无法被释放。
3. 我们前面说的事件监听器其实监听的就是这个子节点删除事件，子节点删除就意味着锁被释放。

![](https://cdn.nlark.com/yuque/0/2024/png/33597641/1730534618523-03958e86-1260-4e9f-a7b8-02ef27d5ebe3.png)

实际项目中，推荐使用 Curator 来实现 ZooKeeper 分布式锁。Curator 是 Netflix 公司开源的一套 ZooKeeper Java 客户端框架，相比于 ZooKeeper 自带的客户端 zookeeper 来说，Curator 的封装更加完善，各种 API 都可以比较方便地使用。

`Curator`主要实现了下面四种锁：

- `InterProcessMutex`：分布式可重入排它锁
- `InterProcessSemaphoreMutex`：分布式不可重入排它锁
- `InterProcessReadWriteLock`：分布式读写锁
- `InterProcessMultiLock`：将多个锁作为单个实体管理的容器，获取锁的时候获取所有锁，释放锁也会释放所有锁资源（忽略释放失败的锁）。

```
CuratorFramework client = ZKUtils.getClient();
client.start();
// 分布式可重入排它锁
InterProcessLock lock1 = new InterProcessMutex(client, lockPath1);
// 分布式不可重入排它锁
InterProcessLock lock2 = new InterProcessSemaphoreMutex(client, lockPath2);
// 将多个锁作为一个整体
InterProcessMultiLock lock = new InterProcessMultiLock(Arrays.asList(lock1, lock2));

if (!lock.acquire(10, TimeUnit.SECONDS)) {
    throw new IllegalStateException("不能获取多锁");
}
System.out.println("已获取多锁");
System.out.println("是否有第一个锁: " + lock1.isAcquiredInThisProcess());
System.out.println("是否有第二个锁: " + lock2.isAcquiredInThisProcess());
try {
    // 资源操作
    resource.use();
} finally {
    System.out.println("释放多个锁");
    lock.release();
}
System.out.println("是否有第一个锁: " + lock1.isAcquiredInThisProcess());
System.out.println("是否有第二个锁: " + lock2.isAcquiredInThisProcess());
client.close();
```

### [为什么要用临时顺序节点？](https://javaguide.cn/distributed-system/distributed-lock-implementations.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A6%81%E7%94%A8%E4%B8%B4%E6%97%B6%E9%A1%BA%E5%BA%8F%E8%8A%82%E7%82%B9)

每个数据节点在 ZooKeeper 中被称为 **znode**，它是 ZooKeeper 中数据的最小单元。

我们通常是将 znode 分为 4 大类：

- **持久（PERSISTENT）节点**：一旦创建就一直存在即使 ZooKeeper 集群宕机，直到将其删除。
- **临时（EPHEMERAL）节点**：临时节点的生命周期是与 **客户端会话（session）** 绑定的，**会话消失则节点消失** 。并且，**临时节点只能做叶子节点** ，不能创建子节点。
- **持久顺序（PERSISTENT_SEQUENTIAL）节点**：除了具有持久（PERSISTENT）节点的特性之外， 子节点的名称还具有顺序性。比如 `/node1/app0000000001`、`/node1/app0000000002` 。
- **临时顺序（EPHEMERAL_SEQUENTIAL）节点**：除了具备临时（EPHEMERAL）节点的特性之外，子节点的名称还具有顺序性。

可以看出，临时节点相比持久节点，最主要的是对会话失效的情况处理不一样，临时节点会话消失则对应的节点消失。这样的话，如果客户端发生异常导致没来得及释放锁也没关系，会话失效节点自动被删除，不会发生死锁的问题。

使用 Redis 实现分布式锁的时候，我们是通过过期时间来避免锁无法被释放导致死锁问题的，而 ZooKeeper 直接利用临时节点的特性即可。

假设不使用顺序节点的话，所有尝试获取锁的客户端都会对持有锁的子节点加监听器。当该锁被释放之后，势必会造成所有尝试获取锁的客户端来争夺锁，这样对性能不友好。使用顺序节点之后，只需要监听前一个节点就好了，对性能更友好。

### [为什么要设置对前一个节点的监听？](https://javaguide.cn/distributed-system/distributed-lock-implementations.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A6%81%E8%AE%BE%E7%BD%AE%E5%AF%B9%E5%89%8D%E4%B8%80%E4%B8%AA%E8%8A%82%E7%82%B9%E7%9A%84%E7%9B%91%E5%90%AC)

Watcher（事件监听器），是 ZooKeeper 中的一个很重要的特性。ZooKeeper 允许用户在指定节点上注册一些 Watcher，并且在一些特定事件触发的时候，ZooKeeper 服务端会将事件通知到感兴趣的客户端上去，该机制是 ZooKeeper 实现分布式协调服务的重要特性。

同一时间段内，可能会有很多客户端同时获取锁，但只有一个可以获取成功。如果获取锁失败，则说明有其他的客户端已经成功获取锁。获取锁失败的客户端并不会不停地循环去尝试加锁，而是在前一个节点注册一个事件监听器。

这个事件监听器的作用是：**当前一个节点对应的客户端释放锁之后（也就是前一个节点被删除之后，监听的是删除事件），通知获取锁失败的客户端（唤醒等待的线程，Java 中的** `**wait/notifyAll**` **），让它尝试去获取锁，然后就成功获取锁了。**

### [如何实现可重入锁？](https://javaguide.cn/distributed-system/distributed-lock-implementations.html#%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0%E5%8F%AF%E9%87%8D%E5%85%A5%E9%94%81-1)

这里以 Curator 的 `InterProcessMutex` 对可重入锁的实现来介绍（源码地址：[InterProcessMutex.java](https://github.com/apache/curator/blob/master/curator-recipes/src/main/java/org/apache/curator/framework/recipes/locks/InterProcessMutex.java)）。

当我们调用 `InterProcessMutex#acquire`方法获取锁的时候，会调用`InterProcessMutex#internalLock`方法。

```
// 获取可重入互斥锁，直到获取成功为止
@Override
public void acquire() throws Exception {
  if (!internalLock(-1, null)) {
    throw new IOException("Lost connection while trying to acquire lock: " + basePath);
  }
}
```

`internalLock` 方法会先获取当前请求锁的线程，然后从 `threadData`( `ConcurrentMap<Thread, LockData>` 类型)中获取当前线程对应的 `lockData` 。 `lockData` 包含锁的信息和加锁的次数，是实现可重入锁的关键。

第一次获取锁的时候，`lockData`为 `null`。获取锁成功之后，会将当前线程和对应的 `lockData` 放到 `threadData` 中

```
private boolean internalLock(long time, TimeUnit unit) throws Exception {
    // 获取当前请求锁的线程
    Thread currentThread = Thread.currentThread();
    // 拿对应的 lockData
    LockData lockData = threadData.get(currentThread);
    // 第一次获取锁的话，lockData 为 null
    if (lockData != null) {
        // 当前线程获取过一次锁之后
        // 因为当前线程的锁存在， lockCount 自增后返回，实现锁重入.
        lockData.lockCount.incrementAndGet();
        return true;
    }
    // 尝试获取锁
    String lockPath = internals.attemptLock(time, unit, getLockNodeBytes());
    if (lockPath != null) {
        LockData newLockData = new LockData(currentThread, lockPath);
        // 获取锁成功之后，将当前线程和对应的 lockData 放到 threadData 中
        threadData.put(currentThread, newLockData);
        return true;
    }

    return false;
}
```

`LockData`是 `InterProcessMutex`中的一个静态内部类。

```
private final ConcurrentMap<Thread, LockData> threadData = Maps.newConcurrentMap();

private static class LockData
{
    // 当前持有锁的线程
    final Thread owningThread;
    // 锁对应的子节点
    final String lockPath;
    // 加锁的次数
    final AtomicInteger lockCount = new AtomicInteger(1);

    private LockData(Thread owningThread, String lockPath)
    {
        this.owningThread = owningThread;
        this.lockPath = lockPath;
    }
}
```

如果已经获取过一次锁，后面再来获取锁的话，直接就会在 `if (lockData != null)` 这里被拦下了，然后就会执行`lockData.lockCount.incrementAndGet();` 将加锁次数加 1。

可重入锁实现逻辑是直接在客户端判断当前线程有没有获取锁，有的话直接将加锁次数加 1 就可以了。

## ZK的概念

### [Data model（数据模型）](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#data-model-%E6%95%B0%E6%8D%AE%E6%A8%A1%E5%9E%8B)

ZooKeeper 数据模型采用层次化的多叉树形结构，每个节点上都可以存储数据，这些数据可以是数字、字符串或者是二进制序列。并且。每个节点还可以拥有 N 个子节点，最上层是根节点以“/”来代表。每个数据节点在 ZooKeeper 中被称为 **znode**，它是 ZooKeeper 中数据的最小单元。并且，每个 znode 都有一个唯一的路径标识。

强调一句：**ZooKeeper 主要是用来协调服务的，而不是用来存储业务数据的，所以不要放比较大的数据在 znode 上，ZooKeeper 给出的每个节点的数据大小上限是 1M 。**

从下图可以更直观地看出：ZooKeeper 节点路径标识方式和 Unix 文件系统路径非常相似，都是由一系列使用斜杠"/"进行分割的路径表示，开发人员可以向这个节点中写入数据，也可以在节点下面创建子节点。这些操作我们后面都会介绍到。

![](https://cdn.nlark.com/yuque/0/2024/png/33597641/1730534816676-53251633-49c9-44f6-8402-f7a3824cff97.png)

ZooKeeper 数据模型

### [znode（数据节点）](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#znode-%E6%95%B0%E6%8D%AE%E8%8A%82%E7%82%B9)

介绍了 ZooKeeper 树形数据模型之后，我们知道每个数据节点在 ZooKeeper 中被称为 **znode**，它是 ZooKeeper 中数据的最小单元。你要存放的数据就放在上面，是你使用 ZooKeeper 过程中经常需要接触到的一个概念。

我们通常是将 znode 分为 4 大类：

- **持久（PERSISTENT）节点**：一旦创建就一直存在即使 ZooKeeper 集群宕机，直到将其删除。
- **临时（EPHEMERAL）节点**：临时节点的生命周期是与 **客户端会话（session）** 绑定的，**会话消失则节点消失**。并且，**临时节点只能做叶子节点** ，不能创建子节点。
- **持久顺序（PERSISTENT_SEQUENTIAL）节点**：除了具有持久（PERSISTENT）节点的特性之外， 子节点的名称还具有顺序性。比如 `/node1/app0000000001`、`/node1/app0000000002` 。
- **临时顺序（EPHEMERAL_SEQUENTIAL）节点**：除了具备临时（EPHEMERAL）节点的特性之外，子节点的名称还具有顺序性

每个 znode 由 2 部分组成:

- **stat**：状态信息
- **data**：节点存放的数据的具体内容

如下所示，我通过 get 命令来获取 根目录下的 dubbo 节点的内容。（get 命令在下面会介绍到）。

```
[zk: 127.0.0.1:2181(CONNECTED) 6] get /dubbo
# 该数据节点关联的数据内容为空
null
# 下面是该数据节点的一些状态信息，其实就是 Stat 对象的格式化输出
cZxid = 0x2
ctime = Tue Nov 27 11:05:34 CST 2018
mZxid = 0x2
mtime = Tue Nov 27 11:05:34 CST 2018
pZxid = 0x3
cversion = 1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 1
```

Stat 类中包含了一个数据节点的所有状态信息的字段，包括事务 ID（cZxid）、节点创建时间（ctime） 和子节点个数（numChildren） 等等。

下面我们来看一下每个 znode 状态信息究竟代表的是什么吧！（下面的内容来源于《从 Paxos 到 ZooKeeper 分布式一致性原理与实践》，因为 Guide 确实也不是特别清楚，要学会参考资料的嘛！ ）：

|   |   |
|---|---|
|znode 状态信息|解释|
|cZxid|create ZXID，即该数据节点被创建时的事务 id|
|ctime|create time，即该节点的创建时间|
|mZxid|modified ZXID，即该节点最终一次更新时的事务 id|
|mtime|modified time，即该节点最后一次的更新时间|
|pZxid|该节点的子节点列表最后一次修改时的事务 id，只有子节点列表变更才会更新 pZxid，子节点内容变更不会更新|
|cversion|子节点版本号，当前节点的子节点每次变化时值增加 1|
|dataVersion|数据节点内容版本号，节点创建时为 0，每更新一次节点内容(不管内容有无变化)该版本号的值增加 1|
|aclVersion|节点的 ACL 版本号，表示该节点 ACL 信息变更次数|
|ephemeralOwner|创建该临时节点的会话的 sessionId；如果当前节点为持久节点，则 ephemeralOwner=0|
|dataLength|数据节点内容长度|
|numChildren|当前节点的子节点个数|

### [版本（version）](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#%E7%89%88%E6%9C%AC-version)

在前面我们已经提到，对应于每个 znode，ZooKeeper 都会为其维护一个叫作 **Stat** 的数据结构，Stat 中记录了这个 znode 的三个相关的版本：

- **dataVersion**：当前 znode 节点的版本号
- **cversion**：当前 znode 子节点的版本
- **aclVersion**：当前 znode 的 ACL 的版本。

### [ACL（权限控制）](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#acl-%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6)

ZooKeeper 采用 ACL（AccessControlLists）策略来进行权限控制，类似于 UNIX 文件系统的权限控制。

对于 znode 操作的权限，ZooKeeper 提供了以下 5 种：

- **CREATE** : 能创建子节点
- **READ**：能获取节点数据和列出其子节点
- **WRITE** : 能设置/更新节点数据
- **DELETE** : 能删除子节点
- **ADMIN** : 能设置节点 ACL 的权限

其中尤其需要注意的是，**CREATE** 和 **DELETE** 这两种权限都是针对 **子节点** 的权限控制。

对于身份认证，提供了以下几种方式：

- **world**：默认方式，所有用户都可无条件访问。
- **auth** :不使用任何 id，代表任何已认证的用户。
- **digest** :用户名:密码认证方式：_username:password_ 。
- **ip** : 对指定 ip 进行限制。

### [Watcher（事件监听器）](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#watcher-%E4%BA%8B%E4%BB%B6%E7%9B%91%E5%90%AC%E5%99%A8)

Watcher（事件监听器），是 ZooKeeper 中的一个很重要的特性。ZooKeeper 允许用户在指定节点上注册一些 Watcher，并且在一些特定事件触发的时候，ZooKeeper 服务端会将事件通知到感兴趣的客户端上去，该机制是 ZooKeeper 实现分布式协调服务的重要特性。

![](https://cdn.nlark.com/yuque/0/2024/png/33597641/1730534816658-170de559-925d-4b7a-ba34-2de0b6f4dcf7.png)

ZooKeeper Watcher 机制

_破音：非常有用的一个特性，都拿出小本本记好了，后面用到 ZooKeeper 基本离不开 Watcher（事件监听器）机制。_

### [会话（Session）](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#%E4%BC%9A%E8%AF%9D-session)

Session 可以看作是 ZooKeeper 服务器与客户端的之间的一个 TCP 长连接，通过这个连接，客户端能够通过心跳检测与服务器保持有效的会话，也能够向 ZooKeeper 服务器发送请求并接受响应，同时还能够通过该连接接收来自服务器的 Watcher 事件通知。

Session 有一个属性叫做：`sessionTimeout` ，`sessionTimeout` 代表会话的超时时间。当由于服务器压力太大、网络故障或是客户端主动断开连接等各种原因导致客户端连接断开时，只要在`sessionTimeout`规定的时间内能够重新连接上集群中任意一台服务器，那么之前创建的会话仍然有效。

另外，在为客户端创建会话之前，服务端首先会为每个客户端都分配一个 `sessionID`。由于 `sessionID`是 ZooKeeper 会话的一个重要标识，许多与会话相关的运行机制都是基于这个 `sessionID` 的，因此，无论是哪台服务器为客户端分配的 `sessionID`，都务必保证全局唯一。

## [ZooKeeper 集群](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#zookeeper-%E9%9B%86%E7%BE%A4)

为了保证高可用，最好是以集群形态来部署 ZooKeeper，这样只要集群中大部分机器是可用的（能够容忍一定的机器故障），那么 ZooKeeper 本身仍然是可用的。通常 3 台服务器就可以构成一个 ZooKeeper 集群了。ZooKeeper 官方提供的架构图就是一个 ZooKeeper 集群整体对外提供服务。

![](https://cdn.nlark.com/yuque/0/2024/png/33597641/1730534883970-824d29a9-c748-402c-9873-f9e90a2628a3.png)

ZooKeeper 集群架构

上图中每一个 Server 代表一个安装 ZooKeeper 服务的服务器。组成 ZooKeeper 服务的服务器都会在内存中维护当前的服务器状态，并且每台服务器之间都互相保持着通信。集群间通过 ZAB 协议（ZooKeeper Atomic Broadcast）来保持数据的一致性。

**最典型集群模式：Master/Slave 模式（主备模式）**。在这种模式中，通常 Master 服务器作为主服务器提供写服务，其他的 Slave 服务器从服务器通过异步复制的方式获取 Master 服务器最新的数据提供读服务。

### [ZooKeeper 集群角色](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#zookeeper-%E9%9B%86%E7%BE%A4%E8%A7%92%E8%89%B2)

但是，在 ZooKeeper 中没有选择传统的 Master/Slave 概念，而是引入了 Leader、Follower 和 Observer 三种角色。如下图所示

![](https://cdn.nlark.com/yuque/0/2024/png/33597641/1730534883944-85ba227f-a2e8-44c5-8c06-8ac86aa8113f.png)

ZooKeeper 集群中角色

ZooKeeper 集群中的所有机器通过一个 **Leader 选举过程** 来选定一台称为 “**Leader**” 的机器，Leader 既可以为客户端提供写服务又能提供读服务。除了 Leader 外，**Follower** 和 **Observer** 都只能提供读服务。Follower 和 Observer 唯一的区别在于 Observer 机器不参与 Leader 的选举过程，也不参与写操作的“过半写成功”策略，因此 Observer 机器可以在不影响写性能的情况下提升集群的读性能。

|   |   |
|---|---|
|角色|说明|
|Leader|为客户端提供读和写的服务，负责投票的发起和决议，更新系统状态。|
|Follower|为客户端提供读服务，如果是写服务则转发给 Leader。参与选举过程中的投票。|
|Observer|为客户端提供读服务，如果是写服务则转发给 Leader。不参与选举过程中的投票，也不参与“过半写成功”策略。在不影响写性能的情况下提升集群的读性能。此角色于 ZooKeeper3.3 系列新增的角色。|

### [ZooKeeper 集群 Leader 选举过程](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#zookeeper-%E9%9B%86%E7%BE%A4-leader-%E9%80%89%E4%B8%BE%E8%BF%87%E7%A8%8B)

当 Leader 服务器出现网络中断、崩溃退出与重启等异常情况时，就会进入 Leader 选举过程，这个过程会选举产生新的 Leader 服务器。

这个过程大致是这样的：

1. **Leader election（选举阶段）**：节点在一开始都处于选举阶段，只要有一个节点得到超半数节点的票数，它就可以当选准 leader。
2. **Discovery（发现阶段）**：在这个阶段，followers 跟准 leader 进行通信，同步 followers 最近接收的事务提议。
3. **Synchronization（同步阶段）**：同步阶段主要是利用 leader 前一阶段获得的最新提议历史，同步集群中所有的副本。同步完成之后准 leader 才会成为真正的 leader。
4. **Broadcast（广播阶段）**：到了这个阶段，ZooKeeper 集群才能正式对外提供事务服务，并且 leader 可以进行消息广播。同时如果有新的节点加入，还需要对新节点进行同步。

ZooKeeper 集群中的服务器状态有下面几种：

- **LOOKING**：寻找 Leader。
- **LEADING**：Leader 状态，对应的节点为 Leader。
- **FOLLOWING**：Follower 状态，对应的节点为 Follower。
- **OBSERVING**：Observer 状态，对应节点为 Observer，该节点不参与 Leader 选举。

### [ZooKeeper 集群为啥最好奇数台？](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#zookeeper-%E9%9B%86%E7%BE%A4%E4%B8%BA%E5%95%A5%E6%9C%80%E5%A5%BD%E5%A5%87%E6%95%B0%E5%8F%B0)

ZooKeeper 集群在宕掉几个 ZooKeeper 服务器之后，如果剩下的 ZooKeeper 服务器个数大于宕掉的个数的话整个 ZooKeeper 才依然可用。假如我们的集群中有 n 台 ZooKeeper 服务器，那么也就是剩下的服务数必须大于 n/2。先说一下结论，2n 和 2n-1 的容忍度是一样的，都是 n-1，大家可以先自己仔细想一想，这应该是一个很简单的数学问题了。

比如假如我们有 3 台，那么最大允许宕掉 1 台 ZooKeeper 服务器，如果我们有 4 台的的时候也同样只允许宕掉 1 台。  
假如我们有 5 台，那么最大允许宕掉 2 台 ZooKeeper 服务器，如果我们有 6 台的的时候也同样只允许宕掉 2 台。

综上，何必增加那一个不必要的 ZooKeeper 呢？

### [ZooKeeper 选举的过半机制防止脑裂](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#zookeeper-%E9%80%89%E4%B8%BE%E7%9A%84%E8%BF%87%E5%8D%8A%E6%9C%BA%E5%88%B6%E9%98%B2%E6%AD%A2%E8%84%91%E8%A3%82)

**何为集群脑裂？**

对于一个集群，通常多台机器会部署在不同机房，来提高这个集群的可用性。保证可用性的同时，会发生一种机房间网络线路故障，导致机房间网络不通，而集群被割裂成几个小集群。这时候子集群各自选主导致“脑裂”的情况。

举例说明：比如现在有一个由 6 台服务器所组成的一个集群，部署在了 2 个机房，每个机房 3 台。正常情况下只有 1 个 leader，但是当两个机房中间网络断开的时候，每个机房的 3 台服务器都会认为另一个机房的 3 台服务器下线，而选出自己的 leader 并对外提供服务。若没有过半机制，当网络恢复的时候会发现有 2 个 leader。仿佛是 1 个大脑（leader）分散成了 2 个大脑，这就发生了脑裂现象。脑裂期间 2 个大脑都可能对外提供了服务，这将会带来数据一致性等问题。

**过半机制是如何防止脑裂现象产生的？**

ZooKeeper 的过半机制导致不可能产生 2 个 leader，因为少于等于一半是不可能产生 leader 的，这就使得不论机房的机器如何分配都不可能发生脑裂。

## [ZAB 协议和 Paxos 算法](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#zab-%E5%8D%8F%E8%AE%AE%E5%92%8C-paxos-%E7%AE%97%E6%B3%95)

Paxos 算法应该可以说是 ZooKeeper 的灵魂了。但是，ZooKeeper 并没有完全采用 Paxos 算法 ，而是使用 ZAB 协议作为其保证数据一致性的核心算法。另外，在 ZooKeeper 的官方文档中也指出，ZAB 协议并不像 Paxos 算法那样，是一种通用的分布式一致性算法，它是一种特别为 Zookeeper 设计的崩溃可恢复的原子消息广播算法。

### [ZAB 协议介绍](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#zab-%E5%8D%8F%E8%AE%AE%E4%BB%8B%E7%BB%8D)

ZAB（ZooKeeper Atomic Broadcast，原子广播） 协议是为分布式协调服务 ZooKeeper 专门设计的一种支持崩溃恢复的原子广播协议。 在 ZooKeeper 中，主要依赖 ZAB 协议来实现分布式数据一致性，基于该协议，ZooKeeper 实现了一种主备模式的系统架构来保持集群中各个副本之间的数据一致性。

### [ZAB 协议两种基本的模式：崩溃恢复和消息广播](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html#zab-%E5%8D%8F%E8%AE%AE%E4%B8%A4%E7%A7%8D%E5%9F%BA%E6%9C%AC%E7%9A%84%E6%A8%A1%E5%BC%8F-%E5%B4%A9%E6%BA%83%E6%81%A2%E5%A4%8D%E5%92%8C%E6%B6%88%E6%81%AF%E5%B9%BF%E6%92%AD)

ZAB 协议包括两种基本的模式，分别是

- **崩溃恢复**：当整个服务框架在启动过程中，或是当 Leader 服务器出现网络中断、崩溃退出与重启等异常情况时，ZAB 协议就会进入恢复模式并选举产生新的 Leader 服务器。当选举产生了新的 Leader 服务器，同时集群中已经有过半的机器与该 Leader 服务器完成了状态同步之后，ZAB 协议就会退出恢复模式。其中，**所谓的状态同步是指数据同步，用来保证集群中存在过半的机器能够和 Leader 服务器的数据状态保持一致**。
- **消息广播**：**当集群中已经有过半的 Follower 服务器完成了和 Leader 服务器的状态同步，那么整个服务框架就可以进入消息广播模式了。** 当一台同样遵守 ZAB 协议的服务器启动后加入到集群中时，如果此时集群中已经存在一个 Leader 服务器在负责进行消息广播，那么新加入的服务器就会自觉地进入数据恢复模式：找到 Leader 所在的服务器，并与其进行数据同步，然后一起参与到消息广播流程中去。