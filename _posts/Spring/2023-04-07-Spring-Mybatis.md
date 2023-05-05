---
    layout:     post
    title:      Spring-Mybatis
    date:       2023-04-07
    author:     lily
    header-img: img/Spring.jpg
    catalog: 	 true
    tags:       Spring
---
# Mybatis
目录

1. [Mybatis](#mybatis)
2. [数据库整体概念](#数据库整体概念)
3. [Mybatis连接数据库](#mybatis连接数据库)
      1. [springboot数据库连接配置](#springboot数据库连接配置)
4. [xml配置开发](#xml配置开发)
5. [使用注解开发](#使用注解开发)
   1. [配置类configClass](#配置类configclass)
6. [整合后的使用规则](#整合后的使用规则)
---
# 数据库整体概念
> 数据库
> 数据库是一个容器，包含了很多数据，当然这些数据可能存在不同的小容器(表)里面。
> 若用水来形容数据，数据库就是水库。
> 
> 数据源
> 数据源是连接到数据库的一类路径，它包含了访问数据库的信息(地址、用户名、密码)。
> 数据源就像是排水管道。
> 
> 数据库连接
> 数据库连接是根据数据源产生的实际连接上数据库的路径。
> 数据库连接就像是管道里面的水管，这些水管都按照管道(数据源)的配置访问数据库。当打开了数据连接的时候，就像是打开了水管一样。
> 
> 数据库连接池
> 每个数据源可能会配置数据库连接池，就像是排水管道的自动化系统。
> 数据库连接池的作用就是维护数据库连接，减少创建和删除数据库连接的操作，来达到减少数据访问时耗的目的。

# Mybatis连接数据库

  ### springboot数据库连接配置
  > 使用SpringBoot连接MySQL数据库需要以下几个依赖：

> spring-boot-starter-data-jpa：用于数据访问以及仓库的存储和检索。
> 
> spring-boot-starter-jdbc：提供了对JDBC的支持，以便创建和使用JdbcTemplate等对象。
> 
> mysql-connector-java：MySQL JDBC 驱动程序。
> 
> spring-boot-starter-web：提供了对Web的支持，包括RESTful、WebSocket等。
> 
> spring-boot-starter-test：提供了对测试的支持，包括JUnit、Mockito等。
> 
> HikariCP：数据库连接池。
> 
> 这些依赖之间的关系如下：
> 
> spring-boot-starter-data-jpa依赖于spring-boot-starter-jdbc和hibernate-core；hibernate-core又依赖于javax.persistence-api和antlr。
> 
> spring-boot-starter-jdbc依赖于spring-jdbc，spring-jdbc又依赖于spring-core和spring-beans。
> 
> mysql-connector-java是mysql数据库的Java连接器。
> 
> spring-boot-starter-web依赖于spring-boot-starter和spring-webmvc。
> 
> HikariCP作为数据库连接池起到了管理、创建和回收连接的作用。它不需要其他依赖，但需要与具体的数据库驱动程序兼容。
> 
# xml配置开发

1. 配置文件
1.  数据库配置文件（例如application.properties），用于定义数据库连接信息。 
2.  MyBatis的配置文件（例如mybatis-config.xml），用于配置MyBatis相关属性，如映射器、类型处理器、数据库连接池等。 
3.  Mapper映射文件，用于定义SQL语句和参数映射关系。 

以下是一个使用Spring整合MyBatis的示例：

1. 数据库配置文件（application.properties）：

```
spring.datasource.driverClassName=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/mydatabase
spring.datasource.username=root
spring.datasource.password=root
```

2. MyBatis的配置文件（mybatis-config.xml）：

```
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${spring.datasource.driverClassName}"/>
        <property name="url" value="${spring.datasource.url}"/>
        <property name="username" value="${spring.datasource.username}"/>
        <property name="password" value="${spring.datasource.password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <!-- Mapper映射文件的位置 -->
    <mapper resource="mapper/UserMapper.xml"/>
  </mappers>
</configuration>
```

3. Spring的配置文件（applicationContext.xml）：

```
<!-- 开启MyBatis扫描 -->
<mybatis:scan base-package="com.example.mapper"/>

<!-- MyBatis配置文件的位置 -->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
  <property name="dataSource" ref="dataSource"/>
  <property name="configLocation" value="classpath:mybatis-config.xml"/>
</bean>

<!-- 数据库连接池 -->
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
  <property name="driverClassName" value="${spring.datasource.driverClassName}"/>
  <property name="url" value="${spring.datasource.url}"/>
  <property name="username" value="${spring.datasource.username}"/>
  <property name="password" value="${spring.datasource.password}"/>
</bean>

<!-- Mapper映射文件自动扫描 -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
  <property name="basePackage" value="com.example.mapper"/>
</bean>
```

4. Mapper映射文件（UserMapper.xml）：

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.mapper.UserMapper">
  <select id="getUserById" parameterType="int" resultType="user">
    select * from user where id = #{id}
  </select>
</mapper>
```
# 使用注解开发
## 配置类configClass

1. 创建配置类替换原本配置文件
> 1. Mybatis：创建了一个名为MybtatisConfig类
>    1. mapperScannerConfigurer的Bean，该Bean用于自动扫描com.catGod.dao包下的MyBatis Mapper接口，并将它们注册为Spring的Bean以便使用。具体来说，它使用了MapperScannerConfigurer这个Spring组件，并将需要扫描的包名设置为com.catGod.dao。该配置可让我们更加简化MyBatis的Mapper接口的配置，避免手动一个个配置接口到Spring容器中的麻烦。
>    2. 加载sqlSscetionFactory对象： 定义获取DataSoruce的Mybatis相关配置Bean，有关dataSoruce注入以及jdbc连接数据库设置的相关配置，最后获取DataSoruce对象
>    3. jdbc配置文件中的配置信息变为，基本类型，用基本类型装配的方式写入jdbc配置文件

```java
@Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
    MapperScannerConfigurer msc = new MapperScannerConfigurer();
    msc.setBasePackage("com.catGod.dao");
    return msc;
}
```

2. 启用注解
> spring整合mybatis就是使mybatis的使用规则遵循spring的使用规则，即全注解开发。
> 1. mybatis配置类属于配置信息设定相关的类，加上@config属性
> 2. 在spring中添加扫描范围，使得该配置类可以被spring配置类扫描到（管理到）

# 整合后的使用规则

1. 在mapper接口中定义sql方法，使用注解定义sql语句
2. 在注册Spring配置类时加载Mybatis配置类获取ctp对象
3. 用ctp对象获取相关Bean，再调用具体sql查询方法
