---
title: "微服务（一）"
date: 2025-06-09
categories:
  - "engineering"
tags:
  - "technique"
  - "platform"
  - "微服务"
  - "云原生"
---

## 概述

### 什么是微服务？

微服务是一种软件架构风格，相较于单体架构（整体式架构），应用被拆分为多个小型、独立服务，每个服务围绕特定业务功能构建，独立开发、部署、扩展和维护，服务间通过轻量级的通信机制协作，来组合完成完整应用。微服务和微服务架构是一个意思。每个微服务由各个小型独立团队负责。

### 为什么需要微服务？

微服务架构从单体架构（整体式架构）演进而来，经历了从单体架构到面向服务（SOA）架构，再到微服务架构的变化。微服务架构使应用程序更易于扩展和更快地开发，从而加速创新并缩短新功能的上市时间。

微服务架构具有自治和专用的特点。自治加快了应用的发布速度，让开发变得敏捷、功能扩展更灵活、工具选择更自由、部署更轻松。专用降低了应用功能的耦合性，提高了代码模块的重用，也缩小了故障影响提升了可用性，让应用更具弹性和可伸缩性。

### 微服务和云原生是什么关系？

微服务架构是云原生架构的基石，除了微服务之外，云原生的关键技术还包括容器、Serverless和DevOps。微服务架构的核心目的是通过模块化来提升应用研发的效率，而云原生的核心目的是最大化地利用云平台的特性来实现高效的资源利用、快速迭代和自动化运维，两者相辅相成。

### 构建微服务有哪些典型的服务组件？

典型的微服务组件可以从七个角度来看：

1. 业务核心组件：服务实例、服务接口

3. 通信组件：API网关、RPC通信（同步）、消息队列（异步）、定时任务（异步）、注册中心

5. 数据管理组件：独立数据库、分布式缓存、时间流处理

7. 运维监控组件：容器和编排、配置中心、日志与监控

9. 安全组件：身份认证与授权、服务网格

11. 弹性与容错组件：域名解析、负载均衡、限流器、熔断器、重试与降级

13. 开发与交付组件：持续交付、基础设施即代码

## 参考阅读

1. [什么是微服务？](https://aws.amazon.com/cn/microservices/)AWS

3. [什么是微服务？](https://www.aliyun.com/getting-started/what-is/what-is-microservice)阿里云

5. [微服务是什么？](https://www.ruanyifeng.com/blog/2022/04/microservice.html)阮一峰

7. [什么是云原生？](https://www.amazonaws.cn/knowledge/what-is-cloud-native/)AWS

9. [云原生架构](https://www.amazonaws.cn/knowledge/what-is-cloud-native/) AWS

11. [什么是云原生？](https://www.aliyun.com/getting-started/what-is/what-is-cloud-native#3749b362e7doq)阿里云

13. [什么是云原生？](https://cloud.google.com/learn/what-is-cloud-native?hl=zh-CN)Google Cloud
