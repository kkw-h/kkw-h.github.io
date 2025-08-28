---
title: MCP Toolbox for Databases 搭建及使用完整指南
date: 2025-08-28 17:13:54
tags:
  - AI工具
  - MCP
  - 数据库
  - 开发工具
  - Trae编辑器
categories: 工具分享
---

## 📖 概述

MCP Toolbox for Databases 是 Google 开发的一个强大的数据库管理工具，专门为现代 AI 开发工作流设计。它通过 Model Context Protocol (MCP) 提供了统一的界面来管理多种数据库系统，让开发者能够更高效地与数据库进行交互。

## ✨ 核心特性

- **🔌 多数据库支持**: 支持 MySQL、PostgreSQL、SQLite、MongoDB、Redis 等主流数据库
- **🎯 MCP 协议集成**: 完美集成 Model Context Protocol，支持各种 AI 编辑器
- **⚡ 高性能查询**: 优化的查询执行引擎，支持复杂 SQL 操作
- **🔧 工具化设计**: 可自定义数据库工具，方便 AI 助手调用
- **🛡️ 安全可靠**: 支持连接池管理和安全的认证机制

## 🚀 安装部署

### 系统要求
- **操作系统**: Linux、macOS、Windows (WSL)
- **内存**: 至少 512MB RAM
- **存储**: 100MB 可用空间
- **网络**: 需要访问数据库实例

### 二进制安装（推荐）

```bash
# 下载最新版本（当前版本 0.13.0）
export VERSION=0.13.0

# Linux x86_64
curl -LO https://storage.googleapis.com/genai-toolbox/v$VERSION/linux/amd64/toolbox
chmod +x toolbox

# macOS Apple Silicon
curl -LO https://storage.googleapis.com/genai-toolbox/v$VERSION/darwin/arm64/toolbox
chmod +x toolbox

# macOS Intel
curl -LO https://storage.googleapis.com/genai-toolbox/v$VERSION/darwin/amd64/toolbox
chmod +x toolbox
```

### 验证安装

```bash
# 检查版本
./toolbox --version

# 查看帮助信息
./toolbox --help
```

## ⚙️ 配置文件设置

### 创建基础配置文件

创建 `tools.yaml` 配置文件：

```yaml
# tools.yaml - MCP Toolbox 配置文件

# 数据源配置
sources:
  # MySQL 示例
  mysql-production:
    kind: mysql
    host: 127.0.0.1
    port: 3306
    database: production_db
    user: app_user
    password: secure_password_123
    maxOpenConns: 10
    maxIdleConns: 5
    connMaxLifetime: "30m"

  # PostgreSQL 示例  
  postgres-staging:
    kind: postgresql
    host: localhost
    port: 5432
    database: staging_db
    user: readonly_user
    password: another_password
    sslmode: disable

  # SQLite 示例
  sqlite-local:
    kind: sqlite3
    file: "/path/to/local.db"

# 工具定义
tools:
  # 用户查询工具
  search_user_by_id:
    kind: mysql-sql
    source: mysql-production
    statement: |
      SELECT 
        id, 
        username, 
        email, 
        created_at, 
        status 
      FROM users 
      WHERE id = {{.user_id}}
    description: |
      根据用户ID查询用户详细信息。
      支持精确匹配用户ID。
    templateParameters:
      - name: user_id
        type: integer
        description: 用户ID编号
        required: true

  # 产品搜索工具
  search_products:
    kind: mysql-sql  
    source: mysql-production
    statement: |
      SELECT 
        p.id,
        p.name,
        p.price,
        p.stock,
        c.name as category_name
      FROM products p
      LEFT JOIN categories c ON p.category_id = c.id
      WHERE p.name LIKE CONCAT('%', {{.keyword}}, '%')
        AND p.status = 'active'
      ORDER BY p.created_at DESC
      LIMIT {{.limit}}
    description: |
      根据关键词搜索产品信息。
      支持模糊匹配产品名称。
    templateParameters:
      - name: keyword
        type: string
        description: 搜索关键词
        required: true
      - name: limit
        type: integer
        description: 返回结果数量
        default: 10
        maximum: 100

  # 订单统计工具
  get_order_stats:
    kind: mysql-sql
    source: mysql-production
    statement: |
      SELECT 
        COUNT(*) as total_orders,
        SUM(amount) as total_amount,
        AVG(amount) as avg_amount,
        DATE(created_at) as order_date
      FROM orders 
      WHERE created_at >= {{.start_date}}
        AND created_at <= {{.end_date}}
      GROUP BY DATE(created_at)
      ORDER BY order_date DESC
    description: |
      获取指定时间范围内的订单统计信息。
      按日期分组显示订单数量和金额。
    templateParameters:
      - name: start_date
        type: string
        description: 开始日期 (YYYY-MM-DD)
        format: date
        required: true
      - name: end_date
        type: string
        description: 结束日期 (YYYY-MM-DD) 
        format: date
        required: true
```

## 🖥️ 启动和使用

### 命令行模式启动

```bash
# 基本启动
./toolbox --tools-file "tools.yaml"

# 启用UI界面（Web管理界面）
./toolbox --tools-file "tools.yaml" --ui

# 指定监听端口
./toolbox --tools-file "tools.yaml" --ui --port 8080

# 启用调试模式
./toolbox --tools-file "tools.yaml" --verbose
```

### 集成到 Trae 编辑器

在 Trae 编辑器的配置文件中添加 MCP 服务器配置：

```json
// Trae 编辑器配置文件 (~/.trae/config.json)
{
  "mcpServers": {
    "database-toolbox": {
      "command": "/absolute/path/to/toolbox",
      "args": [
        "--tools-file",
        "/absolute/path/to/tools.yaml",
        "--stdio"
      ],
      "env": {
        "LOG_LEVEL": "info"
      }
    }
  }
}
```

**重要提示**: 集成到 IDE 时必须添加 `--stdio` 参数，否则会报错。

## 🎯 使用示例

### 在 Trae 编辑器中调用工具

配置完成后，在 Trae 编辑器中可以直接调用定义的数据库工具：

```
# 调用用户查询工具
@database-toolbox search_user_by_id user_id=123

# 调用产品搜索工具  
@database-toolbox search_products keyword="手机" limit=5

# 调用订单统计工具
@database-toolbox get_order_stats start_date="2024-01-01" end_date="2024-12-31"
```

### Web UI 界面功能

启动 UI 界面后（`--ui` 参数），可以通过浏览器访问管理界面：

- **🔧 工具管理**: 查看和测试所有定义的数据库工具
- **📊 连接状态**: 监控数据库连接状态和性能指标
- **⚡ 实时查询**: 直接在界面中执行 SQL 查询
- **📋 历史记录**: 查看工具调用历史和执行结果

## 🔧 高级配置

### 环境变量配置

```bash
# 使用环境变量管理敏感信息
export DB_PASSWORD="your_secure_password"
```

在 `tools.yaml` 中使用环境变量：

```yaml
sources:
  mysql-db:
    kind: mysql
    host: "127.0.0.1"
    password: "${DB_PASSWORD}"  # 使用环境变量
```

### 多环境配置

创建不同环境的配置文件：

```bash
# 开发环境
./toolbox --tools-file "tools.dev.yaml"

# 生产环境  
./toolbox --tools-file "tools.prod.yaml"

# 测试环境
./toolbox --tools-file "tools.test.yaml"
```

## ⚠️ 注意事项

### 安全最佳实践

1. **🔐 密码管理**: 不要将密码硬编码在配置文件中，使用环境变量或密钥管理服务
2. **🌐 网络安全**: 生产环境建议使用 SSL/TLS 加密数据库连接
3. **👥 权限控制**: 为工具箱创建专用的数据库用户，授予最小必要权限
4. **📊 访问日志**: 启用访问日志记录，监控所有数据库操作

### 性能优化

1. **连接池配置**: 合理设置 `maxOpenConns` 和 `maxIdleConns`
2. **查询优化**: 为常用查询字段添加索引
3. **缓存策略**: 考虑添加查询结果缓存机制
4. **批量操作**: 支持批量查询和操作减少网络开销

## 🐛 常见问题

### Q: 启动时报错 "connection refused"
A: 检查数据库服务是否正常运行，网络连接是否通畅

### Q: Trae 编辑器无法连接工具箱
A: 确保配置中添加了 `--stdio` 参数，检查路径是否正确

### Q: 查询性能较慢
A: 检查数据库索引，优化 SQL 查询语句

### Q: 如何查看详细日志
A: 使用 `--verbose` 参数启动，或设置 `LOG_LEVEL=debug` 环境变量

## 📚 扩展资源

- [📖 官方文档](https://github.com/googleapis/genai-toolbox)
- [💬 GitHub Issues](https://github.com/googleapis/genai-toolbox/issues)
- [🛠️ 示例配置](https://github.com/googleapis/genai-toolbox/tree/main/examples)
- [📊 性能调优指南](https://github.com/googleapis/genai-toolbox/wiki/Performance-Tuning)

## 🎉 总结

MCP Toolbox for Databases 是一个功能强大的数据库工具平台，通过 MCP 协议为 AI 开发工作流提供了无缝的数据库集成体验。它的主要优势包括：

- **统一接口**: 多种数据库的统一管理和操作
- **AI 友好**: 完美支持各种 AI 编辑器和助手
- **安全可靠**: 企业级的安全特性和权限控制
- **高性能**: 优化的查询执行和连接管理
- **可扩展**: 支持自定义工具和插件机制

建议在测试环境中充分验证后再部署到生产环境，确保数据安全和系统稳定性。

---

*最后更新: 2025-08-27 | 版本: v0.13.0*