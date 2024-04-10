---
title: 关于MacOS上面NavicatPremium升级到16.3版本之后，旧连接消失问题解决
date: 2024-04-10 15:46:13
tags:
    - 软件问题
---

## 问题

在Navicat Premium升级到16.3之后，会发现原来已经添加过的连接不显示了。
这个是Navicat Premium的数据存储位置改变了，把原来的数据移动到新的数据存储位置就OK了。

## 解决

### 旧数据存储位置

`~/Library/Application Support/PremiumSoft CyberTech/Navicat CC/Common/Settings/0/0`

### 新数据存储位置

`~/Library/Containers/com.navicat.NavicatPremium/Data/Library/Application Support/PremiumSoft CyberTech/Navicat CC`

#### 新数据位置查看方法

1. 打开Navicat Premium
2. 新建连接
3. 选择MySQL
4. 选项卡选择高级
5. 查看设置位置
<img src="/images/Navicat Premium查看新数据/01.png" alt="查看新数据位置" width="100%">

设置位置就是新的数据存储位置

### 复制数据

1. 把新数据位置里面的`Navicat CC`重命名
2. 复制旧数据里面的`Navicat CC`文件夹到新的位置
3. 重新打开Navicat Premium

现在就会出现原来的数据连接了！！！
