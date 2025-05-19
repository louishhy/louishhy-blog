---
title: 关于html图片和相关css的随记
description: ""
date: 2025-05-19T09:49:36.724Z
preview: ""
draft: false
tags: [css]
categories: [frontend]
image: ""
slug: regarding-html-image-and-related-css
---

最近因为参加了一个hackathon重新捡起了前端，
然后发现styling这方面需要和组友合作很多
所以开始重新inspect一些css的细节。

今天想要谈一些关于前端中图片的使用。

## 1. 图片的宽高
图片是一个replacement element，自己有一个intrinsic size。
但是其真实显示的大小是由CSS来决定的。

HTML best practice是务必给图片加上`width`,`height`和`alt`属性。
这样网页会预留好空间来放这个图片，避免layout shift。
与此同时也会知道图片的宽高比。
这样css控制时就会知道图片原始的比例。

注意css中，`width`和`height`是可以是relative的。
我以前的误解是这个东西必须是绝对数值，可以看看下面我为什么会产生这个误解（。

```css
img {
  width: 50%; /* 相对于父元素 */
  height: auto; /* 让浏览器自动计算高度，保持宽高比 */
}
```

## 2. Next.JS中的Image

Next.JS中Image组件中必须用绝对数值specify的`width`和`height`是决定图片的intrinsic size。
最终渲染的大小是css决定的。

这个就是我以为css中必须是绝对数值的原因。

另外一个就是Next.JS可以使用`fill`来让图片充满父元素，这个时候就不用`width`和`height`。

然而一个细节就是默认这个时候这个图片的position是absolute的。
所以要记得设置父元素/祖先元素为非`static`的position。

## 3. 关于`<img>`的display
`<img>`的display是`inline`的。

因此其实可以通过styling来改变其display的方式使得它不必包裹在一个`<div>`中
也能服从良好的布局。

之前有一篇文章说最好要用`<div>`包裹图片，现在想想也不一定。
事实上`object-fit`和`object-position`都不必再来一个`<div>`来包裹图片，
这些效果会直接作用于画框（`<img>`）上。

[MDN object-fit](https://developer.mozilla.org/en-US/docs/Web/CSS/object-fit)

[MDN object-position](https://developer.mozilla.org/en-US/docs/Web/CSS/object-position)

## 4. 关于text-align
`text-align`其实不单单是用来控制文字的，任何inline的元素都可以用这个属性来控制。

所以理所当然地你可以用这个来居中图片（只要你还没有改变它的`display`，默认是`inline`）

```css
img {
  text-align: center;
}
```

[MDN text-align](https://developer.mozilla.org/en-US/docs/Web/CSS/text-align)

## 5. 关于如何学习CSS的一个说法

看到一句话说CSS不能使用传统的程序员思维来学习，更多是随用随查，多用。
但是底层的一些概念要搞懂。

有一个学习路线说的是搞懂四个概念：
> **选择器，样式，盒模型，定位**

个人感觉总结的还是很不错的，
事实上很多时候颠来倒去的奇妙bug和不优雅的实现也是因为这些概念没有扎实引起。

以后可以参考这个学习路线来再吃一遍css。