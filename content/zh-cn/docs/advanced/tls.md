---
aliases:
    - /zh/docs/advanced/tls/
description: 通过 TLS 保证传输安全
linkTitle: TLS
title: TLS
type: docs
weight: 32
---



{{% pageinfo %}} 此文档已经不再维护。您当前查看的是快照版本。如果想要查看最新版本的文档，请参阅[最新版本](/zh-cn/docs3-v2/java-sdk/advanced-features-and-usage/security/tls/)。
{{% /pageinfo %}}

2.7.5 版本在传输链路的安全性上做了很多工作，对于内置的 Dubbo Netty Server 和新引入的 gRPC 协议都提供了基于 TLS 的安全链路传输机制。

TLS 的配置都有统一的入口，如下所示：

##### Provider 端

```java
SslConfig sslConfig = new SslConfig();
sslConfig.setServerKeyCertChainPath("path to cert");
sslConfig.setServerPrivateKeyPath(args[1]);
// 如果开启双向 cert 认证
if (mutualTls) {
  sslConfig.setServerTrustCertCollectionPath(args[2]);
}

ProtocolConfig protocolConfig = new ProtocolConfig("dubbo/grpc");
protocolConfig.setSslEnabled(true);
```



##### Consumer 端

```java
if (!mutualTls) {}
    sslConfig.setClientTrustCertCollectionPath(args[0]);
} else {
    sslConfig.setClientTrustCertCollectionPath(args[0]);
    sslConfig.setClientKeyCertChainPath(args[1]);
    sslConfig.setClientPrivateKeyPath(args[2]);
}
```

为尽可能保证应用启动的灵活性，TLS Cert 的指定还能通过 -D 参数或环境变量等方式来在启动阶段根据部署环境动态指定，具体请参见 Dubbo 配置读取规则与 TLS 示例

{{% alert title="提示" color="primary" %}}
参考 Dubbo [配置读取规则](/zh-cn/docsv2.7/user/configuration/configuration-load-process/)，TLS [示例](https://github.com/apache/dubbo-samples/tree/master/4-governance/dubbo-samples-ssl)

如果要使用的是 gRPC 协议，在开启 TLS 时会使用到协议协商机制，因此必须使用支持 ALPN 机制的 Provider，推荐使用的是 netty-tcnative，具体可参见 gRPC Java 社区的[总结]( https://github.com/grpc/grpc-java/blob/master/SECURITY.md)
{{% /alert %}}


在服务调用的安全性上，Dubbo 在后续的版本中会持续投入，其中服务发现/调用的鉴权机制预计在接下来的版本中就会和大家见面。