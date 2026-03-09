---
title: Docxy 自建 Docker Hub 镜像代理服务推荐
date: 2025-11-14 10:30:00
categories:
  - Technology
  - DevOps
tags:
  - Docker
  - Proxy
---

<img src="data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='960' height='320'><rect width='960' height='320' fill='%23f7f7f7'/><text x='30' y='40' font-size='22' font-family='Arial' fill='%23000'>Docxy Docker Hub 代理架构示意</text><rect x='40' y='80' width='240' height='160' rx='8' ry='8' fill='%23ffffff' stroke='%23008cff' stroke-width='2'/><text x='60' y='120' font-size='18' font-family='Arial' fill='%23008cff'>Docker 客户端</text><text x='60' y='150' font-size='14' font-family='Arial' fill='%23666'>daemon.json</text><text x='60' y='170' font-size='14' font-family='Arial' fill='%23666'>registry-mirrors</text><rect x='360' y='80' width='240' height='160' rx='8' ry='8' fill='%23ffffff' stroke='%2300aa6d' stroke-width='2'/><text x='380' y='120' font-size='18' font-family='Arial' fill='%2300aa6d'>Docxy 代理</text><text x='380' y='150' font-size='14' font-family='Arial' fill='%23666'>透明转发 /auth</text><text x='380' y='170' font-size='14' font-family='Arial' fill='%23666'>支持 HTTPS/Nginx/CDN</text><rect x='680' y='80' width='240' height='160' rx='8' ry='8' fill='%23ffffff' stroke='%23ff7a00' stroke-width='2'/><text x='700' y='120' font-size='18' font-family='Arial' fill='%23ff7a00'>Docker Hub</text><text x='700' y='150' font-size='14' font-family='Arial' fill='%23666'>Registry v2 API</text><defs><marker id='arrow' markerWidth='10' markerHeight='10' refX='6' refY='3' orient='auto'><path d='M0,0 L6,3 L0,6 Z' fill='%23999'/></marker></defs><line x1='280' y1='160' x2='360' y2='160' stroke='%23999' stroke-width='2' marker-end='url(%23arrow)'/><line x1='600' y1='160' x2='680' y2='160' stroke='%23999' stroke-width='2' marker-end='url(%23arrow)'/><text x='320' y='185' font-size='11' font-family='Arial' fill='%23999' text-anchor='middle'>GET /v2 401</text><text x='320' y='200' font-size='11' font-family='Arial' fill='%23999' text-anchor='middle'>→ 改写认证头</text><text x='640' y='185' font-size='11' font-family='Arial' fill='%23999' text-anchor='middle'>流式拉取/转发</text><text x='640' y='200' font-size='11' font-family='Arial' fill='%23999' text-anchor='middle'>镜像层</text></svg>" alt="Docxy 架构演示" width="100%" />


## 工具概述

Docxy 是一款轻量级的 Docker 镜像代理服务，专为解决国内访问 Docker Hub 受限、拉取速度慢、连接超时等问题而设计。它以“完全透明的代理”方式兼容 Docker Registry v2 API，客户端只需配置镜像源地址即可使用，无需改变习惯。

项目地址：`https://github.com/harrisonwang/docxy`

安装脚本直达：`https://raw.githubusercontent.com/harrisonwang/docxy/main/install.sh`

## 特色功能

- 一键部署：提供 `install.sh` 自动化脚本，涵盖环境准备、证书申请（Let's Encrypt）与服务部署。
- 多种部署模式：支持独立 HTTPS、Nginx 反向代理、CDN 回源（HTTP），方便融入现有架构。
- 支持登录提速：通过 `docker login` 认证，将匿名速率（10 次/小时/IP）提升至认证速率（100 次/小时/账户）。
- 完全透明代理：兼容 Registry v2，改写认证流程并流式转发镜像层，几乎无额外开销。
- 高性能与安全：基于 Rust + Actix Web 构建，内存安全、性能出众。

## 使用场景

- 自建镜像加速器：企业/个人需要稳定拉取公共镜像，绕过地域网络限制。
- 统一出口管理：通过 Nginx 或 CDN 接入，集中管理证书与访问策略。
- DevOps 提速：CI/CD 环境频繁拉取镜像，认证提升速率，减少构建等待。

## 安装指南

在开始前，将你的域名解析到目标主机。

1）执行一键安装脚本：

```bash
bash <(curl -Ls https://raw.githubusercontent.com/harrisonwang/docxy/main/install.sh)
```

2）根据需要选择部署模式：

- 独立运行（HTTPS）：监听 80/443，自动重定向与证书处理，最省心。
- Nginx 反向代理：由 Nginx 接管 HTTPS 与证书，Docxy 以 HTTP 在后端运行（如 9000 端口）。
- CDN 回源（HTTP）：Docxy 仅监听 HTTP，信任 `X-Forwarded-*` 头，适合全球加速。

## 使用示例

示例一：配置 Docker 客户端匿名代理

```json
// /etc/docker/daemon.json
{
  "registry-mirrors": ["https://your-domain.com"]
}
```

```bash
sudo systemctl restart docker
docker pull library/redis:latest
```

示例二：登录提升拉取速率

```bash
docker login your-domain.com
```

```json
// ~/.docker/config.json（将代理生成的 auth 同步到官方 Registry）
{
  "auths": {
    "your-domain.com": { "auth": "aBcDeFgHi..." },
    "https://index.docker.io/v1/": { "auth": "aBcDeFgHi..." }
  }
}
```

## 优缺点分析

- 优点：
  - 部署简单、集成灵活，适配 HTTPS/Nginx/CDN 等多种形态。
  - 完全透明代理与高性能流式转发，运维成本低。
  - 通过登录显著提升拉取速率，适合 CI/CD 与团队协作。
- 缺点：
  - 需自备域名，初次部署对新手有一定门槛。
  - 不做镜像缓存，极端网络条件下性能取决于上游连接质量。
  - 仍受 Docker Hub 速率与策略影响，需合理规划账户与访问频次。

## 社区支持

Docxy 开源托管于 GitHub，文档完备，提供一键安装脚本与多模式示例配置。可通过 Issues 与 Pull Requests 参与交流与贡献，适合作为团队自建镜像加速的基座组件。

## 相关链接

- GitHub 仓库：`https://github.com/harrisonwang/docxy`
- 安装脚本：`https://raw.githubusercontent.com/harrisonwang/docxy/main/install.sh`