---
title: 前端学习：开发游戏公会的PR Page中学到的
description: ""
date: 2025-02-01T07:30:04.697Z
preview: ""
draft: false
tags: [react, tailwind, next, frontend]
categories: [frontend]
image: ""
slug: things-i-learnt-from-building-game-guild-pr-page
---

最近找到工作之后心血来潮地想学一下前端，
第一个想自己做一些漂亮的网页，第二个是作为后端了解前端在做什么可以更好地了解整体的landscape。

正好自己之前为所属的mmo公会(BP你死的好惨啊.jpg)做过一个网页，
但是当时的网页是直接抄的模版，所以基本上就是填入想要的东西就可以，
主要的学习点是用Cloudflare。
所以这次就决定从零做起。

## 1. 技术选型
- 前端库: React with Typescript （后期转向了Next.JS框架）
- 框架: Next.JS
- CSS: Tailwind
- UI库: shadcn/ui
- UI动画: motion
- 云服务: Cloudflare

考虑到这个app不需要任何的后端逻辑，只是一个展示用的app，
而且想接触到稍微不太难的底层，所以使用了这个组合。
虽然到后期熟悉了一些React之后，还是投入了Next.JS。

说起来，前段时间在学校的时候另一位同学和我猛猛吐槽tailwind，
但是一把梭真的好爽啊 尤其是对于没有学过前端的人而言。

此外，Cloudflare真的是拯救了穷穷的我。

## 2. Style component的基本思路
这是我第一次使用tailwind或者说正经地学习css，
并且确实是我觉得最痛苦也最烦躁的一部分之一。
还好我们有tailwind。

相应地，总结了一部分思路，
个人觉得参照这些思路就可以决定自己需要哪些properties了。

### 2.1 定位方式
HTML的默认定位方式是`static`，也就是说元素会按照文档流正常排列，
不受定位影响。

所以如果想改变位置，必须指定
`relative? absolute? fixed? sticky?`。
这些决定了你定义component的坐标参考。
值得一提的是`absolute`的参考是第一个**非`static`**的ancestor。

例如说，当你需要覆盖屏幕的overlay的时候就用`fixed`，
它给出的坐标系就是整个视窗，随后再使用`inset-0`即可。

### 2.2 Flexbox, grid, container
决定你的容器类型。

flex和grid就不多赘述了，个人感觉要多加使用。
例如想要调整唯一元素居中的时候，相比于自己硬写，
用`justify-center`更加鲁棒。

container是tailwind提供的一种可以动态防止你的容器撑到屏幕两端的容器，
一般在style全页级的容器的时候很好用。

### 2.3 位置与大小
接下来就是决定component的位置和大小。

值得注意的是，当设计component的时候不要规定component本身的大小，
而应该设计component内部的响应式布局。
至于component本身的大小，
应当交给其父元素用围绕它的`<div>`块级元素来进行指定。
component本身使用`w-full`等适应其父元素，个人觉得会更好。

此外，在使用比例大小和基于`vw`，`vh`的大小时
有些时候还是要善用max或者min的。
它可以防止元素变得过小，
也可以防止变得变得过宽盛满元素。
这个确实是新的知识，人的视野其实只能集中在屏幕上的一部分，
文字太宽的话，会变得难以阅读。

### 2.4 Padding和Margin
这个就不多赘述了。一般而言p-4(1rem)是一个不错的开始点。

### 2.5 背景颜色
简单的背景颜色调整不再赘述。

一个很不错的functionality是tailwind的`bg-gradient-to-*`，
可以很愉快的构建出渐变色彩。

此外，透明度也可以很方便地调整。对于颜色是`*-color-*/[alpha]`，
比如说`from-gray-900/50`。`opacity-*`也可以帮助调整。

### 2.6 阴影/圆角/边缘等装饰
Tailwind提供了非常好的`rounded-*`，`border-*`和`shadow-[size]`, `shadow-[color]`
等utilities可以来美化元素。

### 2.7 图片等的处理
虽然严格上并不算是css相关，但是在处理用Next框架的图片的时候，
有一些必要的东西需要知道。
1. 把next/image的`Image`包在`<div>`中，再用块级元素去调整它的大小。
内层的图片加上`fill`即可，fill会使得next将图片填满父容器。
2. `object-position`和`object-fit`决定了**可替换元素(replaced elements)**
在其所占据的box中的位置。
可替换元素指的是像图片这种无法被css stylesheet影响到其本身的元素。
所以对于图片，视频而言非常重要。

> 对于`object-fit`，常用的主要是`object-cover`（不变形覆盖，无黑边但是会被crop）
> 和`object-contain`（不变形，完整展示，但是有可能产生黑边）。

3. 需要立刻展示的图片不要忘记了`priority`
4. 关于sizes，可以使用响应式，因为Next会自动调整fetch的图像的大小。
一个栗子是：`sizes="(min-width: 1080px) 100vw, 1080px`。
这样图片就会至少是1080p。

### 2.8 动画效果
复杂的动画效果建议使用motion库。
但是简单的onhover，tailwind的`hover:scale-105`等等的utility很好用。
具体你需要`transition-transform duration-500 hover:scale-110`的combo。
(`transition-transform`使得转换变得平滑。)

其他的就是少用的东西了，可以随时查询调整。

总结起来就是五大基础：
位置，大小（包括padding/margin的大小），颜色，装饰，动画。
图片需要多考虑一下可替换元素的特殊性质和图片的优化。

## 3.一些问题的解决方法
中途碰到了一些不熟悉的实现在这里也记录一下。
### 3.1 导入新字体并使用tailwind
1. 下载新字体，并把woff2打包好放在public中。
2. 去global css中设置好font face。
3. 去tailwind.config.ts的theme中添加font family。
别忘记加上default themes里面的sans。

### 3.2 预加载所有的图片后再展示
因为PR page是一系列的图片轮流播放，并且头图比较大，
所以有些用户会看到一个碎裂的图像加载出来（网速烂的时候）。

自己想到的解决方法是preload完所有的hero image才开始展示。
贴个代码。

```jsx
    // ...之前的代码
    const [currentIndex, setCurrentIndex] = useState(0);
    const [isInitialLoadComplete, setIsInitialLoadComplete] = useState(false);

    // 在背景第一次进入DOM后预加载，并且通过isInitialLoadComplete来控制。
    useEffect(() => {
        const preloadAllImages = async () => {
            // 封装为Promise，将其加载到浏览器内存。
            const loadPromises = backgroundImagesPath.map((path) => {
                return new Promise<void>((resolve) => {
                    const img = new Image();
                    img.src = path;
                    img.onload = () => {
                        resolve();
                    };
                });
            });

            // 等待全部预加载完毕。
            await Promise.all(loadPromises);
            // 发送预加载信号。
            setIsInitialLoadComplete(true);
        };

        preloadAllImages();
    }, []);

    useEffect(() => {
        if (!isInitialLoadComplete) {
            return;
        }

        // 预加载之后才开始播放轮换动画。
        const interval = setInterval(() => {
            setCurrentIndex((prev) => (prev + 1) % backgroundImagesPath.length);
        }, backgroundShiftIntervalSeconds * 1000);

        return () => clearInterval(interval);
    }, [isInitialLoadComplete]);
```

### 3.3 渐变切换的做法以及注意点
轮播的时候图片需要渐变隐出，与此同时下一张图片渐变进入。

这个效果我们可以用framer motion来实现。motion.div可以指定退出的动画。

需要注意的点是，因为`motion.div`退出的时候，如果直接将其从DOM上移除，
那么就不会有退出动画了，会直接消失。

motion的方法是将你要添加**退出**动画的元素一定要包在一个`<AnimatePresence>`中。
观察DOM可以发现，当元素被卸载时，它会存在这个Presence中一小段时间，
直到动画结束才会被卸载。

此外必须要提醒的是，在`<AnimatePresence>`中存在的元素，
一定要一个key。
motion通过检测这个key知道哪些元素发生了变化，
从而进行animate。

放一个代码！

```jsx
useEffect(() => {
        // Wait for initial load to complete
        if (!isInitialLoadComplete) {
            return;
        }

        const interval = setInterval(() => {
            setCurrentIndex((prev) => (prev + 1) % backgroundImagesPath.length);
        }, backgroundShiftIntervalSeconds * 1000);

        return () => clearInterval(interval);
    }, [isInitialLoadComplete]);

    return (
        <div className="relative h-screen w-full overflow-hidden bg-[#190157]">
            <AnimatePresence initial={false}> {/* initial 控制一开始就有的元素是否播放动画 */}
                {isInitialLoadComplete && (
                    <Background
                        key={currentIndex}
                        backgroundImagePath={backgroundImagesPath[currentIndex]}
                        backgroundShiftTransitionDuration={backgroundShiftDurationSeconds} />
                )}
            </AnimatePresence>
        { /* 其他的代码 */ }
    )
```

### 3.4 Next的Server-side component与Motion的问题
因为知识不足，被这个坑了老大一把。

Next默认是用React Server Component，
所以在使用Motion的时候无法完全兼容，Animate Presence的时候存在glitch。

原因是因为motion使用了effects，这些只有在client端才有。

解决方法是'use client'。

### 3.5 关于shadcn/ui
可以去`globals.css`中设置theme colors，非常适合决定好一套颜色之后
在全网页通用使用，
可以维持审美的一致性。

---

以上！

这个项目做了三四天，作为小白有不少的收获，记录一下。