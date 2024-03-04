---
title: NPM 的替代方案, JSR 快速体验
categories: 前端
tags: node
date: 2024-3-4
---

## 前言

NPM 作为 Node.js 的包管理工具，已经成为 Node.js 开发者不可或缺的工具。但是 NPM 也有一些局限性，比如安装包的速度慢、依赖包的版本管理不方便、安装包的依赖关系不明确等。为了解决这些问题，JSR 项目提供了一套新的包管理工具，可以替代 NPM。

## JSR 介绍

JSR 全称为 JavaScript Repository，2024年02月09日，Deno 推出新的 JavaScript 软件包 registry，即 npm 的替代方案 JSR([https://jsr.io](https://jsr.io))。

JSR 针对 TypeScript 进行了优化，仅支持 ES 模块。它可与 Deno 和基于 npm 的项目（Node、Bun、Cloudflare Workers 等）一起使用，并且免费开源。   

1）NPM 已经存在，为什么还要创建 JSR？
 - ESM 模块已成为标准，CommonJS 也逐渐被取代。
 - 在 Node 和浏览器之外出现了越来越多的 JavaScript 运行时，这使得以 Node 为中心的注册表不再适用。
 - 现在，TypeScript 已成为事实上的标准，因此需要一个能更好地支持 TypeScript 的现代注册表。

2）JSR 的核心特性：
 - 真正的 TypeScript 优先环境：高效的类型检查和无需转译的麻烦，可以直接使用TypeScript。
 - 编写和部署性能和易用性至上：通过集成的工作空间和无缝的NPM集成，JSR将易用性放在首位。
 - 安全和可访问的模块：JSR 中的所有模块都通过 HTTPS 公开，确保您的代码始终安全。
 - 开源，社区驱动：由开发者构建，为开发者构建，JSR由JavaScript社区的真实需求和贡献而塑造。
 - 仅限 ESM：JSR 仅支持 ECMAScript 模块，不支持 CommonJS。
 - 跨运行时支持：JSR 并非专为 Node 或 Deno 量身定制，而是旨在支持所有 JavaScript 运行时。
 - npm 兼容性：JSR 提供了一个 npm 兼容层，方便在 Node 项目中使用。

 ## JSR 快速体验

你可以像这样安装软件包：

```bash
# deno
deno add @luca/flag
# npm (and npm-like systems)
npx jsr add @luca/flag
```

您可以像导入其他 ES 模块一样导入软件包：

```javascript
import { printProgress } from "@luca/flag";
printProgress();
```

从命令行可以轻松发布自己的 TypeScript 和 JavaScript 模块：

```bash
# with deno installed (https://docs.deno.com/runtime/manual)
deno publish
# with npm-like systems
npx jsr publish
```

模块作为 TypeScript 源代码发布到 JSR。API 文档生成、类 Node 环境的类型声明和转译都由 JSR 处理。模块作者只需专注于编写 TypeScript。
更多参考：https://jsr.io/

