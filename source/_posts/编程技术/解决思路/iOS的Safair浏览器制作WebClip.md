---
title: iOS的Safair浏览器制作WebClip
date: 2022-02-04 19:31:18
tags:
  - iOS
  - H5
  - WebClip
  - 解决思路
---

## 隐藏 Safari 用户界面组件

在 iOS 上，作为优化 Web 应用程序的一部分，让它使用独立模式看起来更像原生应用程序。当您使用此独立模式时，Safari 不用于显示 Web 内容 — 具体来说，屏幕顶部没有浏览器 URL 文本字段或屏幕底部的按钮栏。屏幕顶部仅显示状态栏。阅读[更改状态栏外观](https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariWebContent/ConfiguringWebApplications/ConfiguringWebApplications.html#//apple_ref/doc/uid/TP40002051-CH3-SW1)以了解如何最小化状态栏。

将`apple-mobile-web-app-capable`元标记设置`yes`为打开独立模式。例如，以下 HTML 使用独立模式显示 Web 内容。

``` html
<meta name="apple-mobile-web-app-capable" content="yes">
```

`window.navigator.standalone`您可以使用只读布尔 JavaScript 属性确定网页是否以独立模式显示。有关独立模式的更多信息，请参阅[apple-mobile-web-app-capable](https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariHTMLRef/Articles/MetaTags.html#//apple_ref/doc/uid/TP40008193-SW3)。

## 为 Web Clip 指定网页图标

您可能希望用户能够将您的 Web 应用程序或网页链接添加到主屏幕。这些由图标表示的链接称为 Web Clip。按照这些简单的步骤指定一个图标来代表您在 iOS 上的 Web 应用程序或网页。

- 要为整个网站（网站上的每个页面）指定一个图标，请将一个 PNG 格式的图标文件放在名为`apple-touch-icon.png`

- 要为单个网页指定图标或将网站图标替换为特定于网页的图标，请向网页添加链接元素，如下所示：

  `<link rel="apple-touch-icon" href="/custom_icon.png">`

  在上面的示例中，替换`custom_icon.png`为您的图标文件名。

- 要为不同的设备分辨率指定多个图标（例如，同时支持 iPhone 和 iPad 设备），`sizes`请为每个链接元素添加一个属性，如下所示：

  | `<link rel="apple-touch-icon" href="touch-icon-iphone.png">` |
  | ------------------------------------------------------------ |
  | `<link rel="apple-touch-icon" sizes="152x152" href="touch-icon-ipad.png">` |
  | `<link rel="apple-touch-icon" sizes="180x180" href="touch-icon-iphone-retina.png">` |
  | `<link rel="apple-touch-icon" sizes="167x167" href="touch-icon-ipad-retina.png">` |

  使用最适合设备大小的图标。有关当前图标大小和建议，请参阅*iOS 人机界面指南*的“图形”章节。

如果没有与设备推荐尺寸匹配的图标，则使用大于推荐尺寸的最小图标。如果没有大于建议大小的图标，则使用最大的图标。

如果没有使用链接元素指定图标，则在网站根目录中搜索带有`apple-touch-icon...`前缀的图标。例如，如果设备的适当图标大小为 58 x 58，则系统会按以下顺序搜索文件名：

1. 苹果触摸图标 80x80.png
2. 苹果触摸icon.png

**注意：** iOS 7 上的 Safari 不会为图标添加效果。旧版本的 Safari 不会为以`-precomposed.png`后缀命名的图标文件添加效果。有关详细信息，请参阅第一步：在 iTunes Connect中识别您的应用程序。

## 指定启动屏幕图像

在 iOS 上，与原生应用程序类似，您可以指定在 Web 应用程序启动时显示的启动屏幕图像。这在您的 Web 应用程序离线时特别有用。默认情况下，使用上次启动 Web 应用程序时的屏幕截图。要设置另一个启动图像，请在网页中添加一个链接元素，如下所示：

```html
<link rel="apple-touch-startup-image" href="/launch.png">
```

在上面的示例中，替换`launch.png`为您的启动屏幕文件名。有关当前启动屏幕尺寸和建议，请参阅*iOS 人机界面指南*的“图形”章节。

## 添加启动图标标题

在 iOS 上，您可以为启动图标指定 Web 应用程序标题。默认情况下，使用`<title>`标签。要设置不同的标题，请向网页添加元标记，如下所示：

```html
<meta name="apple-mobile-web-app-title" content="AppTitle">
```

在上面的示例中，替换`AppTitle`为您的标题。

## 更改状态栏外观

如果您的 Web 应用程序像原生应用程序一样以独立模式显示，您可以最小化显示在 iOS 屏幕顶部的状态栏。使用状态栏样式的元标记执行此操作。

[除非您首先按照隐藏 Safari 用户界面组件](https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariWebContent/ConfiguringWebApplications/ConfiguringWebApplications.html#//apple_ref/doc/uid/TP40002051-CH3-SW2)中的描述指定独立模式，否则此元标记无效。然后使用状态栏样式元标记 ,`apple-mobile-web-app-status-bar-style`来根据您的应用程序需要更改状态栏的外观。例如，如果要使用整个屏幕，请将状态栏样式设置为半透明黑色。

例如，以下 HTML 将状态栏的背景颜色设置为黑色：

```html
<meta name="apple-mobile-web-app-status-bar-style" content="black">
```

有关状态栏外观的更多信息，请参阅*iOS 人机界面指南*的“UI 栏”一章。

## 链接到其他原生应用

通过使用特殊 URL 创建链接，您的 Web 应用程序可以链接到其他内置 iOS 应用程序。可用功能包括拨打电话号码、发送 SMS 或 iMessage 以及在其本机应用程序中打开 YouTube 视频（如果已安装）。例如，要链接到电话号码，请按以下格式构建锚元素：

```html
<a href="tel:1-408-555-5555">给我打电话</a>
```

有关这些功能的完整信息，请参阅*[Apple URL Scheme Reference](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007899)*。

## 参考资料
>
> - [配置 Web 应用程序](https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariWebContent/ConfiguringWebApplications/ConfiguringWebApplications.html#//apple_ref/doc/uid/TP40002051-CH3-SW6)
