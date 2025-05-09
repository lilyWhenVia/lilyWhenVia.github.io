# springboot默认配置
## ComponentScan与MapperScan
@ComponentScan是SpringBoot的注解，如其意“Sacn”是扫描的意思。
**SpringBoot在没配置@ComponentScan的情况下，默认只扫描和主类处于同包下的Class**
1. @ComponentScan是组件扫描注解，用来扫描@Controller  @Service @Repository这类,主要就是定义扫描的路径从中找出标志了需要装配的类到Spring容器中
2. @MapperScan 是扫描mapper类的注解,就不用在每个**dao层**的各个类上加@Mapper了

## controller层
1. ``@RestController("/xxx")``,其中xxx地址是标识该controller的标识但是访问时并不用加上该地址，应该直接访问方法地址。
2. 可以通过idea自带的http请求工具来测试controller层的方法，该请求工具会自动生成url请求地址，可以在上面验证访问的地址是否正确。
   ```java
    ### @PostMapping("/register") idea自动生成的请求地址
    POST http://localhost:8080/user-center/register
    Content-Type: application/json

    {
    "username": "user1",
    "userPassword": "password1",
    "checkPassword":"password1"
    }

   ```
# debug
## controller层debug
1. 用debug可以发现很多网络请求的细节，请求的参数、参数的传递和获取等等。

