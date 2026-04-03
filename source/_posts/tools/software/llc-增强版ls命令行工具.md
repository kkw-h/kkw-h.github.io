---
title: llc - 带文件注释功能的增强版 ls 工具
date: 2026-04-03 16:00:00
categories:
  - Tools
  - Software
tags:
  - CLI
  - Go
  - Tools
---

## 简介

**llc**（ls with comments）是一个跨平台的增强版 `ls -l` 命令行工具，在保留标准 `ls -l` 输出的基础上，额外显示文件的注释信息。支持 macOS 和 Linux 系统。

<!-- more -->

## 为什么需要 llc？

在日常开发中，我们经常需要为文件添加一些备注说明，但传统的 `ls` 命令无法显示这些信息。llc 填补了这个空白：

- 在 macOS 上使用 **Spotlight/Finder 注释**
- 在 Linux 上使用 **xattr 扩展属性**
- 完全兼容标准 `ls -l` 的输出格式

## 核心特性

| 特性 | 说明 |
|------|------|
| 📋 标准格式 | 保留 `ls -l` 完整输出（权限、所有者、大小、修改时间） |
| 💬 文件注释 | 显示文件注释（macOS: Finder 注释 / Linux: xattr） |
| 📝 设置注释 | 通过 `-e` 选项直接为文件添加注释 |
| 🎨 颜色输出 | 目录蓝色、可执行文件绿色、符号链接青色 |
| 📊 智能排序 | 支持按名称/时间/大小排序，支持反向排序 |
| 📏 多种选项 | 人类可读大小（`-h`）、inode 显示（`-i`）、目录优先等 |
| ⚡ 高性能 | Go 实现，比旧版 Swift 实现快 50-200 倍 |

## 安装

### Homebrew 安装（推荐）

```bash
brew tap kkw-h/llc
brew install llc
```

### 一键安装脚本

```bash
curl -fsSL https://raw.githubusercontent.com/kkw-h/llc/main/install.sh | bash
```

### 手动下载

从 [GitHub Releases](https://github.com/kkw-h/llc/releases) 下载对应系统的二进制文件。

### 源码构建

```bash
git clone https://github.com/kkw-h/llc.git
cd llc
go build -o llc
```

## 使用方法

### 基本用法

```bash
# 列出当前目录（带注释）
llc

# 列出指定目录
llc /path/to/directory

# 显示人类可读的文件大小
llc -h

# 按修改时间排序
llc -t

# 反向排序
llc -r

# 显示 inode 号
llc -i
```

### 文件注释操作

```bash
# 为文件添加注释
llc -e file.txt "这是重要的配置文件"

# 再次执行 llc，即可看到注释列
llc
```

输出示例：

```
-rw-r--r--  user  staff  1.2K  Apr 3 15:30  file.txt  这是重要的配置文件
drwxr-xr-x  user  staff   160  Apr 3 14:20  projects/
-rwxr-xr-x  user  staff   4.5K  Apr 3 13:10  deploy.sh   部署脚本
```

## 配置

llc 支持通过 `~/.llcrc` 配置文件设置默认选项：

```bash
# 总是显示人类可读大小
LLC_DEFAULT_OPTS="-h"

# 设置默认排序方式
LLC_DEFAULT_OPTS="-th"
```

## 适用场景

1. **配置文件管理** - 为各种配置文件添加用途说明
2. **项目文档** - 标记重要文件的功能和作用
3. **脚本管理** - 记录脚本的目的和使用场景
4. **团队协作** - 帮助团队成员快速理解文件用途

## 对比传统 ls

| 命令 | 显示注释 | 性能 | 颜色输出 | 跨平台 |
|------|----------|------|----------|--------|
| ls | ❌ | ⚡ | 部分支持 | ✅ |
| llc | ✅ | ⚡⚡⚡ | ✅ | ✅ |

## 总结

llc 是一个轻量级但实用的命令行工具，它完美解决了「想知道这个文件是干什么的」这个常见痛点。如果你经常需要在终端中管理大量文件，llc 会让你的工作更加高效。

**项目地址：** [https://github.com/kkw-h/llc](https://github.com/kkw-h/llc)
