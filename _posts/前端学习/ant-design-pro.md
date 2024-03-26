# yarn
## 关于yarn tyarn npm cnpm的区别
1. npm是nodejs的包管理工具，yarn是facebook开发的另一个包管理工具，tyarn是yarn的国内镜像（taobao镜像源），cnpm是npm的国内镜像。
2. 推荐使用 tyarn 来进行包管理，可以极大地减少 install 的时间和失败的概率，并且完全兼容 npm。如果喜欢使用 npm 而不是 yarn，可以使用 cnpm, 安装速度比 tyarn 更快，但是与 npm 不是完全兼容。
## yarn和tyarn的安装
1. yarn的安装``npm install -g yarn`` ; tyarn的安装``npm install -g tyarn`` 或者 ``yarn global add tyarn`` 或者 ``npm i yarn tyarn -g``
2. 全局路径：通过npm下载的工具都会在电脑上的 ``E:\NodeJs\node_global``中（应该是nodejs配置中设置的安装路径），其中也会包含yarn、tyarn安装的依赖包的路径。**只要在环境变量中配置yarn的路径，就可以在窗口直接使用yarn代替npm**

---

# 框架的下载
## 使用yarn
1. 下载 pro-cli 来快速的初始化脚手架。 ``yarn add @ant-design/pro-cli -g `` 。（官网给出的指令是``npm i @ant-design/pro-cli -g``，因为我想尝试使用yarn并行下载，但是没有在官网查到yarn下载adsp的指令，所以尝试使用add指令，发现指令正确。
2. **在webstorm中**，准备项目的文件目录下执行命令``pro create myapp``，使用pro指令下载项目
3. 选择项目模板
    ```
    ? 🐂 使用 umi@4 还是 umi@3 ? (Use arrow keys)
      umi@4
    ❯ umi@3
    ? 🚀 要全量的还是一个简单的脚手架? (Use arrow keys)
    ❯ simple
      complete
    ```
4. 为了获得自定义更加丰富的页面最后添加了preset-ui插件 ``yarn add @umijs/preset-ui`` ，会在项目外新生成一个依赖目录（node_module）。

---

# 项目启动
1. 安装依赖：安装依赖时发现node版本过高（18），需要使用node的16版本，使用nvm切换版本
2. 使用指令```yarn```即可根据package.json中的依赖项下载依赖

---
# 使用nvm时遇到的bug
1. nvm切换node版本时其实是通过快捷方式指向的特定的node地址，发现本地nvm根本无法通过快捷方式指向node所在文件。
2. 一开始以为是nvm自动配置的两个环境变量出现了错误``syslink``和``nvm_home``，所以重新设置了路径，结果发现在powershell中输入指令``node -v``无效。并且无法使用``npm install``。
3. 发现nvm的配置文件``settings.txt``中的``root``指向了错误的地址，修改后发现无效。
4. 在网上查找到nvm新版本指令``nvm debug``后发现nvm根本没有指定（激活）到任何一个node版本
5. 根据stakekeoverflow上的回答，在下载并且使用nvm之前应该把所有的node版本文件都卸载干净，且环境变量也要删除干净。再重新下载nvm（for windows版本最新版支持win），问题就解决了。

# 路由自动生成
**前提**：为了给前端项目删去一些不必要的组件和代码，删除了一部分文件，但是由于这些文件在其他地方有所引用，直接启动项目会报错，所以还要在被引用的地方删除对应的代码。

而我删除的文件在路由中被引用到了，手动删除了报错部分引用相关文件的代码，但是一编译运行还是会报错。才发现在``config.ts``中的路由配置中，还有一部分路由是自动生成的，而这部分路由正是引用了我删除的文件，**要在``config.ts``中删除对应的路由配置。**
>在这一部分，脚手架通过结合一些配置文件、基本算法及工具函数，搭建好了路由和菜单的基本框架，主要涉及以下几个模块/功能：
>1. 路由管理 通过约定的语法根据在 **config.ts** 中配置路由。
>2. 菜单生成 根据路由配置来生成菜单。菜单项名称，嵌套路径与路由高度耦合。
>3. 面包屑 组件 PageContainer 中内置的面包屑,也可通过 RouteContext 提供的信息自定义生成。

# 代理proxy
ant-design-pro自动配置了代理，可以在``proxy.ts``进行具体的正向代理配置，在chrome中可以通过抓包工具发现项目请求的地址还是前端项目的端口（域名不同http://localhost:8000,就属于跨域），如果请求响应成功在**chrome中可以直接看到请求的真实地址**
```xml
# 在response header中可以看到
X-Real-Url:http://localhost:8080/user-center/login
```
## 正向代理和反向代理
1. 正向代理：代理客户端，客户端向代理服务器发送请求，代理服务器再向真实服务器发送请求，代理服务器将真实服务器的响应返回给客户端。
>  1. 访问原来无法访问的资源，如google
> 2. 可以做缓存，加速访问资源
> 3. 对客户端访问授权，上网进行认证                                                                                                                                                 
> 4. 代理可以记录用户访问记录（上网行为管理），对外隐藏用户信息
2.  反向代理：代理服务器代理真实服务器，客户端向代理服务器发送请求，代理服务器再向真实服务器发送请求，真实服务器将响应返回给代理服务器，代理服务器再将响应返回给客户端。
> 反向代理的作用:
>   1. 保证内网的安全，阻止web攻击，大型网站，通常将反向代理作为公网访问地址，Web服务器是内网。
>   2. 负载均衡，通过反向代理服务器来优化网站的负载。