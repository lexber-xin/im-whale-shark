# WhaleShark
WhaleShark（鲸鲨）是一个用 Java 实现的高性能分布式 IM 系统，它支持对接用户自定义多端登录方式，目前多端登录方式有单端、双端、三端以及无限制
<br>

## 技术栈
使用到 Netty、Redis、Redisson、RabbitMQ、Zookeeper 等主流技术栈
<br>
+ Netty 实现高性能消息收发，应用层握手(用户登录登出)，心跳检测(挂后台)
+ Redis 和 Redisson 客户端实现用户 Session 信息的存储、发布订阅模式实现路由层信息缓存
+ RabbitMQ 解耦对接 TCP 网关服务和逻辑层交互、保证分布式下消息顺序性
+ Zookeeper 注册中心及时感知服务节点上线下线情况

## 模块职责介绍
采用 DDD 架构思想搭建各个模块层级，并使用大量设计模式优化架构，使项目可扩展、易阅读
<br>
项目树如下
```text
im-systam-whale-shark
├─ im-codec    接入层：负责网关服务配置文件集成、私有协议包结构定义以及使用私有协议对消息编解码
├─ im-common   基础层：负责定义整个 IM 架构所有常量、状态码、错误码、基础数据模型
├─ im-domain   领域层：负责定义用户、好友、群组等多个领域的逻辑，以及消息的发送服务
├─ im-service  服务层：负责定义底层组件如 redis、zk、mq 的配置逻辑，回调机制和基类消息发送
├─ im-tcp      网关层：负责定义心跳机制、监控用户消息读取以及上线下线、Netty 消息通道以及 WebSocket 全双工通道
└─ pom.xml
```

## 亮点
1. 使用策略模式重构用户操作指令逻辑
2. 使用状态模式重构用户自定义多端登录方式
3. 使用模板模式重构消息接收器(群聊、单聊的消息接收器逻辑十分相似)
4. 使用 Redis 缓存用户信息的方式模拟路由层，实现跨服务之间的多 Channel 通讯
5. 使用 Redisson 发布订阅模式，监听用户登录行为，发送用户下线通知
6. 使用 Rabbitmq 处理分布式消息顺序性问题，并且解决线上 MQ 消息积压和消息不一致等问题
