---
    layout:     post
    title:      maven & tomcat
    date:       2023-04-07
    author:     lily
    header-img: img/JavaWEB.jpg
    catalog: 	 true
    tags:       JavaWEB
---

# Maven
1. maven 是一个java项目管理工具，里面含有很多相关的帮助插件,其中maven helper可以帮助更方便的一键编译运行maven项目等
2. maven编译,多次编译会覆盖原有的项目内文件.若文件存在,新编译文件会覆盖,但不会删除已存在文件
1. maven-install-plugin:2.4 already delet from meven/plugins, using install:install as replacement
2. 当前文件下目录下配置文件的路径`System.getProperty("user.dir");`
# Tomcat
本地集成tomcat修改虚拟路径：
您可以通过修改Tomcat的conf目录下的server.xml文件来配置虚拟目录，从而设置项目访问路径。
在**<Host name="localhost" appBase="webapps"…**中添加
`**<Context path="files" reloadable="true" docBase="/tomcat VirtualDir" debug="" />**。`
其中**path="files"**为配置的虚拟路径，**docBase="/tomcat VirtualDir"**为文件存储的物理路径。
通过URL访问项目资源，通常的URL资源访问路径为
`http://服务器IP:端口号/项目名/资源路径`
