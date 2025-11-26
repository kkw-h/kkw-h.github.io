---
title: VPS 的购买以及代理搭建
date: 2025-11-26
tags:
  - VPS
  - Linux
  - 代理
---

## 简介

本文将介绍如何购买 VPS（Virtual Private Server）以及如何在 VPS 上搭建代理服务。

## 1. 购买 VPS

购买 VPS 如果是用在搭建代理的情况下最需要注意的就是选择一个优质的 IP 及线路。

### IP的重要性

IP 的选择最重要的就是查看这个 IP 在访问网站时受到的风控强度。这会直接影响到代理的使用体验。有些会出现频繁验证是否是机器人，有些则会出现直接禁止访问的情况。

### IP的选取

#### 常见的IP类型

- 住宅 IP：固定路线互联网服务商。例如：中国电信、中国联通、中国移动等。
- 数据中心 IP： 云服务商机房分配的 IP。例如：阿里云、腾讯云、AWS、DigitalOcean 等。
- 移动 IP：手机网络运营商分配的 IP。
- 商业 IP：商业用途，企业网络。
- 教育 IP：教育结构的 IP，一般认为是受信任的，在申请教育优惠时有奇效。
一般而言， 住宅 IP≈移动 IP>商业 IP>数据中心 IP

#### 原生/广播

- 原生 IP：注册国家与 IP 所在实际地理位置一致。
- 广播 IP：注册国家与 IP 所在实际地理位置不一致。

#### 流媒体解锁

- 流媒体解锁：常见的为 Netflix、Disney+、Hulu 等。
- 网络解锁：常见的为 Google、Facebook 等。
这些会根据 IP 地区进行内容的限制

#### 送中

有些 IP 会被服务商标记为CN 地区，这会限制使用一部分功能

### IP检测（只有IP地址）

在购买 VPS 之前一般平台会提供一个测试 IP。
首先我们需要知道 IP 的基础信息和地理位置。

#### [ip2location](https://www.ip2location.com/demo)

这是用来检查IP 的地理位置和基础信息的网站。
我们需要关注的是Region+Usage Type+AS Usage Type+ASN来确定基本情况
- Region：IP 所在的地理位置
- Usage Type：IP 的使用类型
- AS Usage Type：AS 的使用类型
- ASN：AS 的编号

#### [ipqs](https://www.ipqualityscore.com/user/search)

用来检查 IP 是否有滥用的情况

### IP检查（需要VPS）

#### [IP质量体检脚本](https://github.com/xykt/IPQuality)

用来检查 IP 的质量


## 2. 搭建代理

### 服务端

这里使用 3x-ui 作为代理软件。
[3x-ui](https://github.com/MHSanaei/3x-ui)
面板访问的时候最好是在已经申请了 HTTPS 的情况下进行，这样可以避免一些问题。
推荐 vless-Reality 方式

### 客户端

- macOS [clash-party](https://github.com/mihomo-party-org/clash-party)
- windows [clash-party](https://github.com/mihomo-party-org/clash-party)
- iOS [Loon](https://nsloon.app/docs/intro/)
- Android [FlClash](https://github.com/chen08209/FlClash)
- 鸿蒙 [ClashBox](https://github.com/xiaobaigroup/ClashBox)