---
layout:     post
title:      SpringMVC
subtitle:   MVC.md
date:       2023-04-08
author:     lily
header-img: img/Spring.jpg
catalog:    true
tags:
    - Spring
---
# SpringMVC
目录
1. [SpringMVC](#springmvc)
   1. [bean的加载控制](#bean的加载控制)
   2. [请求映射路径](#请求映射路径)
      1. [请求参数传递](#请求参数传递)
         1. [josn格式数据传输](#josn格式数据传输)
   3. [RESTful风格](#restful风格)
   4. [统一返回格式](#统一返回格式)
   5. [异常处理器](#异常处理器)
   6. [拦截器](#拦截器)
      1. [过滤器](#过滤器)
---

## bean的加载控制
---
   * SpringMVC控制表现层相关的bean（controller）
   * Spring控制
     * 业务层相关的bean（service）
     * 功能层相关的bean（dao， DataSource）
---
要避免循环依赖，可以使用注解@Lazy
## 请求映射路径
### 请求参数传递
#### josn格式数据传输
    @enableWebMvc
## RESTful风格

## 统一返回格式
## 异常处理器
## 拦截器
### 过滤器