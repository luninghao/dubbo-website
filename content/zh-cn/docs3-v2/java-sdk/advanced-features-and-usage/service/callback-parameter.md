---
aliases:
    - /zh/docs3-v2/java-sdk/advanced-features-and-usage/service/callback-parameter/
description: 通过参数回调从服务器端调用客户端逻辑
linkTitle: 服务端对客户端进行回调
title: 服务端对客户端进行回调
type: docs
weight: 9
---


## 特性说明
参数回调方式与调用本地 callback 或 listener 相同，只需要在 Spring 的配置文件中声明哪个参数是 callback 类型即可。Dubbo 将基于长连接生成反向代理，这样就可以从服务器端调用客户端逻辑。可以参考 [dubbo 项目中的示例代码](https://github.com/apache/dubbo-samples/tree/master/2-advanced/dubbo-samples-callback)。

## 使用场景
回调函数通知客户端执行结果，或发送通知，在方法执行时间比较长时，类似异步调用，审批工作流中回调客户端审批结果。

## 使用方式
### 服务接口示例

CallbackService.java
```java
package com.callback;
 
public interface CallbackService {
    void addListener(String key, CallbackListener listener);
}
```

CallbackListener.java
```java
package com.callback;
 
public interface CallbackListener {
    void changed(String msg);
}
```

### 服务提供者接口实现示例

```java
package com.callback.impl;
 
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
 
import com.callback.CallbackListener;
import com.callback.CallbackService;
 
public class CallbackServiceImpl implements CallbackService {
     
    private final Map<String, CallbackListener> listeners = new ConcurrentHashMap<String, CallbackListener>();
  
    public CallbackServiceImpl() {
        Thread t = new Thread(new Runnable() {
            public void run() {
                while(true) {
                    try {
                        for(Map.Entry<String, CallbackListener> entry : listeners.entrySet()){
                           try {
                               entry.getValue().changed(getChanged(entry.getKey()));
                           } catch (Throwable t) {
                               listeners.remove(entry.getKey());
                           }
                        }
                        Thread.sleep(5000); // 定时触发变更通知
                    } catch (Throwable t) { // 防御容错
                        t.printStackTrace();
                    }
                }
            }
        });
        t.setDaemon(true);
        t.start();
    }
  
    public void addListener(String key, CallbackListener listener) {
        listeners.put(key, listener);
        listener.changed(getChanged(key)); // 发送变更通知
    }
     
    private String getChanged(String key) {
        return "Changed: " + new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date());
    }
}
```

### 服务提供者配置示例

```xml
<bean id="callbackService" class="com.callback.impl.CallbackServiceImpl" />
<dubbo:service interface="com.callback.CallbackService" ref="callbackService" connections="1" callbacks="1000">
    <dubbo:method name="addListener">
        <dubbo:argument index="1" callback="true" />
        <!--也可以通过指定类型的方式-->
        <!--<dubbo:argument type="com.demo.CallbackListener" callback="true" />-->
    </dubbo:method>
</dubbo:service>
```

### 服务消费者配置示例

```xml
<dubbo:reference id="callbackService" interface="com.callback.CallbackService" />
```

### 服务消费者调用示例

```java
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("classpath:consumer.xml");
context.start();
 
CallbackService callbackService = (CallbackService) context.getBean("callbackService");
 
callbackService.addListener("foo.bar", new CallbackListener(){
    public void changed(String msg) {
        System.out.println("callback1:" + msg);
    }
});
```