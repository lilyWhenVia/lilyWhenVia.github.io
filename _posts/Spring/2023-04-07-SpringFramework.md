---
    layout:     post
    title:      SpringFramework
    date:       2023-04-07
    author:     lily
    header-img: img/Spring.jpg
    catalog: 	 true
    tags:       Spring
---

# 基础概念
### 核心容器
ioc容器：
#### 核心概念

- 控制反转
- 依赖注入
##### IOC：控制反转
##### DI：依赖注入
目的：充分解耦

1. 依赖注入方式
> 为什么不推荐使用Field注入
> 从上面的示例中我们可以发现Field注入非常简短，简洁，代码易于阅读。类可以只关注重要的内容，而不受样板代码的影响。不需要构造函数或setter提供来依赖，只需要在字段上面加上@Autowired注解就行了。Java是非常冗长的，所以任何缩短代码的机会都是受欢迎的。但是：
> 
> 违反单一责任原则
> 添加新的依赖项非常容易。添加6个、10个甚至12个依赖项没有问题。当使用构造函数注入时，在某一点之后，构造函数参数的数量会变得过高，并且很明显会出现问题。依赖太多通常意味着类有太多的责任。这可能违反了单一职责原则和关注点分离，这表明类需要进一步的检查和重构。当直接注入字段时，没有这样的警告，因为这种方法可以无限扩展。
> 
> 依赖隐藏
> 使用依赖注入容器意味着类不再负责管理自己的依赖项。获取依赖项的职责是从类中提取的。由其他人现在负责提供依赖项——依赖注入容器或在测试中手动分配它们。当类不再负责获取其依赖项时，它应该使用公共接口(方法或构造函数)清楚地与它们通信。这样就可以清楚类需要什么，以及它是可选的(setter)还是强制的(构造函数)。
> 
> 依赖注入容器耦合
> DI框架的核心思想之一是托管类不应该依赖于所使用的DI容器。换句话说，它应该只是一个普通的POJO，可以独立地实例化它，前提是将所有必需的依赖项传递给它。通过这种方式，您可以在单元测试中实例化它，而不需要启动DI容器，并单独测试它(使用的容器更像是集成测试)。如果没有容器耦合，则可以将该类作为托管或非托管类使用，甚至可以切换到新的DI框架。
> 
> 然而，当直接注入字段时，您无法直接用所有需要的依赖项实例化类。这意味着:
> 1）有一种方法(通过调用默认构造函数)可以在一个状态中使用new来创建一个对象，该状态中缺少一些强制协作者，使用将导致NullPointerException。
> 2）这样的类不能在DI容器(测试、其他模块)之外重用，因为除了反射之外，没有其他方法为它提供所需的依赖项。
> 
> 不变性
> 与构造函数不同，Field注入不能用于将依赖项分配给最终字段。

#### 容器管理操作
:::success

1. 通过spring配置文件 applicationContext.xml管理容器内的bean对象及其依赖关系
2. spring提供了Ioc容器
:::
#### bean的基础配置

1. 通过bean标签导入bean对象
2. 格式
3. 属性
   1. `id`bean对象注册时对应的唯一标识（getBean），且setter要与之同名才可注入成功
   2. `class`bean对象注册类型
   3. `name`可有多个，在容器管理时，ref可引用name或id
   4. `scop`变量范围：单例/多例
4. 子标签
   1. `property`依赖配置
   2. 属性`ref`
5. **spring自动装配**
   1. `<bean id="brandService" class="com.catGod.service.impl.BrandServiceImpl" autowire="byType"/>`
   2. `autowire`属性byType，意为按brandService中所需要的bean类型**自动装配**到所需对象中
#### bean实例化

1. 空参构造方法
   1. 无setter方法, 在构造方法中提供bean对象
   2. 配置`<constructor-arg name="bookDao" ref="book1"/>`,其中name与属性名相同,耦合度较高
2. 静态工厂
```java
配置文件
<!--    静态工厂-->
    <bean id="orderDao" class="com.catGod.Factory.staticFactoryBean" factory-method="getOrderDao"/>

    
    //        原始方式
orderDao orderDao1 = staticFactoryBean.getOrderDao();
System.out.println(orderDao1);

//        spring方式
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
orderDao orderDao = (orderDao) ctx.getBean("orderDao");
System.out.println(orderDao);
```

3. 实例工厂
```java
<!--    实例工厂-->
    <bean id="InstanceFactory" class="com.catGod.Factory.InstanceFactory" />
<!--    orderDao-->
    <bean id="orderDao2" factory-bean="InstanceFactory" factory-method="getOrderDao"/>

```
#### bean的生命周期

1. 初始化容器
   1. 创建对象（内存分配）
   2. 执行构造方法
   3. 执行属性注入（setter）
   4. **执行bean初始化方法（init）**
2. 使用bean
3. 关闭销毁容器
##### 生命周期控制

1. 方法控制
- 通过自定初始化和销毁方法管理bean `init-method="init" destroy-method="destroy"`
- 通过实现接口（了解）
2. 关闭容器
- `ClassPathXmlApplicationContext`
   - `close()`
   - `registerShutdownHook()`
#### 数据源对象（容器中）的管理

1. 把数据源对象当作bean对象管理
2. maven导入原坐标
3. spring管理
#### 加载properties文件
### 事务管理
