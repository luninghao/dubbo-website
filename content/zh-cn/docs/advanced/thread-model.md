---
aliases:
    - /zh/docs/advanced/thread-model/
description: 配置 Dubbo 中的线程模型
linkTitle: 线程模型
title: 线程模型
type: docs
weight: 4
---



{{% pageinfo %}} 此文档已经不再维护。您当前查看的是快照版本。如果想要查看最新版本的文档，请参阅[最新版本](/zh-cn/docs3-v2/java-sdk/advanced-features-and-usage/performance/threading-model/)。
{{% /pageinfo %}}

如果事件处理的逻辑能迅速完成，并且不会发起新的 IO 请求，比如只是在内存中记个标识，则直接在 IO 线程上处理更快，因为减少了线程池调度。  

但如果事件处理逻辑较慢，或者需要发起新的 IO 请求，比如需要查询数据库，则必须派发到线程池，否则 IO 线程阻塞，将导致不能接收其它请求。  

如果用 IO 线程处理事件，又在事件处理过程中发起新的 IO 请求，比如在连接事件中发起登录请求，会报“可能引发死锁”异常，但不会真死锁。

![dubbo-protocol](/imgs/user/dubbo-protocol.jpg)


因此，需要通过不同的派发策略和不同的线程池配置的组合来应对不同的场景:

```xml
<dubbo:protocol name="dubbo" dispatcher="all" threadpool="fixed" threads="100" />
```

Dispatcher

* `all` 所有消息都派发到线程池，包括请求，响应，连接事件，断开事件，心跳等。
* `direct` 所有消息都不派发到线程池，全部在 IO 线程上直接执行。
* `message` 只有请求响应消息派发到线程池，其它连接断开事件，心跳等消息，直接在 IO 线程上执行。
* `execution` 只有请求消息派发到线程池，不含响应，响应和其它连接断开事件，心跳等消息，直接在 IO 线程上执行。
* `connection` 在 IO 线程上，将连接断开事件放入队列，有序逐个执行，其它消息派发到线程池。

ThreadPool

* `fixed` 固定大小线程池，启动时建立线程，不关闭，一直持有。(缺省)
* `cached` 缓存线程池，空闲一分钟自动删除，需要时重建。
* `limited` 可伸缩线程池，但池中的线程数只会增长不会收缩。只增长不收缩的目的是为了避免收缩时突然来了大流量引起的性能问题。
* `eager` 优先创建`Worker`线程池。在任务数量大于`corePoolSize`但是小于`maximumPoolSize`时，优先创建`Worker`来处理任务。当任务数量大于`maximumPoolSize`时，将任务放入阻塞队列中。阻塞队列充满时抛出`RejectedExecutionException`。(相比于`cached`:`cached`在任务数量超过`maximumPoolSize`时直接抛出异常而不是将任务放入阻塞队列)