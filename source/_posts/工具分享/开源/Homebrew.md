---
title: Homebrew MacOS平台上的软件包管理工具
date: 2021-09-14 10:57:23
tags:
  - 开源
  - MacOS
  - 实用工具
---


## 安装

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## 使用

``` bash
#搜索软件包
brew search git
#安装git
brew install git
```

## 添加国内加速镜像

[阿里云 https://developer.aliyun.com/mirror/homebrew](https://developer.aliyun.com/mirror/homebrew)
[腾讯云 https://mirrors.cloud.tencent.com/homebrew-bottles](https://mirrors.tencent.com/help/homebrew-bottles.html)

### Bash 终端配置

```bash
    # 替换brew.git:
    cd "$(brew --repo)"
    git remote set-url origin https://mirrors.aliyun.com/homebrew/brew.git
    # 替换homebrew-core.git:
    cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
    git remote set-url origin https://mirrors.aliyun.com/homebrew/homebrew-core.git
    # 应用生效
    brew update
    # 替换homebrew-bottles:
    echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.aliyun.com/homebrew/homebrew-bottles' >> ~/.bash_profile
    source ~/.bash_profile
```

### Zsh 终端配置

```bash
    # 替换brew.git:
    cd "$(brew --repo)"
    git remote set-url origin https://mirrors.aliyun.com/homebrew/brew.git
    # 替换homebrew-core.git:
    cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
    git remote set-url origin https://mirrors.aliyun.com/homebrew/homebrew-core.git
    # 应用生效
    brew update
    # 替换homebrew-bottles:
    echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.aliyun.com/homebrew/homebrew-bottles' >> ~/.zshrc
    source ~/.zshrc
```

### 恢复默认配置

出于某些场景, 可能需要回退到默认配置, 你可以通过下述方式回退到默认配置。

首先执行下述命令:

```bash
# 重置brew.git:
cd "$(brew --repo)"
git remote set-url origin https://github.com/Homebrew/brew.git
# 重置homebrew-core.git:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://github.com/Homebrew/homebrew-core.git
```

然后删掉 HOMEBREW_BOTTLE_DOMAIN 环境变量,将你终端文件

```bash
# 打开环境变量配置文件
 ~/.bash_profile
# 或者
 ~/.zshrc
# 删除 HOMEBREW_BOTTLE_DOMAIN 环境变量
 HOMEBREW_BOTTLE_DOMAIN
# 应用生效
 source ~/.bash_profile
# 或者
 source ~/.zshrc
```

## 参考资料

> - [Homebrew](https://brew.sh/)
