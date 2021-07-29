---
title: SpringCloudStream入门及案例
date: 2021-07-11 11:03:38
tags: [Spring, SpringCloudStream, rabbitMQ, 消息队列]
categories: [Spring, SpringCloudStream]
description: 在微服务项目中，我们需要通过消息队列来传递不同服务之间的数据和消息，而SpringCloudStream则可以为不同种类的消息队列提供一个统一的高层api使用入口
---

# 前言

在微服务架构流行的今天，消息队列的应用显得尤为重要，然而对于一个Java或者说一个Spring项目来说，不同的消息队列需要导入不同的driver依赖，这种情况下，就产生了一个痛点，即项目极有可能深度与某个特定的消息队列绑定，而通过本文所述的这项技术Spring Cloud Stream，可以将消息队列driver的实现与应用代码分离，即开发者不需要再写带有“mq特色”的代码了。

过去，我们可以通过Spring Integration来集成各种企业级别应用的外部系统，比如数据库、消息队列，随着Spring Boot的不断壮大和流行，官方将Spring Integration与Spring Boot整合到了一块，形成了一个新的项目--Spring Cloud Stream。Spring



