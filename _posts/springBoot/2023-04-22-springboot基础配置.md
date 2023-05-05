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

# mybatis-plus in Springboot
