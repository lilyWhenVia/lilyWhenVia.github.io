---
    layout:     post
    title:      SpringFramework
    date:       2023-04-07
    author:     lily
    header-img: img/Spring.jpg
    catalog: 	 true
    tags:       Spring
---

# Spring简介
## Spring是什么
**是一个轻量级的控制反转（IoC）和面向切面（AOP）的容器框架。**
## Spring的优点
* **方便解耦，简化开发**，通过Spring提供的IoC容器，我们可以将对象之间的依赖关系交由Spring进行控制，避免硬编码造成的程序耦合度高。
* **AOP编程的支持**，通过Spring提供的AOP功能，方便进行面向切面编程。
* **声明式事务的支持**，在Spring中，我们可以从单调烦闷的事务管理代码中解脱出来，通过声明式方式灵活地进行事务的管理，提高开发效率和质量。
* **方便程序的测试**，可以用非容器依赖的编程方式进行几乎所有的测试工作。
* **方便集成各种优秀框架**，Spring提供了对各种优秀框架的直接支持。
### 核心概念
- 控制反转
- 依赖注入
### IOC（控制反转）&DI（依赖注入）是什么
简单的说，就是我们类里需要另一个类，只需要让Spring帮我们创建 ，这叫做控制反转；然后Spring帮我们将需要的对象设置到我们的类中，这叫做依赖注入。
###依赖注入方式
* 使用有参构造方法注入
  ```java
   public class A {
         private B b;
         public A(B b) {
            this.b = b;
         }
   }
   ```
* 使用属性(Setter)注入
  ```java
   public class A {
         private B b;
         public void setB(B b) {
            this.b = b;
         }
   }
   ```
* 使用接口注入
  ```java
   // 将调用类所有依赖注入的方法抽取到接口中，调用类通过实现该接口提供相应的注入方法。 
   public interface Dao{
       public void delete(String name);
   } 

   public class DapIml implements Dao{
       private String name;
       public void delete(String name){
           this.name=name;
       }
   }
   ```
> 再idea中使用@autowired注解时会有warning警示
> 为什么不推荐使用Field注入
> 从上面的示例中我们可以发现Field注入非常简短，简洁，代码易于阅读。类可以只关注重要的内容，而不受样板代码的影响。不需要构造函数（构造器）或setter提供来依赖，只需要在字段上面加上@Autowired注解就行了。Java是非常冗长的，所以任何缩短代码的机会都是受欢迎的。
> 但是： Field注入是不推荐的，因为它会破坏类的不变性，使得类变得不可测试。如果一个类依赖于Field注入，那么它就不可能在没有Spring的情况下被实例化。这就是为什么推荐使用构造函数注入或者setter注入。
> 
> 违反单一责任原则
> 依赖隐藏

### 通过容器管理注入
* Spring就是一个管理类实例化并注入的容器。
* 通过配置文件或注解描述类和类之间的依赖关系，自动完成类的初始化和依赖注入的工作。

1. spring配置文件：``applicationContext.xml``管理容器内的bean对象及其依赖关系
2. spring提供了Ioc容器的配置文件实现类：``ClassPathXmlApplicationContext``
#### Spring的IOC容器讲解
#### 1. BeanFactory
   > BeanFactory是一个类工厂，和传统的类工厂不同，传统的类工厂仅负责构造一个类或几个类的实例

   >而BeanFactory可以创建并管理各种类的对象，Spring称这些被创建和管理的Java对象为Bean。BeanFactory是Spring框架的基础，所有的Spring框架都是建立在BeanFactory之上的。
   1. 顶层接口，定义了IOC容器的基本功能
   2. 通过`getBean()`方法获取bean对象
   ***
#### 2. ApplicationContext
> ApplicationContext由BeanFactory派生而来，提供了更多面向实际应用的功能。
> 
>``ApplicationContext``的主要实现类是```ClassPathXmlApplicationContext```和 ``FileSystemXmlApplicationContext``，前者默认从类路径加载配置文件，后者默认从文件系统中加载配置文件
***
#### 3. WebApplicationContext
> WebApplicationContext是一个接口，它继承了ApplicationContext接口，它是一个Web应用的ApplicationContext，它可以读取Web应用下的配置文件。
1. 它是专门为web应用准备的，它可以读取web应用下的配置文件。
2. 它允许以相对于Web根目录的路径中加载配置文件完成初始化工作。从``WebApplicationContext``中可以获取``ServletContext``的引用，**整个WebApplicationContext对象作为属性放置到ServletContext中,以便Web应用环境中可以访问Spring应用上下文。**
   
3. ConfigurableWebApplicationContext扩展了WebApplicationContext,允许通过配置方式实例化WebApplicationContext，定义了两个重要方法。
   * **setServletContext(ServletContext servletcontext)**：为Spring设置ServletContext
   * **setConfigLocation(String[] configLocations)**：设置Spring配置文件地址。
***
#### 4. AnnotationConfigApplicationContext
> AnnotationConfigApplicationContext是一个**注解驱动**的应用上下文，它可以从一个或多个基于Java的配置类中加载Spring的bean定义，而无需XML配置。
1. 通过注解驱动的方式来实现Spring的配置
2. 继承于``GenericApplicationContext``，实现了``ConfigurableApplicationContext``接口
***
### BeanFactory、ApplicationContext和WebApplicationContext的联系与区别
* Spring通过一个配置文件描述Bean与Bean之间的依赖关系，通过Java语言的反射技术能实例化Bean并建立Bean之间的依赖关系。Spring的IoC容器在完成这些底层工作的基础上，还提供了bean实例缓存、生命周期管理、事件发布，资源装载等高级服务。

* BeanFactory是Spring最核心的接口，提供了高级IoC的配置机制。ApplicationContext建立在BeanFactory的基础上，是BeanFactory的子接口，提供了更多面向应用的功能。我们一般称BeanFactory为IoC容器，ApplicationContext为应用上下文，也称为Spring容器。WebApplicationContext是专门为Web应用准备的，它允许以相对于Web根目录的路径中加载配置文件完成初始化工作，是ApplicationContext接口的子接口。

* BeanFactory是Spring框架的基础，面向Spring本身；ApplicationContext面向使用Spring框架的开发者，几乎所有的应用我们都直接使用ApplicationContext而非底层的BeanFactory；WebApplicationContext是专门用于Web应用。
***
##### 父子容器
Spring使用父子容器实现了很多功能，比如在Spring MVC中，***控制器Bean位于子容器中，业务层和持久层Bean位于父容器中***。但即使这样，*控制器Bean也可以引用持久层和业务层的Bean*，而业务层和持久层就看不到控制器Bean。

### bean的基础配置

1. 通过bean标签导入bean对象
2. 属性
   1. `id`bean对象注册时对应的唯一标识（getBean），且setter要与之同名才可注入成功
   2. `class`bean对象注册类型
   3. `name`可有多个，在容器管理时，ref可引用name或id
   4. `scop`变量范围：单例/多例
3. 子标签
   1. `property`依赖配置
   2. 属性`ref`
4. **spring自动装配**
   1. `<bean id="brandService" class="com.catGod.service.impl.BrandServiceImpl" autowire="byType"/>`
   2. `autowire`属性byType，意为按brandService中所需要的bean类型**自动装配**到所需对象中

### bean的生命周期

1. 初始化容器
   1. 创建对象（内存分配）
   2. 执行构造方法 **or** 执行属性注入（setter）**or** 自动装配
   3. **执行bean初始化方法（init）**
2. 使用bean对象：`getBean()`
3. 关闭销毁容器：`close()`
   1. 执行bean销毁方法（destroy）
   2. 执行垃圾回收
##### 生命周期控制
1. 方法控制
- 通过自定初始化和销毁方法管理bean `init-method="init" destroy-method="destroy"`
- 通过实现接口（了解）
1. 关闭容器
- `ClassPathXmlApplicationContext`
   - `close()`：关闭容器
   - `registerShutdownHook()`：注册关闭钩子，关闭容器
---
##全注解开发
重要的注解及含义
1. `@Configuration`：配置类
2. `@ComponentScan`：扫描包
3. `@Bean`：注册引用数据源（外来数据源）
4. `@component`：注册bean对象
   1. `@Service`：业务层
   2. `@Controller`：控制层
   3. `@Repository`：持久层dao层
5. `@PropertySource`：加载properties文件
6. `@Import`：导入其他配置类
7. `@Value`：获取properties文件中的值，用于基础数据类型bean属性注入
#### 加载配置类
1. `@PropertySource("classpath:xxxx.properties")`：加载properties文件
2. `@ComponentScan("com.catGod")`：扫描包
3. 在其他配置类上添加``@configuration``注解声明为配置类
## Spring事务管理
##### 事务定义
   * 一组逻辑操作单元，使数据从一种状态变换到另一种状态。
   * 事务全称叫数据库事务，是数据库并发控制时的基本单位，它是一个操作集合，这些操作要么不执行，要么都执行，不可分割。例如我们的转账这个业务，就需要进行数据库事务的处理。

##### 事务特性
1. Atomic（原子性）：事务中包含的操作被看做一个逻辑单元，这个逻辑单元中的操作要么全部成功，要么全部失败。

2. Consistency（一致性）：只有合法的数据可以被写入数据库，否则事务应该将其回滚到最初状态。在转账的时候不会出现一当少钱了，另一方没有增加的情况。

3. Isolation（隔离性）：事务允许多个用户对同一个数据进行并发访问，而不破坏数据的正确性和完整性。同时，并行事务的修改必须与其他并行事务的修改相互独立。

4. Durability（持久性）：事务完成之后，它对于系统的影响是永久的，该修改即使出现系统故障也将一直保留，真实的修改了数据库。

以上 4 个属性常被简称为 acid（酸的）。
### Spring中的事务管理
Spring中的事务管理主要有两种方式：编程式事务管理和声明式事务管理。
1. 编程式事务管理
过编程的方式来控制事务的提交和回滚。编程式事务管理需要在代码中**显式的开启和关闭事务**，这样就会导致代码的冗余，不利于代码的维护。因此，Spring提供了声明式事务管理，通过声明式事务管理可以将事务的开启和关闭交给Spring容器来管理，从而减少了代码的冗余。
1. 声明式事务管理
通过**配置的方式来管理事务，即通过配置的方式来控制事务的提交和回滚**。声明式事务管理不需要在代码中显式的开启和关闭事务，这样就减少了代码的冗余，提高了代码的可维护性。Spring提供了两种声明式事务管理的方式：基于AspectJ的XML方式和基于注解的方式。
### Spring基于注解的事务管理
* 事务管理员
* 事务协调员
1. 在Spring的配置类中添加`@EnableTransactionManagement`注解，开启事务管理功能。
2. 在需要进行事务管理的方法上添加`@Transactional`注解来完成事务的管理。


