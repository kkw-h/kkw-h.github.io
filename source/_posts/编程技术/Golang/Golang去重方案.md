---
title: Golang去重方案
date: 2024-07-23 09:33:32
tags:
  - golang
---

``` go
//定义一个对应类型的 map
arrMap := make(map[int64]bool) //key 可以是各种类型
for _, i := range arr{
  if arrMap[i.id] {
    continue
  }
  arrMap[i.id] = true
  //数据处理
}
```

