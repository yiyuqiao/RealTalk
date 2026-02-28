# RealTalk

基于 Go 构建的分布式实时通讯平台，支持 WebSocket 即时消息与 WebRTC 音视频通话。

## 1. 项目简介

RealTalk 是一个基于 Gin + Kafka + Redis + WebRTC 构建的分布式实时通讯系统，支持：

单聊 / 群聊

WebSocket 实时消息

WebRTC 音视频通话

后台管理系统

TLS/SSL 安全通信

高并发异步消息架构

本项目旨在模拟企业级即时通讯系统的核心架构设计，重点解决：

高并发消息削峰

消息可靠传输

NAT 穿透下的音视频通信

服务解耦与扩展性设计

## 2. 系统架构
整体架构
```bash
Client
   │
   │ WebSocket / HTTPS
   ▼
Gin HTTP Server
   │
   ├── Kafka（消息解耦）
   ├── Redis（缓存会话 / 在线状态）
   ├── MySQL（持久化）
   └── WebRTC（音视频直连）
```


### 架构设计思想

Kafka：将消息发送、存储、推送完全解耦

Redis：缓存在线状态与热点会话，减少数据库压力

MySQL：保证数据持久化与一致性

WebRTC + 信令服务器：实现 NAT 穿透与音视频协商

系统支持每秒 1000+ 消息削峰处理能力（本地压测环境）。

## 3. 技术栈
技术	作用
Gin	HTTP 服务框架
WebSocket	实时消息推送
WebRTC	音视频通信
Kafka	消息异步处理
Redis	在线状态缓存
MySQL	数据持久化
TLS/SSL	安全通信
Gorm	ORM 框架

## 4. 核心功能
4.1 实时聊天系统

        支持单聊 / 群聊
        
        支持消息持久化
        
        支持离线消息存储
        
        在线用户状态维护
        
        消息流程
        
        用户通过 WebSocket 发送消息
        
        服务端写入 Kafka
        
        消费者：
        
        写入 MySQL
        
        推送给目标用户
        
        更新 Redis 状态
        
        实现消息异步处理链路，避免同步写库导致阻塞。

4.2 WebRTC 音视频通话

        实现内容：
        
        SDP 协商
        
        ICE 候选交换
        
        NAT 穿透协调
        
        通话状态管理
        
        信令通过 WebSocket 传递，音视频流点对点传输。
        
        支持：
        
        呼叫
        
        接听
        
        挂断
        
        通话状态同步

4.3 高并发设计
        Kafka 削峰
        
        将消息处理拆分为：
        
        发送
        
        持久化
        
        推送
        
        避免同步阻塞
        
        Redis 缓存策略
        
        缓存内容：
        
        用户在线状态
        
        用户会话列表
        
        最近联系人
        
        热点群聊数据
        
        降低 MySQL 直接访问压力。

4.4 安全机制

        HTTPS + TLS 加密通信
        
        用户身份认证机制
        
        AES 对部分敏感数据加密
        
        Token 认证机制

## 5. 项目目录结构
```bash
RealTalk
├─api                # 接口定义
├─cmd                # 服务启动入口
├─configs            # 配置文件
├─internal           # 核心业务逻辑
│   ├─dao            # 数据访问层
│   ├─service        # 业务服务层
│   │   ├─chat
│   │   ├─kafka
│   │   ├─redis
│   │   └─aes
│   ├─model
│   └─https_server
├─pkg                # 公共组件
│   ├─enum
│   ├─constants
│   ├─ssl
│   └─zlog
├─web                # 前端代码
└─test               # 单元测试
```
采用典型 Go 分层结构：

handler → service → dao

internal 封装核心逻辑

pkg 提供公共组件

## 6. 部署方式
6.1 环境依赖

Go 1.20+

MySQL 8+

Redis

Kafka

Node.js（前端）

6.2 启动步骤
【1】 修改配置文件
```bash
configs/config.yaml
```
配置：
```bash
MySQL
Redis
Kafka
TLS 证书路径
```
【2】 启动后端
```bash
go run cmd/kama_chat_server/main.go
```
【3】 启动前端
```bash
cd web/chat-server
npm install
```
npm run dev
