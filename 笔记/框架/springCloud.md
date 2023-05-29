# 微服务简介

## 什么是微服务？

什么是微服务呢？

**就是将一个大的应用，拆分成多个小的模块，每个模块都有自己的功能和职责，每个模块可以**

**进行交互，这就是微服务**

**简而言之，微服务架构的风格，就是将单一程序开发成一个微服务，**

**每个微服务运行在自己的进程中，并使用轻量级通信机制，通常是**

**HTTP RESTFUL API 。这些服务围绕业务能力来划分构建的，并通**

**过完全自动化部署机制来独立部署这些服务可以使用不同的编程语**

**言，以及不同数据存储技术，以保证最低限度的集中式管理。**



## 微服务的自动化部署(CI /CD）(持续集成 持续交付)

**（CI /CD）(持续集成 持续交付)** CI表示持续集成	CD表示持续交付	可自动完成编译部署等操作

持续交付通常是指开发人员对应用的更改会自动进行错误测试并上传到存储库
持续部署指的是自动将开发人员的更改从存储库发布到生产环境	为了解决因手动流程降低应用交付速度

使用了微服务之后，系统被拆分成多个微服务，若要部署，则有多少个服务，就要部署多少次，按照单体架构的部署步骤，时间划分长
使用自动化部署(例如开源组件 **Jenkins**)



![img](file:///C:\Users\ljxxx\AppData\Roaming\Tencent\Users\1755733446\QQ\WinTemp\RichOle\[NPD]L6EN139I1Z9ZMZ$5SU.png)

## 微服务架构的设计原则

**开闭原则 单一原则** 6 大设计原则 架构设计和代码设计思路一样的

可以了解设计原则



# SpringCloud简介

Spring Cloud 作为 **Java** 语言的**微服务框架**，它依赖于 **Spring Boot**，有快速开发、持续交付和容易部署等特点。

现主要有Spring 和 Netflix、alibaba	三家公司维护和开发
而每个公司的版本不同，对应要求的springboot版本也不同

企业中 用的最多的版本	spring **Hoxton.SR12** 	boot **2.3.12.RELEASE**	 alibaba **2.2.7.RELEASE**



## SpringCloud版本对应关系

之前的版本命名规则为 Axxx Bxxx Cxxx Dxxx Exx ...Hxx 从H之后 就以年份命名 2020版

**https://start.spring.io/actuator/info**	中写明 什么版本的cloud对应什么版本的springboot

cloud和springboot版本一定要对应上

![image-20230522203103534](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522203103534.png)



而alibaba的版本也和spring上不同

官网https://github.com/alibaba/spring-cloud-alibaba wiki中查看

其中有详细说明 alibaba的每个版本对应spring中的什么版本和springboot的什么版本

![image-20230522203242994](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522203242994.png)









