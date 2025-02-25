---
title: "Apache Dubbo 多语言体系再添新员：首个 Rust 语言版本正式发布"
linkTitle: "Dubbo 发布首个 Rust 语言版本实现"
tags: ["Rust", "Release Notes"]
date: 2022-10-23
description: > 
    本文介绍了 Dubbo Rust 首个正式版本 v0.2.0 的核心功能和使用方式，Dubbo Rust 社区未来规划和参与方式。
---

Dubbo Rust 定位为 Dubbo 多语言体系的重要实现，提供高性能、易用、可扩展的 RPC 框架，同时通过接入 Dubbo Mesh 体系提供丰富的服务治理能力。本文主要为大家介绍 Dubbo Rust 项目基本情况，通过一个示例快速体验 Rust 首个正式版本特性，并给出了 Dubbo Rust 社区的近期规划，适合于关注或正在采用 Rust 语言的开发者与企业用户阅读。
## 1 Dubbo Rust 简介
Dubbo 作为 Apache 基金会最活跃的明星项目之一，同时也是国内最受欢迎的开源微服务框架，在易用性、高性能通信、服务治理等方面有着非常大的优势，通过 Dubbo3、Dubbo Mesh 等提供了云原生友好的开发与部署模式。与此同时，Dubbo 的多语言体系也得到了快速发展，长期以来提供的有 Java、Golang 两种语言实现，Rust、Node、Python、C++ 等语言实现的支持也已在社区正式启动。

- Dubbo 官网 [https://dubbo.apache.org/](https://dubbo.apache.org/)
- Dubbo Java https://github.com/apache/dubbo/
- Dubbo Golang https://github.com/apache/dubbo-go/
- **Dubbo Rust https://github.com/apache/dubbo-rust/**

Dubbo Rust 目标是对齐 Dubbo3 的所有核心功能设计，包括基于 HTTP/2 的高性能通信、用户友好的微服务开发编程模式、通过接入DubboMesh提供丰富的服务治理能力等，相比于其他语言实现，Dubbo Rust 将很好的利用 Rust 语言极致性能、安全和指令级掌控能力的特点。
对于微服务框架，主流的编程语言都有对应的实现，而 Dubbo Rust 将很好的填补 Rust 领域的空白：

-  Golang：在微服务框架领域已经占据着很重要的地位；开源社区出现了dubbo-go、gRPC、go-micro、go-zero等多个微服务框架
- Java：国内用户量最大的编程语言，Spring Cloud、Dubbo等优秀的微服务框架已经非常流行
- C/C++：brpc、grpc 等微服务框架
-  Rust：目前没有很完善的微服务框架

依托 Dubbo 庞大的用户群，以及 Dubbo 体系下的 Mesh 服务治理整体方案规划。Dubbo Rust 可以轻松地融入到现有的云原生研发体系中，不会增加使用者的研发负担。下图是社区推出的 Dubbo Mesh 架构设计。

![dubbo-rust](/imgs/rust/dubbo-rust-mesh.png)

在上述架构下，整体分为控制面和数据面两个部分，其中，

- 控制面负责管理流量治理、地址发现、安全认证、可观测性等服务治理相关的配置信管控工作，包括与K8S等底层技术设施的对接；
- Dubbo Rust 作为数据面组件，负责接收来自控制面的配置；将配置应用到服务中；同时为服务提供基础的RPC通信能力。

在架构设计方面，Dubbo Rust 将围绕 Dubbo 核心设计以及 Rust 语言的特性进行设计，并将 Dubbo 框架的核心设计输出为文档，从而提升Dubbo框架的易用性。因此，Dubbo Rust 具有如下特点：易用性、高性能以及可扩展，同时面向云原生提供丰富的服务治理能力。
## 2 快速体验 Dubbo Rust
### 2.1 首个版本核心能力
**Dubbo Rust 首个正式版本为 v0.2.0**，v0.2.0 提供的能力包括

- 基于 HTTP/2 的 Triple 协议的基础通信能力
- 基于 IDL 的 RPC 定义支持，Protobuf 来生成代码，同时支持 Serde 序列化
- request-response、request/response streaming、bi-streaming  通信模型支持
- 设计了简洁的、可扩展的架构，支持对 Listener、Connector、Filter、Protocol以及Invoker组件进行扩展

Dubbo Rust v0.2.0 的核心组件及通信流程如下图所示

![dubbo-rust](/imgs/rust/dubbo-rust-module.png)

核心架构已经基本完成，接下来的版本将重点关注核心组件的扩展以及服务治理相关组件的设计实现。
### 2.2 Quick Start
> 完整示例可查看 【Dubbo官网】 -> 【Rust SDK 文档】。
 [https://dubbo.apache.org/zh-cn/docs3-v2/rust-sdk/quick-start/](/zh-cn/docs3-v2/rust-sdk/quick-start/)

使用 Dubbo Rust 服务开发的基本步骤为

1. 使用 IDL 定义服务
2. 添加 Dubbo Rust 依赖到项目
3. 编译 IDL
4. 基于 IDL 编译生成的 stub 编写 Server & Client 逻辑
5. 运行项目

1. 使用 IDL 定义 Dubb 服务
```protobuf
```protobuf
// ./proto/greeter.proto
syntax = "proto3";

option java_multiple_files = true;

package org.apache.dubbo.sample.tri;


// The request message containing the user's name.
message GreeterRequest {
string name = 1;
}

// The response message containing the greetings
message GreeterReply {
string message = 1;
}

service Greeter{
// unary
rpc greet(GreeterRequest) returns (GreeterReply);
}
```
```
 

2. 增加 Dubbo Rust 依赖
```toml
```toml
# ./Cargo.toml
[package]
name = "example-greeter"
version = "0.1.0"
edition = "2021"

[dependencies]
dubbo = "0.1.0"
dubbo-config = "0.1.0"

[build-dependencies]
dubbo-build = "0.1.0"
```
```
 3. 编译 IDL 并根据生成的 stub 编写逻辑
编写 Dubbo Server
```rust
#[tokio::main]
async fn main() {
    register_server(GreeterServerImpl {
        name: "greeter".to_string(),
    });

    // Dubbo::new().start().await;
    Dubbo::new()
        .with_config({
            let r = RootConfig::new();
            match r.load() {
                Ok(config) => config,
                Err(_err) => panic!("err: {:?}", _err), // response was droped
            }
        })
        .start()
        .await;
}

struct GreeterServerImpl {
    name: String,
}

impl Greeter for GreeterServerImpl {
    async fn greet(
        &self,
        request: Request<GreeterRequest>,
    ) -> Result<Response<GreeterReply>, dubbo::status::Status> {
        println!("GreeterServer::greet {:?}", request.metadata);

        Ok(Response::new(GreeterReply {
            message: "hello, dubbo-rust".to_string(),
        }))
    }
}
```
 
编写 Dubbo Client
```rust
#[tokio::main]
async fn main() {
    let mut cli = GreeterClient::new().with_uri("http://127.0.0.1:8888".to_string());

    println!("# unary call");
    let resp = cli
        .greet(Request::new(GreeterRequest {
            name: "message from client".to_string(),
        }))
        .await;
    let resp = match resp {
        Ok(resp) => resp,
        Err(err) => return println!("{:?}", err),
    };
    let (_parts, body) = resp.into_parts();
    println!("Response: {:?}", body);
}
```

这样，一个简单的 Dubbo Rust 示例就开发完成了，可以到 Dubbo 官网查看完整文档。
## 3 Roadmap 与未来规划
Dubbo Rust Roadmap 规划分为三个阶段：

- 首先，提供作为 RPC 框架的基础能力，此阶段重点完成的包括基于 HTTP/2 的 RPC 通信、基于 IDL 的 RPC 定义、其他必要的 RPC 内核组件等
- 其实，是完善 Dubbo Rust 作为微服务框架的高级功能，此阶段包括微服务定义、配置、功能设计等，如服务超时、异步调用、上下文传递等，具体可参见 Dubbo Java 的高级特性。
- 第三阶段重点是引入丰富的服务治理能力支持，如流量治理、限流降级、可观测性等，这一目标将主要通过融入 Dubbo Mesh 体系，即适配 Dubbo Mesh 控制面实现。

其中，第一阶段的工作已经基本完成，大家可通过上文的 Quick Start 进行深入体验，第二、第三阶段的工作已经在社区全面开展，欢迎感兴趣的社区开发者参与进来，具体联系方式参见下文。

下图是侧重从第一阶段（RPC框架）、第二阶段（微服务开发框架）的视角对当前 Dubbo Rust 功能完备性的评估和任务拆解。

![dubbo-rust](/imgs/rust/dubbo-rust-tasks.png)

上图中都是 Dubbo Rust 核心设计的重要组件，保证 Dubbo Rust 具备微服务框架中完整的 RPC 通信能力以及服务治理能力。

- Protocol、Filter、Listener、Connector 等组件都是 RPC 通信核心能力
- 服务注册发现、负载均衡、Cluster、Metadata 为后续服务治理能力做铺垫

除了上图列出的模块以外，还有一些非功能需求也需要支持，例如：

- Dubbo 多语言框架之间相互通信测试
- 性能验证与持续的 benchmark 机制
- 整体架构的持续优化，如核心配置简化以及相应的文档完善
## 4 参与 Dubbo Rust 社区
和 Rust 语言一样，Dubbo Rust 是一个非常有活力、非常前沿的社区，另一方面，依赖 Apache Dubbo 社区背后庞大的开发者群体和企业用户，Dubbo Rust 有着非常深厚的用户基础和发展潜力。Dubbo Rust 的快速发展期待社区贡献者的加入
参与 Dubbo Rust 社区可以收获

- 见证 Dubbo Rust 开源项目的建设以及发展
- 在大型项目中通过实际使用学习 Rust 语言，加深对 Rust 语言的理解
- 获得提名为 Apache Dubbo CommitterPMC
- 借助 Dubbo 社区提高个人曝光度，提高个人技术影响力
- 与阿里巴巴等企业专家的面对面交流机会，快速提高技术视野

参与 Dubbo Rust 社区的方式有如下几种

- 搜索并加入钉钉群并参与社区双周会，钉钉群号 **44694199**
- 到 GitHub 提 Issue 或贡献代码 https://github.com/apache/dubbo-rust


