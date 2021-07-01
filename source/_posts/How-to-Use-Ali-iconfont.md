---
title: How to Use Ali iconfont
date: 2021-07-01 13:57:46
tags: iconfont
categories:
- Web 前端
---

[Iconfont](https://www.iconfont.cn/) 是阿里妈妈MUX倾力打造的矢量图标管理、交流平台。

设计师将图标上传到 iconfont 平台，用户可以自定义下载多种格式的icon，平台也可将图标转换为字体，便于前端工程师自由调整与调用。

## Unicode 引用

- 支持按字体的方式去动态调整图标大小，颜色等等。
- 默认情况下不支持多色，直接添加多色图标会自动去色。

### 第一步：拷贝项目下面生成的 `@font-face`

资源管理 --> 我的项目 --> 我参与的项目 --> xxx项目 --> Unicode or 下载至本地

提供的是文件链接，也可以把下面这3个文件下载到本地引用。

```css
@font-face {
  font-family: 'myIcon'; /*自定义，默认为项目名称*/
  src: url('iconfont.woff2?t=1625109690491') format('woff2'),
       url('iconfont.woff?t=1625109690491') format('woff'),
       url('iconfont.ttf?t=1625109690491') format('truetype');
}
```

### 第二步：定义使用 iconfont 的样式

```css
/* 自定义 className */
.myIconFont {
  font-family: "myIcon" !important; /* 需要与你的@font-face中的font-family保持一致 */
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```

### 第三步：挑选相应图标并获取字体编码，应用于页面

```html
<span class="myIconFont">&#x33;</span>
<span class="myIconFont">&#xe718;</span>
<i class="myIconFont">&#xe718;</i>
```

修改图标大小：重写 font-size 属性

```html
<span class="myIconFont" style="font-size: 500px;">&#x33;</span>
```

## Font Class 引用

font-class 是 Unicode 使用方式的一种变种，主要是解决 Unicode 书写不直观，语意不明确的问题。

与 Unicode 使用方式相比，具有如下特点：

- 相比于 Unicode 语意明确，书写更直观。可以很容易分辨这个 icon 是什么。
- 因为使用 class 来定义图标，所以当要替换图标时，只需要修改 class 里面的 Unicode 引用。

### 第一步：引入项目下面生成的 fontclass 代码：

资源管理 --> 我的项目 --> 我参与的项目 --> xxx项目 --> Font class or 下载至本地

```html
<link rel="stylesheet" href="./iconfont.css">
```

### 第二步：挑选相应图标并获取类名，应用于页面：

```html
<!-- 注意：第一个 class 需要与 iconfont.css 文件中的 @font-face {font-family: "myIconFont"} 保持一致，默认项目名，可自定义。第二个 class 是icon+图标的名称 -->
<span class="myIconFont icon-xxx"></span>
```

改变图标的大小：重写 font-size 属性

```html
<span class="myIconFont icon-xxx" style="font-size: 500px;"></span>
```

## Symbol 引用

这是一种全新的使用方式，应该说这才是未来的主流，也是平台目前推荐的用法。这种用法其实是做了一个 SVG 的集合，与另外两种相比具有如下特点：

- 支持多色图标了，不再受单色限制。
- 通过一些技巧，支持像字体那样，通过 `font-size`, `color` 来调整样式。
- 兼容性较差，支持 IE9+，及现代浏览器。
- 浏览器渲染 SVG 的性能一般，还不如 png。

### 第一步：引入项目下面生成的 symbol 代码：

资源管理 --> 我的项目 --> 我参与的项目 --> xxx项目 --> Symbol or 下载至本地

```html
<script src="./iconfont.js"></script>
```

### 第二步：加入通用 CSS 代码（引入一次就行）：

```html
<style>
/*自定义样式class名称*/
.myIcon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
</style>
```

### 第三步：挑选相应图标并获取类名，应用于页面：

```html
<!-- 样式名称与上一步定义的保持一致 -->
<svg class="myIcon" aria-hidden="true">
  <use xlink:href="#icon-xxx"></use>
</svg>
```

改变图标的大小：设置 font-size 属性

```html
<svg class="myIcon" aria-hidden="true" style="font-size: 500px;">
  <use xlink:href="#icon-xxx"></use>
</svg>
```

改变图标的大小：同时设置 width 和 height 属性（只设置一个没有作用）

```html
<svg class="myIcon" aria-hidden="true" style="width: 300px; height: 300px;">
  <use xlink:href="#icon-xxx"></use>
</svg>
```

## References

- [@font-face - MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@font-face)
- [iconfont 使用帮助](https://www.iconfont.cn/help/detail?spm=a313x.7781069.1998910419.d8cf4382a&helptype=code)
