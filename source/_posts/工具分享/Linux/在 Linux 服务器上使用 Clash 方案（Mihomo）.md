---
title: 在 Linux 服务器上使用 Clash 方案（Mihomo）
date: 2025-07-24
tags:
  - Linux
  - 网络工具
  - 代理
  - Clash
  - Mihomo
---

## 简介

Mihomo（原名 Clash.Meta）是一个基于 Clash 的强大代理工具，专为 Linux 服务器环境优化。它提供了丰富的代理协议支持和灵活的规则配置，是在 Linux 服务器上搭建代理服务的理想选择。

## 安装步骤

### 1. 下载 Mihomo

首先，我们需要从 GitHub 下载最新版本的 Mihomo：

```bash
# 创建安装目录
mkdir -p /usr/local/bin/mihomo

# 下载最新版本
# https://github.com/MetaCubeX/mihomo/releases
curl -L https://github.com/MetaCubeX/mihomo/releases/download/v1.17.0/mihomo-linux-amd64-v1.17.0.gz -o mihomo.gz

# 解压
gzip -d mihomo.gz

# 添加执行权限
chmod +x mihomo

# 移动到安装目录
mv mihomo /usr/local/bin/mihomo/
```

### 2. 创建配置文件

创建配置目录并下载示例配置：

```bash
# 创建配置目录
mkdir -p /etc/mihomo

# 创建配置文件
touch /etc/mihomo/config.yaml
```

### 3. 修改配置文件

根据您的需求修改配置文件，主要需要配置以下部分：

- 代理服务器信息
- 规则设置
- 监听端口

基本配置示例：

```yaml
mixed-port: 7890
allow-lan: true
bind-address: '*'
mode: rule
log-level: info

proxies:
  - name: "服务器1"
    type: ss
    server: server-address
    port: 443
    cipher: chacha20-ietf-poly1305
    password: "password"

proxy-groups:
  - name: PROXY
    type: select
    proxies:
      - 服务器1
      - DIRECT

rules:
  - DOMAIN-SUFFIX,google.com,PROXY
  - DOMAIN-SUFFIX,github.com,PROXY
  - DOMAIN-SUFFIX,githubusercontent.com,PROXY
  - MATCH,DIRECT
```

### 4. 创建系统服务

为了让 Mihomo 作为系统服务运行，创建一个 systemd 服务文件：

```bash
cat > /etc/systemd/system/mihomo.service << 'EOF'
[Unit]
Description=mihomo Daemon, Another Clash Kernel.
After=network.target NetworkManager.service systemd-networkd.service iwd.service

[Service]
Type=simple
LimitNPROC=500
LimitNOFILE=1000000
CapabilityBoundingSet=CAP_NET_ADMIN CAP_NET_RAW CAP_NET_BIND_SERVICE CAP_SYS_TIME CAP_SYS_PTRACE CAP_DAC_READ_SEARCH CAP_DAC_OVERRIDE
AmbientCapabilities=CAP_NET_ADMIN CAP_NET_RAW CAP_NET_BIND_SERVICE CAP_SYS_TIME CAP_SYS_PTRACE CAP_DAC_READ_SEARCH CAP_DAC_OVERRIDE
Restart=always
ExecStartPre=/usr/bin/sleep 1s
ExecStart=/usr/local/bin/mihomo -d /etc/mihomo
ExecReload=/bin/kill -HUP $MAINPID

[Install]
WantedBy=multi-user.target
EOF
```

### 5. 启动服务

```bash
# 重新加载 systemd
systemctl daemon-reload

# 启动 Mihomo 服务
systemctl start mihomo

# 设置开机自启
systemctl enable mihomo

# 查看服务状态
systemctl status mihomo

# 查看服务运行日志
journalctl -u mihomo -o cat -e
# 或
journalctl -u mihomo -o cat -f
```

## 使用方法

### 设置系统代理

在需要使用代理的终端中，可以设置环境变量：

```bash
export http_proxy="http://127.0.0.1:7890"
export https_proxy="http://127.0.0.1:7890"
export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=http://127.0.0.1:7890
```

如果想要永久设置，可以将上述命令添加到 `~/.bashrc` 或 `~/.zshrc` 文件中。

### 验证代理

可以使用以下命令测试代理是否工作：

```bash
curl -v https://www.google.com
# 查看IP 是否已经变化
curl ipinfo.io/ip
```

## 高级配置

### 规则设置

Mihomo 支持多种规则类型，包括：

- DOMAIN：域名匹配
- DOMAIN-SUFFIX：域名后缀匹配
- DOMAIN-KEYWORD：域名关键词匹配
- IP-CIDR：IP 段匹配
- GEOIP：地理位置 IP 匹配
- MATCH：兜底规则

### Dashboard 配置

Mihomo 支持 Web 界面管理，在配置文件中添加：

```yaml
external-controller: 0.0.0.0:9090
secret: "your_password"
```

然后访问 `http://服务器IP:9090` 即可打开管理界面。
公共面板地址
https://yacd.metacubex.one/#/proxies


## 故障排除

1. 如果服务无法启动，检查日志：
   ```bash
   journalctl -u mihomo -f
   ```

2. 确保配置文件格式正确，YAML 格式对缩进敏感

3. 检查防火墙设置，确保相关端口已开放

## 参考资料

> - [Mihomo GitHub 仓库](https://github.com/MetaCubeX/mihomo)
> - [Mihomo 官方文档](https://wiki.metacubex.one/)