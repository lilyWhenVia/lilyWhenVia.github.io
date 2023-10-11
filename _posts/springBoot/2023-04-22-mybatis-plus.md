<!-- 目录 -->
[mybatis-plus简介](#mybatis-plus简介)
[mybatis-plus与mybatis的区别](#mybatis-plus与mybatis的区别)
[plus的使用](#plus的使用)
[比较](#比较)
[plus的使用](#plus的使用)


# mybatis-plus简介
1. mybatis-plus仅是在mybatis框架上做增强，并没有修改整个框架的核心逻辑。所以用基础的mybatis设置也是可以的。
2. plus maven依赖坐标
   **添加mybatis-plus依赖时需要注意，mybatis-plus依赖于mybatis，所以需要先添加mybatis依赖**
   因为mapper的实现类生成是依靠于mybatis，如果不添加mybatis依赖，会导致mapper层的bean无法被注入，以及sqlsssessionfactory的bean无法整合到mapper层的实现类中注入
 ```java
<!--        mybatis-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.5.2</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>3.0.1</version>
        </dependency>
```
3. 
1. plus主要增强功能
   1. sql不用自己书写，但支持自己定制sql
   2. 
---

# mybatis-plus与mybatis的区别
MyBatis和MyBatis-Plus都是Java中的ORM框架，用于简化数据库操作。以下是它们之间的区别：

1. MyBatis是一个轻量级的ORM框架，它提供了一个简单的SQL映射文件和一个运行时SQL执行引擎来执行SQL语句。MyBatis通过XML或注解来定义SQL语句。MyBatis-Plus是在MyBatis的基础上进行的封装，它提供了更多的功能和便利的API，减少了开发者的工作量。

2. MyBatis-Plus提供了一些常用的CRUD方法，例如insert、delete、update和select等，这些方法可以直接使用，而不需要自己编写SQL语句。MyBatis只提供了基本的SQL执行功能，需要开发者自己编写SQL语句。

3. MyBatis-Plus提供了一些高级功能，例如分页查询、逻辑删除、自动填充等。这些功能可以大大提高开发效率。MyBatis没有这些功能，需要开发者自己实现。

4. MyBatis-Plus提供了一些代码生成工具，可以根据数据库表自动生成Java代码，减少了开发者的工作量。MyBatis没有这个功能。

总之，MyBatis-Plus是MyBatis的升级版，提供了更多的功能和便利的API，可以大大提高开发效率。但是，MyBatis在某些方面可能更加灵活和自由，需要开发者自己实现一些高级功能。

## 比较
Mybatis-Plus是一个Mybatis的增强工具，它在Mybatis的基础上做了增强，却不做改变。我们在使用Mybatis-Plus之后既可以使用Mybatis-Plus的特有功能，又能够正常使用Mybatis的原生功能。Mybatis-Plus(以下简称MP)是为简化开发、提高开发效率而生，但它也提供了一些很有意思的插件，比如SQL性能监控、乐观锁、执行分析等。

# plus的使用
以下是一个使用MyBatis-Plus的示例代码：

引入依赖
在项目的pom.xml文件中添加MyBatis-Plus的依赖：
```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.0</version>
</dependency>
```
配置数据源
在项目的配置文件中配置数据源，例如application.yml或application.properties。这里以application.yml为例：
```yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
```
创建实体类
创建Java实体类，并使用MyBatis-Plus的注解来映射数据库表和字段。例如，创建一个User实体类：
```java
@Data
@TableName("user")
public class User {
    @TableId(type = IdType.AUTO)
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```
其中，@TableName注解用于指定实体类对应的数据库表名，@TableId注解用于指定主键的生成策略。

创建Mapper接口
创建Mapper接口，并继承MyBatis-Plus提供的BaseMapper接口，该接口提供了常用的CRUD方法。例如，创建一个UserMapper接口：
```java
public interface UserMapper extends BaseMapper<User> {
}
```
这里只需要继承BaseMapper接口即可，不需要自己编写SQL语句。

使用Mapper接口
在代码中使用Mapper接口中的方法来进行数据库操作，例如插入、删除、更新和查询等。例如，插入一条用户记录：
```java
@Autowired
private UserMapper userMapper;

public void addUser(User user) {
    userMapper.insert(user);
}
```
使用高级功能
MyBatis-Plus提供了一些高级功能，例如分页查询、逻辑删除、自动填充等，可以通过注解或配置文件来启用这些功能。例如，使用分页查询：
```java
public void getUserList(int pageNum, int pageSize) {
    Page<User> page = new Page<>(pageNum, pageSize);
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    wrapper.eq("age", 18);
    userMapper.selectPage(page, wrapper);
    List<User> userList = page.getRecords();
}
```
这里使用了MyBatis-Plus提供的Page类来进行分页查询，使用QueryWrapper类来构造查询条件。

使用代码生成器
MyBatis-Plus提供了一个代码生成器，可以根据数据库表自动生成Java代码和Mapper接口。例如，使用Maven插件来生成代码：
```xml
<build>
    <plugins>
        <plugin>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-maven-plugin</artifactId>
            <version>3.4.0</version>
            <executions>
                <execution>
                    <id>generate</id>
                    <phase>generate-sources</phase>
                    <goals>
                        <goal>generator</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <globalConfig>
                    <author>author</author>
                    <outputDir>src/main/java</outputDir>
                    <fileOverride>true</fileOverride>
                    <open>false</open>
                </globalConfig>
                <dataSource>
                    <url>jdbc:mysql://localhost:3306/test?useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=Asia/Shanghai</url>
                    <driverName>com.mysql.cj.jdbc.Driver</driverName>
                    <username>root</username>
                    <password>root</password>
                </dataSource>
                <packageConfig>
                    <parent>com.example.demo</parent>
                    <entity>entity</entity>
                    <mapper>mapper</mapper>
                    <service>service</service>
                    <serviceImpl>service.impl</serviceImpl>
                    <controller>controller</controller>
                </packageConfig>
                <strategy>
                    <tablePrefix>tbl_</tablePrefix>
                </strategy>
            </configuration>
        </plugin>
    </plugins>
</build>
```
这里使用了MyBatis-Plus提供的Maven插件来生成代码，可以根据配置文件中的信息来生成Java代码和Mapper接口。执行mvn generate-sources命令即可生成代码。


# mybatis-plus与springboot整合版本问题
mybatis spring boot starter的版本选择要求与MyBatis、MyBatis-Spring、Java和Spring Boot的版本有关。例如：

- 使用**MyBatis 3.5+、MyBatis-Spring 3.0、Java 17+和Spring Boot 3.0-3.1（尚未发布），您可以使用mybatis spring boot starter的master**分支。
- 使用**MyBatis 3.5+、MyBatis-Spring 2.0+、Java 8+和Spring Boot 2.1-2.6，您可以使用mybatis spring boot starter的2.3.x**分支。
- 使用**MyBatis 3.4+、MyBatis-Spring 1.3+、Java 6+和Spring Boot 1.4-2.0，您可以使用mybatis spring boot starter的1.3.x**分支。

**可以在GitHub上查看mybatis spring boot starter的不同分支和版本信息¹**。

 与必应的对话， 2023/5/15
(1) GitHub - mybatis/spring-boot-starter: MyBatis integration with .... https://github.com/mybatis/spring-boot-starter.
(2) mybatis-spring. https://mybatis.org/spring/ja/boot.html.
(3) mybatis-spring. https://mybatis.org/spring/boot.html.

## org.mybatis.spring.boot 版本

**org.mybatis.spring.boot 是 MyBatis 官方提供的一个 Spring Boot Starter，它能够简化 MyBatis 在 Spring Boot 项目中的配置。org.mybatis.spring.boot 的版本与 MyBatis 和 Spring Boot 的版本有关。**
具体而言，org.mybatis.spring.boot 的版本号通常遵循以下格式：
**``
{MyBatis版本号}.{org.mybatis.spring.boot主版本号}.{org.mybatis.spring.boot次版本号}
``**

其中，*MyBatis版本号是 MyBatis 的版本号*，org.mybatis.spring.boot主版本号和次版本号则与 Spring Boot 的版本有关。一般来说，不同的 org.mybatis.spring.boot 版本号适配的 Spring Boot 版本是不同的，需要根据实际情况选择合适的版本。

> 例如，对于 Spring Boot 2.5.x，可以使用 org.mybatis.spring.boot 2.1.5 版本，它对应的 MyBatis 版本为 3.5.x。而对于 Spring Boot 2.6.x，可以使用 org.mybatis.spring.boot 2.2.0 版本，它对应的 MyBatis 版本也为 3.5.x。

# 默认配置

​当使用 MyBatis-Plus 时，它会默认给实体类的字段进行驼峰式到下划线的转换，以生成对应的表字段名。例如，实体类中的 `private String userName` 字段会被自动转换成 `user_name` 字段名。

此外，MyBatis-Plus 还提供了全局配置和注解配置两种方式来控制表名和表字段的映射关系。以下是默认的一些配置：

| 配置项 | 默认值 | 说明 |
| ------ | ------ | ------ |
| 数据库表名 | 实体类名（驼峰转下划线） | 默认的表名是根据实体类名自动转换为下划线格式，例如 `UserInfo` 对应的表名是 `user_info` |
| 主键字段名 | id | 默认的主键字段名是 `id` |
| 驼峰转下划线 | 开启 | 默认开启驼峰转下划线功能 |
| 字段名与属性名的映射 | 与属性名一致 | 默认情况下，字段名与属性名一致，不需要进行额外配置 |

如果想要修改这些默认配置，可以通过在 `application.yml` 文件中配置 MyBatis-Plus 的全局配置来实现，例如：

```yaml
mybatis-plus:
  global-config:
    db-config:
      table-prefix: tb_  # 表名前缀
      column-underline: false  # 关闭驼峰转下划线
```

或者使用 MyBatis-Plus 提供的注解来进行配置，例如：

```java
@TableName("tb_user")  // 指定表名
public class User {
    
    @TableId(type = IdType.AUTO)  // 指定主键生成策略
    private Long id;
    
    @TableField(value = "user_name")  // 指定表字段名
    private String userName;
}
```

在上述代码中，`@TableName` 注解用于指定表名，`@TableId` 注解用于指定主键生成策略和字段名，`@TableField` 注解用于指定表字段名。需要注意的是，使用注解配置会覆盖全局配置。


# 增强注解
以下是 MyBatis-Plus 中常用的注解及其作用：

| 注解 | 说明 |
| ---- | ---- |
| `@TableName` | 标识实体类对应的数据库表名，可以用于指定表名，如 `@TableName("user")`，默认情况下会根据实体类名转换为下划线格式的表名 |
| `@TableId` | 标识实体类的主键字段，用于指定主键的生成策略和字段名，常用属性包括 `type`（主键生成策略）、`value`（字段名）等 |
| `@TableField` | 标识实体类的字段，用于指定字段名和其他属性，常用属性包括 `value`（字段名）、`exist`（是否为数据库字段）、`fill`（插入或更新时的填充策略）等 |
| `@TableLogic` | 标识逻辑删除字段，用于指定逻辑删除的值，默认情况下为 `0`（未删除）和 `1`（已删除），可以通过 `value` 属性自定义逻辑删除值 |
| `@Version` | 标识乐观锁字段，用于实现乐观锁功能，需要指定字段名和乐观锁的类型，常用类型包括 `int`、`Long`、`Date` 等 |
| `@EnumValue` | 标识枚举字段，用于指定数据库中存储的枚举值，默认情况下枚举字段存储的是枚举的索引值，使用 `@EnumValue` 可以指定存储的枚举值 |
| `@JsonIgnore` | 标识字段在序列化时忽略，用于排除字段在 JSON 序列化时的输出 |
| `@JsonFormat` | 标识日期字段的格式，用于指定日期的格式化方式 |

这些注解可以根据实际需求在实体类中使用，以方便地进行数据库表映射、乐观锁、逻辑删除等操作。请根据具体场景选择适合的注解进行使用。

> **在使用 @TableName 注解手动指定表名后，table-prefix 配置将不会再生效**。在实体类上使用了 @TableName 注解并指定了表名，MyBatis-Plus 将使用您手动指定的表名，而不会再添加前缀。