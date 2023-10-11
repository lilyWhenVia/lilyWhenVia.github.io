---
layout:     post
title:      SpringSecurity
subtitle:   SpringSecurity基础
date:       2023-05-15
author:     lily
header-img: img/springSecurity.jpg
catalog:    true
tags:
    - springSecurity
---
# spring security 
<!-- 目录 -->
## 使用BCryptPasswordEncoder进行密码加密和校验的Spring Security实现

在Spring Security中，我们可以使用`BCryptPasswordEncoder`来对密码进行加密和校验。下面是相关步骤的总结：

### `BCryptPasswordEncoder`作为密码加密器
将其注入到Spring容器中，可以在配置类中添加以下代码：

   ```java
   @Configuration
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
   
       @Bean
       public PasswordEncoder passwordEncoder() {
           return new BCryptPasswordEncoder();
       }
   
       // 其他配置...
   }
   ```

   这样，`BCryptPasswordEncoder`就可以在应用程序中被使用了。

在用户认证服务中查询数据库并验证密码，实现`UserDetailsService`接口的`loadUserByUsername`方法：

   ```java
   @Service
   public class UserDetailsServiceImpl implements UserDetailsService {
   
       @Autowired
       private UserRepository userRepository;
   
       @Autowired
       private PasswordEncoder passwordEncoder;
   
       @Override
       public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
           User user = userRepository.findByUsername(username);
           if (user == null) {
               throw new UsernameNotFoundException("User not found");
           }
   
           // 校验密码
           String rawPassword = user.getPassword(); // 从数据库中获取已加密的密码
           if (!passwordEncoder.matches(rawPassword, userEnteredPassword)) {
               throw new BadCredentialsException("Invalid password");
           }
   
           // 创建并返回用户详细信息
           // ...
       }
   }
   ```

### 关于BCrypt密码加密器的盐（salt）的概念。
BCrypt加密算法会自动生成一个随机的盐，并将盐与密码一起进行哈希处理，然后将哈希结果存储在数据库中。在密码校验时，盐会从存储的哈希结果中提取出来，并与用户输入的密码进行比较，以验证密码是否匹配。

Spring Security的`BCryptPasswordEncoder`在`matches`方法中会自动提取并使用存储的哈希结果中的盐进行密码比较。下面是`BCryptPasswordEncoder`的`matches`方法的简化版本的伪代码：

```java
public boolean matches(CharSequence rawPassword, String encodedPassword) {
    // 从数据库中提取盐值
    String salt = encodedPassword.substring(0, 29);
    
    // 对用户输入的密码进行哈希处理
    String hashedPassword = hash(rawPassword, salt);
    
    // 比较哈希结果
    return hashedPassword.equals(encodedPassword);
}
```

在这个伪代码中，`encodedPassword`是存储在数据库中的加密密码，包含盐和哈希结果。`rawPassword`是用户输入的密码。`matches`方法首先从`encodedPassword`中提取前29个字符作为盐，然后使用盐对`rawPassword`进行哈希处理，生成一个哈希结果。最后，比较生成的哈希结果与`encodedPassword`中的哈希结果是否相等，以确定密码是否匹配。

由于`matches`方法会自动提取盐并进行比较，您无需手动存储和管理盐。整个过程由`BCryptPasswordEncoder`负责处理，确保密码的正确比较和校验。


在`loadUserByUsername`方法中，我们首先通过用户名从数据库中查询用户信息。然后，使用`BCryptPasswordEncoder`的`matches`方法将用户输入的密码与数据库中存储的加密密码进行比较。如果密码匹配，则认证成功；否则，抛出异常表示密码不正确。

需要注意的是，`BCryptPasswordEncoder`的`matches`方法会自动从加密后的密码中提取盐值，并将其用于密码比较，无需手动解密。

通过以上配置和代码，Spring Security会自动处理密码的加密和校验逻辑，您只需要实现`UserDetailsService`接口中的`loadUserByUsername`方法，并返回正确的用户信息。

总结一下使用`BCryptPasswordEncoder`进行密码加密和校验的关键点：

- 配置`BCryptPasswordEncoder`作为密码加密器，并注入到Spring容器中。
- 在用户认证服务中查询数据库并验证密码时，通过`BCryptPasswordEncoder`的`matches`方法进行密码校验，无需手动解密和提取盐值。


# Filter的自定义注册和配置
## filter基本用法
1. ``doFilter``方法中的``        filterChain.doFilter(request, response);``是将请求传递给下一个filter，如果没有下一个filter则传递给servlet。
2. **每个filter执行完之后需要手动放行**，因为默认是不放行的，因此若不在编码中显示放行之后的servlet或filter则不会被访问到。
3. ``return``语句，若在filter中使用return结束方法，则最后按责任链执行时该filter**不会**接着``filterChain.doFilter(request, response);``执行，但是后面的filter和servlet**会**被执行。

## security中的过滤器体系链
1. Spring Security 的核心主要是一系列的过滤器链。**这些过滤器本质上是一连串的Filter， 然后又以一个独立的Filter的形式插入到Filter Chain里，其名为FilterChainProxy。**

2. Spring Security 在 Servlet 的过滤链（filter chain）中注册了一个过滤器 FilterChainProxy，它会**把请求代理到 Spring Security 自己维护的多个过滤链**，每个过滤链会匹配一些 URL，如果匹配则执行对应的过滤器。过滤链是有顺序的，一个请求只会执行第一条匹配的过滤链。Spring Security 的配置本质上就是新增、删除、修改过滤器。

![filter](https://lingmoumou-blog.oss-cn-beijing.aliyuncs.com/blog/4d513018/page_11.png)

