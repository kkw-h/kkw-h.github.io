---
title: "Laravel框架中Request类的get与input方法详解"
date: 2021-12-23 11:00:00
tags:
  - Laravel
---

## 摘要

本文详细介绍了Laravel框架中`Request`类的`get`和`input`方法的用法、实现细节以及它们之间的区别。通过对比分析，帮助开发者更好地理解两种方法在处理HTTP请求数据时的不同场景和潜在影响。

## 使用方式

### Get 方法

```php
$request = new \Illuminate\Http\Request();
$request->get('id');
```

`get`方法用于从请求中获取查询参数或属性。

### Input 方法

```php
$request = new \Illuminate\Http\Request();
$request->input('id');
```

`input`方法用于从请求中获取输入数据。

## 查看函数实现

### Get 方法实现

位置 `vendor/laravel/framework/src/Illuminate/Http/Request.php`

![Get方法的实现细节](/images/Laravel的Request类Get和Input的区别/01.png)

继续深入 `vendor/symfony/http-foundation/Request.php`

![Get具体实现](/images/Laravel的Request类Get和Input的区别/02.png)

`get()` 方法的实现逻辑如下：

```php
if ($this !== $result = $this->attributes->get($key, $this)) {
    return $result;
}
if ($this->query->has($key)) {
    return $this->query->all()[$key];
}
if ($this->request->has($key)) {
    return $this->request->all()[$key];
}
```

### Input 方法实现

位置 `vendor/laravel/framework/src/Illuminate/Http/Concerns/InteractsWithInput.php`

![Input方法实现](/images/Laravel的Request类Get和Input的区别/03.png)

![GetInputSource](/images/Laravel的Request类Get和Input的区别/04.png)

`input()` 方法会合并 `getInputSource` 和 `query` 的数据来查找指定的输入。

## 涉及影响

在使用 `$request->merge([]);` 方法后，两种方法的行为会有所不同：

- `get()` 方法可能会因为参数冲突而无法获取到通过 `merge` 方法添加的参数。
- `input()` 方法则可以获取到 `merge` 方法添加的参数，但可能会忽略原始的API传入参数。

## 结论

了解`get`和`input`方法的区别对于Laravel开发者来说非常重要。选择正确的方法可以确保应用程序按预期接收和处理请求数据。在实际开发中，应根据具体需求和上下文来决定使用哪种方法。
