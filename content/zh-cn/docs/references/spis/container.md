---
aliases:
    - /zh/docs/references/spis/container/
description: 容器扩展
linkTitle: 容器扩展
title: 容器扩展
type: docs
weight: 22
---



{{% pageinfo %}} 此文档已经不再维护。您当前查看的是快照版本。如果想要查看最新版本的文档，请参阅[最新版本](/zh-cn/docs3-v2/java-sdk/reference-manual/spi/description/container/)。
{{% /pageinfo %}}

## 扩展说明

服务容器扩展，用于自定义加载内容。

## 扩展接口

`org.apache.dubbo.container.Container`

## 扩展配置

```sh
java org.apache.dubbo.container.Main spring jetty log4j
```

## 已知扩展

* `org.apache.dubbo.container.spring.SpringContainer`
* `org.apache.dubbo.container.spring.JettyContainer`
* `org.apache.dubbo.container.spring.Log4jContainer`

## 扩展示例

Maven 项目结构：

```
src
 |-main
    |-java
        |-com
            |-xxx
                |-XxxContainer.java (实现Container接口)
    |-resources
        |-META-INF
            |-dubbo
                |-org.apache.dubbo.container.Container (纯文本文件，内容为：xxx=com.xxx.XxxContainer)
```

XxxContainer.java：

```java
package com.xxx;
 
org.apache.dubbo.container.Container;
 
 
public class XxxContainer implements Container {
    public Status start() {
        // ...
    }
    public Status stop() {
        // ...
    }
}
```

META-INF/dubbo/org.apache.dubbo.container.Container：

```properties
xxx=com.xxx.XxxContainer
```