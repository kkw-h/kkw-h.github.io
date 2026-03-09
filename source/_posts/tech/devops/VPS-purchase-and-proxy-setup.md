---
title: VPS 的购买以及代理搭建
date: 2025-11-26
categories:
  - Technology
  - DevOps
tags:
  - VPS
  - Network
---

## 简介

本文将详细介绍如何选购适合搭建代理服务的 VPS（Virtual Private Server），以及如何进行 IP 质量检测和代理服务的搭建。

## 1. VPS 选购指南

在购买 VPS 用于搭建代理时，最核心的考量因素是 **IP 的质量** 及 **线路的稳定性**。

### 1.1 IP 的重要性

IP 的质量直接决定了代理的使用体验。优质的 IP 能有效避免被目标网站风控（如频繁的验证码、直接拒绝访问等）。

### 1.2 IP 类型详解

了解 IP 的分类有助于我们做出更好的选择：

#### 常见的 IP 类型

- **住宅 IP (Residential IP)**：由 ISP（互联网服务提供商）分配给家庭用户的 IP。
  - *特点*：信任度最高，最不容易被风控。
  - *例子*：中国电信、Comcast、AT&T。
- **移动 IP (Mobile IP)**：由移动网络运营商分配给移动设备的 IP。
  - *特点*：信任度极高，通常用于 4G/5G 网络。
- **教育 IP (Education IP)**：分配给教育机构（大学、学院）的 IP。
  - *特点*：信任度较高，常用于申请教育优惠。
- **商业 IP (Business IP)**：分配给企业用户的 IP。
  - *特点*：信任度中等。
- **数据中心 IP (Data Center IP)**：云服务商机房分配的 IP。
  - *特点*：获取成本低，但信任度最低，最容易被风控。
  - *例子*：AWS, Google Cloud, DigitalOcean, Vultr。

**信任度排序**：住宅 IP ≈ 移动 IP > 教育 IP > 商业 IP > 数据中心 IP

#### 原生 IP vs 广播 IP

- **原生 IP (Native IP)**：IP 的注册国家/地区与实际物理位置一致。
  - *优势*：解锁流媒体和本地服务的成功率更高。
- **广播 IP (Broadcast IP)**：IP 的注册国家/地区与实际物理位置不一致（通过 BGP 广播实现）。
  - *劣势*：可能导致部分服务无法正确识别地区。

### 1.3 常见限制与解锁

- **流媒体解锁**：能否观看 Netflix, Disney+, Hulu, HBO 等流媒体内容。
- **网络解锁**：能否访问 Google, Facebook, ChatGPT 等服务。
- **送中 (CN Blocking)**：部分 IP 会被服务商标记为中国地区（CN），导致无法使用某些仅限海外的功能（如 YouTube Premium 后台播放）。

## 2. IP 质量检测

在购买 VPS 后，建议先对分配的 IP 进行体检。

### 2.1 基础信息查询 (无 VPS 环境)

如果平台提供测试 IP，可以使用以下工具查询：

- **[IP2Location](https://www.ip2location.com/demo)**
  - *用途*：查询 IP 的地理位置、运营商、ASN 等基础信息。
  - *关注点*：`Region` (地区), `Usage Type` (使用类型), `ASN` (自治系统号)。

- **[IPQS (IPQualityScore)](https://www.ipqualityscore.com/user/search)**
  - *用途*：检测 IP 的欺诈分数 (Fraud Score) 和滥用记录。
  - *关注点*：Fraud Score 越低越好。

### 2.2 深度体检 (有 VPS 环境)

连接到 VPS 后，可以使用脚本进行全面检测：

- **[IP 质量体检脚本](https://github.com/xykt/IPQuality)**
  - *用途*：一键检测 IP 的流媒体解锁情况、欺诈值、端口连通性等。
  - *命令*：
    ```bash
    bash <(curl -Ls IP.Check.Place)
    ```

## 3. 搭建代理服务

### 3.1 服务端部署

推荐使用 **3x-ui** 面板，它支持多种协议且管理方便。

- **项目地址**: [3x-ui GitHub](https://github.com/MHSanaei/3x-ui)
- **推荐协议**: `VLESS-Reality` (目前抗封锁效果较好)

**安装建议**：
1. 确保 VPS 系统纯净（推荐 Debian 10+ 或 Ubuntu 20.04+）。
2. 建议先申请域名并配置 SSL 证书（虽然 Reality 不需要域名，但有域名更灵活）。
3. 使用一键脚本安装 3x-ui。

### 3.2 客户端配置

根据你的设备选择合适的客户端：

| 平台 | 推荐客户端 | 下载地址 |
| :--- | :--- | :--- |
| **macOS** | Clash Verge (Rev) / Clash Party | [Clash Party](https://github.com/mihomo-party-org/clash-party) |
| **Windows** | Clash Verge (Rev) / Clash Party | [Clash Party](https://github.com/mihomo-party-org/clash-party) |
| **iOS** | Loon / Shadowrocket / Stash | [Loon](https://nsloon.app/docs/intro/) |
| **Android** | FlClash / Surfboard | [FlClash](https://github.com/chen08209/FlClash) |
| **HarmonyOS** | ClashBox | [ClashBox](https://github.com/xiaobaigroup/ClashBox) |

## 总结

搭建一个稳定好用的代理服务，选对 VPS 和 IP 是成功的一半。希望本文能帮助你避坑，顺利搭建自己的网络环境。