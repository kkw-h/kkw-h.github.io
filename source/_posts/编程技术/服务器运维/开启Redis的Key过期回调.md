---
title: Redis开启Key过期回调
date: 2021-04-26 09:53:39
tags:
  - Redis
---

## 修改Redis配置

第一种：修改 Redis 的 redis.conf

``` shell
notify-keyspace-events Ex
```

第二种：进入 redis-cli

``` shell
config set notify-keyspace-events Ex
```

## 重启Redis

``` shell
service redis-server restart
```

## 监听数据

```php
$redis=Redis::connection('publisher');//创建新的实例
$redis->psubscribe(['__keyevent@*__:expired'], function ($message, $channel) {
  echo $channel.PHP_EOL;//订阅的频道
  echo $message.PHP_EOL;//过期的key
  echo '---'.PHP_EOL;
});
```

配置开启 **read_write_timeout = 0**  表示可以进行长连接
