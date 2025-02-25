---
aliases:
    - /zh/docs3-v2/java-sdk/reference-manual/spi/description/logger-adapter/
description: 日志适配扩展
linkTitle: 日志适配扩展
title: 日志适配扩展
type: docs
weight: 26
---



## 扩展说明

日志输出适配扩展点。

## 扩展接口

`org.apache.dubbo.common.logger.LoggerAdapter`

## 扩展配置

```xml
<dubbo:application logger="xxx" />
```

或者：

```sh
-Ddubbo:application.logger=xxx
```

## 已知扩展

* `org.apache.dubbo.common.logger.slf4j.Slf4jLoggerAdapter`
* `org.apache.dubbo.common.logger.jcl.JclLoggerAdapter`
* `org.apache.dubbo.common.logger.log4j.Log4jLoggerAdapter`
* `org.apache.dubbo.common.logger.log4j2.Log4j2LoggerAdapter`
* `org.apache.dubbo.common.logger.jdk.JdkLoggerAdapter`

## 扩展示例

Maven 项目结构：

```
src
 |-main
    |-java
        |-com
            |-xxx
                |-XxxLoggerAdapter.java (实现LoggerAdapter接口)
    |-resources
        |-META-INF
            |-dubbo
                |-org.apache.dubbo.common.logger.LoggerAdapter (纯文本文件，内容为：xxx=com.xxx.XxxLoggerAdapter)
```

XxxLoggerAdapter.java：

```java
package com.xxx;
 
import org.apache.dubbo.common.logger.LoggerAdapter;
 
public class XxxLoggerAdapter implements LoggerAdapter {
    public Logger getLogger(URL url) {
        // ...
    }
}
```

XxxLogger.java：

```java
package com.xxx;
 
import org.apache.dubbo.common.logger.Logger;
 
public class XxxLogger implements Logger {
    public XxxLogger(URL url) {
        // ...
    }
    public void info(String msg) {
        // ...
    }
    // ...
}
```

META-INF/dubbo/org.apache.dubbo.common.logger.LoggerAdapter：

```properties
xxx=com.xxx.XxxLoggerAdapter
```