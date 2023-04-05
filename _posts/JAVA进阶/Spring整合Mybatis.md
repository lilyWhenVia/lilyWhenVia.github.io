<a name="gQBT6"></a>
# 数据库概念
:::tips
数据库<br />数据库是一个容器，包含了很多数据，当然这些数据可能存在不同的小容器(表)里面。<br />若用水来形容数据，数据库就是水库。

数据源<br />数据源是连接到数据库的一类路径，它包含了访问数据库的信息(地址、用户名、密码)。<br />数据源就像是排水管道。

数据库连接<br />数据库连接是根据数据源产生的实际连接上数据库的路径。<br />数据库连接就像是管道里面的水管，这些水管都按照管道(数据源)的配置访问数据库。当打开了数据连接的时候，就像是打开了水管一样。

数据库连接池<br />每个数据源可能会配置数据库连接池，就像是排水管道的自动化系统。<br />数据库连接池的作用就是维护数据库连接，减少创建和删除数据库连接的操作，来达到减少数据访问时耗的目的。
:::
<a name="mbu5q"></a>
# Mybatis连接数据库

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
<a name="sRWBj"></a>
# 使用注解开发
<a name="a7Vr2"></a>
## 配置类
它创建了一个名为mapperScannerConfigurer的Bean，该Bean用于自动扫描com.catGod.dao包下的MyBatis Mapper接口，并将它们注册为Spring的Bean以便使用。具体来说，它使用了MapperScannerConfigurer这个Spring组件，并将需要扫描的包名设置为com.catGod.dao。该配置可让我们更加简化MyBatis的Mapper接口的配置，避免手动一个个配置接口到Spring容器中的麻烦。
```java
@Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
    MapperScannerConfigurer msc = new MapperScannerConfigurer();
    msc.setBasePackage("com.catGod.dao");
    return msc;
}
```
