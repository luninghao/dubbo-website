---
aliases:
    - /zh/docs/advanced/echo-service/
description: 通过回声测试检测 Dubbo 服务是否可用
linkTitle: 回声测试
title: 回声测试
type: docs
weight: 18
---



{{% pageinfo %}} 此文档已经不再维护。您当前查看的是快照版本。如果想要查看最新版本的文档，请参阅[最新版本](/zh-cn/docs3-v2/java-sdk/advanced-features-and-usage/service/echo-service/)。
{{% /pageinfo %}}

回声测试用于检测服务是否可用，回声测试按照正常请求流程执行，能够测试整个调用是否通畅，可用于监控。

所有服务自动实现 `EchoService` 接口，只需将任意服务引用强制转型为 `EchoService`，即可使用。

Spring 配置：
```xml
<dubbo:reference id="memberService" interface="com.xxx.MemberService" />
```

代码：
```java
// 远程服务引用
MemberService memberService = ctx.getBean("memberService"); 
 
EchoService echoService = (EchoService) memberService; // 强制转型为EchoService

// 回声测试可用性
String status = echoService.$echo("OK"); 
 
assert(status.equals("OK"));
```