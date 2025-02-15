---
aliases:
    - /zh/docs/advanced/stickiness/
description: 为有状态服务配置粘滞连接
linkTitle: 粘滞连接
title: 粘滞连接
type: docs
weight: 31
---



{{% pageinfo %}} 此文档已经不再维护。您当前查看的是快照版本。如果想要查看最新版本的文档，请参阅[最新版本](/zh-cn/docs3-v2/java-sdk/advanced-features-and-usage/performance/stickiness/)。
{{% /pageinfo %}}

粘滞连接用于有状态服务，尽可能让客户端总是向同一提供者发起调用，除非该提供者挂了，再连另一台。

粘滞连接将自动开启[延迟连接](../lazy-connect)，以减少长连接数。

```xml
<dubbo:reference id="xxxService" interface="com.xxx.XxxService" sticky="true" />
```

Dubbo 支持方法级别的粘滞连接，如果你想进行更细粒度的控制，还可以这样配置。

```xml
<dubbo:reference id="xxxService" interface="com.xxx.XxxService">
    <dubbo:method name="sayHello" sticky="true" />
</dubbo:reference>
```