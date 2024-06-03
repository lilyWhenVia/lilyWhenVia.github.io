---
layout:     post
title:      springboot基础
subtitle:   springboot
date:       2023-05-15
author:     lily
header-img: img/springBoot.jpg
catalog:    true
tags:
    - springBoot
---
# 基础配置

在Spring中，@Repository、@Service和@Controller等注解都是@Component注解的拓展。@Repository注解用于标识数据访问对象（DAO），@Service注解用于标识业务逻辑对象，@Controller注解用于标识控制器对象。

而MyBatis中，@Mapper注解是用来标识Mapper接口的。在Spring中，为了将Mapper注入到容器中，我们可以使用@Component或@Repository注解来标识Mapper，也可以使用@MapperScan注解来指定要扫描的Mapper接口包。

因此，@Mapper注解的类可以视为一种组件，也可称之为DAO组件，用来操作数据库，而@Component注解则更为通用，可以标识任何类型的组件。

``@MapperScan``

1.首先了解@Mapper
在持久层的接口上添加@Mapper注解,编译后会生成相应的接口实现类，但由于要在每个接口上都进行配置，所以产生了``@MapperScan``。
2.``@MapperScan``
指定要编译成接口实现类的包路径，在编译完成后这个包下的所有接口都会生成相应的接口实现类。
``@ComponentScan``

1.会自动扫描包路径下的``@Controller、@Service、@Repository、@Component``类，符合扫描规则的类会装配到spring容器中。
2.``@MapperScan``和``@ComponentScan``可以同时使用
如果@MapperScan和@ComponentScan扫描的是同一路径会产生错误

# SpringBoot可以同时处理多少请求
> 摘录自https://juejin.cn/post/7203648441721126972

SpringBoot默认的内嵌容器是Tomcat，也就是我们的程序实际上是运行在Tomcat里的。所以与其说SpringBoot可以处理多少请求，到不如说Tomcat可以处理多少请求。
关于Tomcat的默认配置，都在spring-configuration-metadata.json文件中，对应的配置类则是org.springframework.boot.autoconfigure.web.ServerProperties。

和处理请求数量相关的参数有四个：

*  **server.tomcat.threads.min-spare**：参照核心线程数，启动后这些线程便创建完成，不会一个个创建，这些线程是主要的运行线程，对于核心线程需要根据电脑配置和业务类型来确定数量。
*  **server.tomcat.threads.max**：参照最大线程数，这里有一点不同，tomacat7使用的是无界队列，所以不会像线程池那样等待队列满启动额外线程，这里会直接启动线程处理，等到达最大线程书后再回放队列。
*  **server.tomcat.max-connections**：最大连接数。顾名思义，就是最大支持的连接量，其实这里就是最大支持的连接数，至于是不是和accept-count相加需要看客户端和服务端的keep-alive配置，如果服务端不能及时处理请求，进入accept queue的半连接同样会超时，这个时候这些请求算不算在最大请求总量里面，有待考究。tomcat接收socket会先进入accept queue，就是下面的参数，当达到了最大的max-connections的数量时（这里使用信号量），线程池队列将不再接收，相当于无界变成了有界，剩下来的请求会被阻塞在accept queue中。
*  **server.tomcat.accept-count**：等待队列的长度，默认大小是100。
![boot](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2117b6bdb31d4de3b6c912ed31616f9b~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

如果把Tomcat比作一家饭店的话，那么一个请求其实就相当于一位客人。min-spare就是厨师(长期工)；max是厨师总数(长期工+临时工)；max-connections就是饭店里的座位数量；accept-count是门口小板凳的数量。来的客人优先坐到饭店里面，然后厨师开始忙活，如果长期工可以干的完，就让长期工干，如果长期工干不完，就再让临时工干。图中画的厨师一共15人，饭店里有30个座位，也就是说，如果现在来了20个客人，那么就会有5个人先在饭店里等着。如果现在来了35个人，饭店里坐不下，就会让5个人先到门口坐一下。如果来了50个人，那么饭店座位+门口小板凳一共40个，所以就会有10人离开。
也就是说，SpringBoot同时所能处理的最大请求数量是
``max-connections+accept-count``
超过该数量的请求直接就会被丢掉。


## 测试
```yml
 server:
   tomcat:
     threads:
       # 最少线程数
       min-spare: 10
       # 最多线程数
       max: 15
     # 最大连接数
     max-connections: 30
     # 最大等待数
     accept-count: 10
```
使用apifox模拟100个请求访问

# 包扫描配置
