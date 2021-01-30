[TOC]

# SpringCloud

![image-20210130133637144](../images/image-20210130133637144.png)

微服务架构4个问题：

* 服务很多，客户端该怎么访问
* 这么多服务，服务之间如何通信
* 这么多服务，如何治理
* 服务挂了怎么办

解决方案：

* Spring Cloud Netflix（一站式解决方案）
	* API网关，zuul组件
	* Feign ---HttpClinet：Http通信方式，同步，阻塞
	* 服务注册发现：Eureka
	* 熔断机制：Hystrix
* Apache Dubbo Zookeeper（半自动，需要整合别人的）
	* API：没有，找第三方组件，或者自己实现
	* Dubbo：RPC通信框架
	* Zookeeper：
	* 没有：借助第三方
* Spring Cloud Alibaba（一站式解决方案）

## 1 常见问题

### 1.1 什么是微服务

* **微服务**：强调的是服务的大小，关注的是某一个点，是具体解决某一个问题并提供落地对应服务的一个服务应用，狭义的看，可以看作是IDEA里面的一个个module
* **微服务架构**：微服务架构是一种架构模式，它提倡将单一应用程序划分成一组小的服务，服务之间互相协调，互相配合，为用户提供最终价值。每个服务运行在其独立的进程中，服务于服务间采用轻量级的通信机制相互协作，每个服务都围绕着具体的业务进行构建，并且能够被独立的部署到生产环境中，另外，应尽量避免统一的，集中式的服务管理机制，对具体的一个服务而言，应根据业务上下文，选择合适的语言、工具对其进行构建
* **微服务化核心**：就是将传统的一站式应用，根据业务拆分成一个一个的服务，彻底的去耦合，每一个微服务提供单个业务功能的服务，一个服务做一件事情，从技术角度看就是一种小而独立的处理过程，类似进程的概念，能够自行单独启动或销毁，拥有自己独立的数据库。

![image-20210130134724720](../images/image-20210130134724720.png)

### 1.2 微服务之间是如何独立通讯的

### 1.3 SpringCloud和Dubbo有哪些区别

|              |     Dubbo     |           Spring            |
| :----------: | :-----------: | :-------------------------: |
| 服务注册中心 |   ZooKeeper   | SpringCloud Netfilx Eureka  |
| 服务调用方式 |      RPC      |          REST API           |
|   服务监控   | Dubbo-monitor |      SpringBoot Admin       |
|    断路器    |    不完善     | SpringCloud Netfilx Hystrix |
|   服务网关   |      无       |  SpringCloud Netfilx Zuul   |
|  分布式配置  |      无       |     SpringCloud Config      |
|   服务跟踪   |      无       |     SpringCloud Sleuth      |
|   消息总线   |      无       |       SpringCloud Bus       |
|    数据流    |      无       |     SpringCloud Stream      |
|   批量任务   |      无       |      SpringCloud Task       |

**最大区别：SpringCloud抛弃了Dubbo的RPC通信，采用的是基于HTTP的REST方式**

严格来说，这两种方式各有优劣。从一定程度上来说，后者牺牲了服务调用的性能，但也避免了上面提到的原生RPC带来的问题。而且REST相比RPC更加灵活，服务提供方和调用方的依赖只依靠一纸契约，不存在代码级别的强依赖，这在强调快速演化的微服务环境下，显得更加合适

### 1.4 SpringBoot和SpringCloud，谈谈对他们的理解

* SpringBoot专注于快速方便的开发单个个体微服务
* SpringCloud是关注全局的微服务协调整治治理框架，它将SpringBoot开发的一个个单体微服务整合并管理起来，为各个微服务之间提供：配置管理、服务发现、断路器、路由、微代理、事件总线、全局锁、决策竞选、分布式会话等集成服务
* SpringBoot可以离开SpringCloud独立使用，开发项目，但是SpringCloud离不开SpringBoot，属于依赖关系
* **SpringBoot专注于快速、方便的开发单个个体微服务，SpringCloud关注全局的服务治理框架** 

### 1.5 什么是服务熔断，什么是服务降级

### 1.6 微服务的优缺点分别是什么

![image-20210130140107632](../images/image-20210130140107632.png)

![image-20210130140134828](../images/image-20210130140134828.png)

### 1.7 你知道的微服务技术栈有哪些

![image-20210130133156000](../images/image-20210130133156000.png)

### 1.8 Eureka和Zookeeper都可以提供服务注册与发现的功能，请说说两者的区别

