DUBBO是一个分布式服务框架，致力于提供高性能和透明化的RPC远程服务调用方案，是阿里巴巴SOA服务化治理方案的核心框架，每天为2,000+个服务提供3,000,000,000+次访问量支持，并被广泛应用于阿里巴巴集团的各成员站点。
不巧的是我司也在用，之前的解决方案是JAVA包装成HTTP的restful API给中间层Node.js或者PHP调用，但是其实有时候需求比较紧急的情况下，直接RPC远程调用可能能解决一些燃眉之急。

由于技术栈是PHP和Node.js，前一阵子研究了一下，总结一下，希望能帮助到有类似场景的同学，也分享一下遇到这种根本不了解的问题是如何去解决的过程，PS，我之前没有JAVA开发背景和PHP开发背景。

一，了解dubbo和他的RPC远程调用原理。

首先是这张图分了几个角色，是从官网拿来的：

Provider: 暴露服务的服务提供方。
Consumer: 调用远程服务的服务消费方。
Registry: 服务注册与发现的注册中心。
Monitor: 统计服务的调用次调和调用时间的监控中心。
Container: 服务运行容器。
说的通俗一点，我们要拿PHP或者NodeJs实现一个Consumer，而Consumer与Monitor和Registry是有关联的，与Provider是有调用关系的。

这个图和上面这段话理解后，简单分析下，dubbo服务的Registry一般使用的都是zookeeper作为注册中心，Monitor需要在消费方和服务提供方进行监控统计，这里只需要实现Monitor消费方的通知即可。

二，实现方案。
上面只是简单的分析出来需求，那么如何实现这个Consumer呢，通过查看几个已经存在的开源项目，比如node.js比较有名的，很多人都在用的，支持hessian协议的 p412726700/node-zookeeper-dubbo，还有支持jsonrpc调用的hufeng/node-jsonrpc-dubbo，他们的实现基本上思路是一样的，包括几个PHP的项目：quickj/dubbo-php-client

既然大家都已经实现了，我讲讲我看到的源码和一些关键代码吧。

三，Node.js连接原理
1，基本所有的库都是使用alexguan/node-zookeeper-client来连接的Registry，使用zookeeper.createClient方法来创建zookeeper实例。

2，从zookeeper连接后的实例中拿到对应服务提供者提供的的dubbo协议集合，使用getChildren方法。

3，对拿到的集合遍历，记得要decodeURIComponent，然后对URL做解析，比如是http开头的dubbo协议，还是dubbo开头的协议，需要分别保存。

4，协议中是包含了RPC调用的所有dubbo方法的，此时需要把对应的方法进行保存，有的框架对方法做了属性拷贝，属性对应的方法内容就是一个invoke调用，有的则是对invoke做了API，你自己去传入RPC调用的方法名。

5，对不同协议编写不同的调用实现方法，如HTTP的调用使用node-json-rpc，而dubbo协议则使用hessian的库。

6，分析dubbo协议原理，简单概括就是基于hessian协议，传输前需要序列化，传输成功后使用hessian的反序列化方法拿到结果，传输使用socket方法连接。

7，序列化使用https://github.com/node-modules/hessian.js 自带js-to-java功能，但是这里需要注意，拿到成功返回结果后，如果decode失败，一定要检查java服务方法返回的最好是List，Map，Array这种常规JAVA数据类型，否则可能反序列化失败解析不出js对象。

8，完成调用，缓存实例，对Monitor做通知，详细格式参见dubbo Monitor源码：https://github.com/alibaba/dubbo/blob/master/dubbo-monitor/dubbo-monitor-api/src/main/java/com/alibaba/dubbo/monitor/MonitorService.java#L68


## Resource
[使用PHP和Node.js连接dubbo服务](https://zhuanlan.zhihu.com/p/26581501)


