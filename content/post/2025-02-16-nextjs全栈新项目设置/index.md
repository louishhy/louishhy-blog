---
title: Next.JS的ESLint + Prettier设置
description: ""
date: 2025-02-16T11:03:44.042Z
preview: ""
draft: false
tags: [nextjs, prettier, eslint]
categories: [web dev]
image: ""
slug: prettier-eslint-setup-for-nextjs
---

简单记录一下在设置自己东拼西凑全栈的 ESLint + Prettier 的小 tips 和坑。

## 技术栈

首先贴一下自己最近接触 Web dev 之后选择/脑补出来的技术栈，
设想的使用场景是个人的兴趣开发，PoC 或者是小规模应用。

- Javascript / Typescript / React
- **Meta-framework**: Next.JS
  - 前后端可以同时开发，感觉还是很不错的。
- **CSS**: TailwindCSS
- **组件调试**: Storybook
- **UI 库**: shadcn-ui，lucide-react（图标）
  - 这个很看个人的爱好，我个人挺喜欢 shadcn 的设计语言。
- **认证系统**: Auth.JS
  - 懒人，开箱即用.jpg
- **ORM**: Prisma
- **输入/输出验证**: Zod
- **测试框架**: Jest
- **Format/Linting**：ESLint + Prettier
- **OpenAPI**：next-swagger-doc
- **表单**: react-hook-form

Email 方面目前还没有需求，
目前看着 resend 不错。

## 关于 ESLint + Prettier 的设置

项目开始的时候自然要设置一下 Format 和 linting。
虽然 ESLint 也有 formatting 的功能，
但是 Prettier 胜在开箱即用，据 stackoverflow 而言速度也快。

这次的方法针对的是配合 tailwindcss 的设置。

1. 设置完你的 next.js（利用`create-next-app`）。你的 tailwind 应该也在这个时候设置完毕了。
2. 安装 prettier。

```bash
npm i -D prettier
```

3. 为了防止 prettier 全局扫描时也扫描 node_modules 的全部文件，
   创建一个.prettierignore。

```
//.prettierignore
node_modules
```

4. prettier 存在一个配合 tailwindcss 的 plugin，
   可以按照 tailwind 推荐的方式来 rearrange classes。
   详见[这里](https://tailwindcss.com/blog/automatic-class-sorting-with-prettier)。

   我们首先安装一个 plugin。

```bash
npm install -D prettier-plugin-tailwindcss
```

然后，准备一个`prettier.config.mjs`。
（写这篇文章时 prettier config 对 ts 的支持仍在 beta，
所以用 mjs 以求稳定性。）

```js
const config = {
  plugins: ["prettier-plugin-tailwindcss"],
};

export default config;
```

5. 由于 eslint 和 prettier 可能会在 formatting 上发生冲突，
   所以安装一个 prettier 的官方包 [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)，
   让 eslint 来处理 linting，prettier 来处理 formatting。
   由于具体 config 方法各异，
   请参考这个 repo 的 README，给出了非常详尽的步骤。

6. （可选）可以打开 vscode 的自动 formatting-on-save。
   - 在 marketplace 安装 Prettier 的插件。
   - 搜索`default formatter`。
   - 设置 Default formatter 为 Prettier-code formatter。
   - 搜索`format on save`并将其打开。

现在可以快乐地使用了！
只要按 Ctrl+S 就会自动做好 formatting，让你的代码更加可读。
