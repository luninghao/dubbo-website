---
aliases:
    - /zh/docs/references/telnet/
description: 通过 Telnet 命令进行服务治理
linkTitle: Telnet 手册
title: Telnet 命令参考手册
type: docs
weight: 11
---



{{% pageinfo %}} 此文档已经不再维护。您当前查看的是快照版本。如果想要查看最新版本的文档，请参阅[最新版本](/zh-cn/docs3-v2/java-sdk/reference-manual/qos/)。
{{% /pageinfo %}}

从 `2.0.5` 版本开始，dubbo 开始支持通过 telnet 命令来进行服务治理。

## 使用

```sh
telnet localhost 20880
```

或者：

```sh
echo status | nc -i 1 localhost 20880
```


status命令所检查的资源也可以扩展，参见：[扩展参考手册](../../references/spis/status-checker)。

## 命令

以下展示了 dubbo 内建的 telnet 命令的说明和用法，此外，telnet 命令还支持用户自行扩展，参见：[Telnet 命令扩展](../../references/spis/telnet-handler)。

### `ls`

0. `ls`: 显示服务列表
0. `ls -l`: 显示服务详细信息列表
0. `ls XxxService`: 显示服务的方法列表
0. `ls -l XxxService`: 显示服务的方法详细信息列表


### `ps`

0. `ps`: 显示服务端口列表
0. `ps -l`: 显示服务地址列表
0. `ps 20880`: 显示端口上的连接信息
0. `ps -l 20880`: 显示端口上的连接详细信息


### `cd`

0. `cd XxxService`: 改变缺省服务，当设置了缺省服务，凡是需要输入服务名作为参数的命令，都可以省略服务参数
0. `cd /`: 取消缺省服务

### `pwd`

`pwd`: 显示当前缺省服务

### `trace`

0. `trace XxxService`: 跟踪 1 次服务任意方法的调用情况
0. `trace XxxService 10`: 跟踪 10 次服务任意方法的调用情况
0. `trace XxxService xxxMethod`: 跟踪 1 次服务方法的调用情况
0. `trace XxxService xxxMethod 10`: 跟踪 10 次服务方法的调用情况

### `count`

0. `count XxxService`: 统计 1 次服务任意方法的调用情况
0. `count XxxService 10`: 统计 10 次服务任意方法的调用情况
0. `count XxxService xxxMethod`: 统计 1 次服务方法的调用情况
0. `count XxxService xxxMethod 10`: 统计 10 次服务方法的调用情况

### `invoke`

0. `invoke XxxService.xxxMethod(1234, "abcd", {"prop" : "value"})`: 调用服务的方法
0. `invoke com.xxx.XxxService.XxxService.xxxMethod(1234, "abcd", {"prop" : "value"})`: 调用全路径服务的方法
0. `invoke xxxMethod(1234, "abcd", {"prop" : "value"})`: 调用服务的方法(自动查找包含此方法的服务)
0. `invoke xxxMethod({"name":"zhangsan","age":12,"class":"org.apache.dubbo.qos.legacy.service.Person"})` :当有参数重载，或者类型转换失败的时候，可以通过增加class属性指定需要转换类
0. 当参数为Map<Integer,T>，key的类型为Integer时，建议指定类型。例如`invoke com.xxx.xxxApiService({"3":0.123, "class":"java.util.HashMap"})`

### `select` [^2]

0. `select 1`: 当 invoke 命令匹配到多个方法时使用，根据提示列表选择需要调用的方法

### `status`

0. `status`: 显示汇总状态，该状态将汇总所有资源的状态，当全部 OK 时则显示 OK，只要有一个 ERROR 则显示 ERROR，只要有一个 WARN 则显示 WARN
0. `status -l`: 显示状态列表

### `log` [^1]

0. `log debug`: 修改 dubbo logger 的日志级别
0. `log 100`: 查看 file logger 的最后 100 字符的日志

### `help`

0. `help`: 显示 telnet 命帮助信息
0. `help xxx`: 显示xxx命令的详细帮助信息

### `clear`

0. `clear`: 清除屏幕上的内容
0. `clear 100`: 清除屏幕上的指定行数的内容

### `exit`

`exit`: 退出当前 telnet 命令行

### `shutdown` [^2]

0. `shutdown`: 关闭 dubbo 应用
0. `shutdown -t 1000`: 延迟 1000 毫秒关闭 dubbo 应用

[^1]: `2.0.6` 以上版本支持
[^2]: `2.7.1` 以上版本支持