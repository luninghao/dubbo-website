---
aliases:
    - /zh/docs/advanced/token-authorization/
description: 通过令牌验证在注册中心控制权限
linkTitle: 令牌验证
title: 令牌验证
type: docs
weight: 32
---



{{% pageinfo %}} 此文档已经不再维护。您当前查看的是快照版本。如果想要查看最新版本的文档，请参阅[最新版本](/zh-cn/docs3-v2/java-sdk/advanced-features-and-usage/security/token-authorization/)。
{{% /pageinfo %}}

通过令牌验证在注册中心控制权限，以决定要不要下发令牌给消费者，可以防止消费者绕过注册中心访问提供者，另外通过注册中心可灵活改变授权方式，而不需修改或升级提供者

![/user-guide/images/dubbo-token.jpg](/imgs/user/dubbo-token.jpg)

可以全局设置开启令牌验证：

```xml
<!--随机token令牌，使用UUID生成-->
<dubbo:provider token="true" />
```
或

```xml
<!--固定token令牌，相当于密码-->
<dubbo:provider token="123456" />
```

也可在服务级别设置：

```xml
<!--随机token令牌，使用UUID生成-->
<dubbo:service interface="com.foo.BarService" token="true" />
```
或

```xml
<!--固定token令牌，相当于密码-->
<dubbo:service interface="com.foo.BarService" token="123456" />
```