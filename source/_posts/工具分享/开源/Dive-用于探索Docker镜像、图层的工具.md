---
title: Dive 用于探索Docker镜像、图层的工具
date: 2022-04-25 14:00:56
tags:
  -	开源
  - Docker
  - 实用工具
---

<img src="/images/Dive.gif" alt="使用" width="100%" />

## 描述

要分析一个Docker镜像，只需要 `dive`运行镜像的 ID/Tag/Digest

## 使用

```shell
dive <image-tag>
```

或者如果你想构建你的镜像，然后直接开始分析它

``` bash
dive build -t <some-tag> .
```

## 参考资料

> - [Dive](https://github.com/wagoodman/dive)
