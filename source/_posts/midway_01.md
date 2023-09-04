---
title: MidwayJS 全栈开发（一）技术选型
categories: 前端
tags: node
date: 2023-9-4
---

## 1 前言   

本文主要针对市面上所流行的node框架进行简单对比，对日后产品选型提供参考。   

对于前端同学来讲，NodeJS 可以说是一门非常合适的后端入门语言，有点儿 JS 基础就能快速上手后端；而且还能使用强大且熟悉的 NPM 包生态，帮助我们快速实现全栈开发的转变。 （当然，如果你还不太了解 JS，也可以先前置学习下基础。）   

除此之外，作为全栈开发，我们还需要考虑实际应用场景，看看 NodeJS 到底适合做什么，方便我们日后选型。   

这里我们简单调研了下 NodeJS 相对于传统 java 写后端的优劣：   

#### NodeJS 的优势   

- 占用内存小，启动迅速
- 基于事件驱动，无阻塞 I/O 模型
- 动态脚本语言，代码编写更容易，无需各种Setter\Getter等   

#### NodeJS 的劣势   

- 不适合处理 CPU 密集型（大量复杂计算）
- 缺少运行时类型检查，严谨的类型判断
- 缺少成熟的企业级服务端开发生态体系   

#### 小结    

综上所述，NodeJS 可能更适合以下几种场景：   

- IO 密集型（大量IO读写操作）场景，比如：排队预定系统、消息服务、事件广播
- 简单的 web 应用程序   

不适合以下场景：   

- CPU 密集型（大量计算）场景，比如：大数据
- 大型复杂系统

可以看到，NodeJS 在后端领域也是有用武之地的。作为全栈工程师，学习它，我相信不仅仅是提高我们的全局视野，更是解决一类场景问题的方案选型。   

### 框架选型对比   

接下来我再来聊聊后端框架的选型。鉴于基于 NodeJS 的 Web 框架有很多，目前社区常见的主要包括 ExpressJS、KoaJS、NestJS、MidwayJS 等，这里我们逐一介绍，做个简单的对比。

#### Express   

基本介绍    

Express 是 NodeJS 早期率先出现的一款框架，现在仍然非常流行，包括前端本地开发常用的 WebpackDevServer，底层就是基于它实现的   

Express 是基于函数回调实现异步的，采用 Node 中最常见的 Error-First 的模式设计的，和 NodeJS 本身设计非常相似，非常经典。 同时内置许多常用中间件，可谓是大而全，让人省心。   

HelloWorld 示例：   

```javascript
const express = require('express');
const app = express();

// 中间件
app.use((req, res, next) => {
  next();
});

// 路由
const router = express.Router();
router.get('/hello', (req, res) => {
    res.send('Hello World!');
});
app.use(router);

// 静态资源访问
app.use(express.static('./static'));

// 端口监听
app.listen(3000);
```

#### Koa2   

基本介绍   

随着 ECMAScript 的发展，Async Functions（ES2017）同步方式写异步代码的方式开始流行， KoaJS 一个新的 web 框架，由 Express 原班人马打造，诞生了。   

Koa2 基于 Async Functions 实现，而使用 Try-Catch 来捕获错误。   

Koa2 更关注框架本身性能体积以及定制扩展性，可谓是小而精。而对于后端开发常用的 session，视图模板，路由，文件上传，日志管理等方案，更多由社区提供。   

HelloWorld 示例:   

```javascript
const Koa = require('koa');
const Router = require('koa-router');
const serve = require('koa-static');
const app = new Koa();

// 中间件
app.use(async (ctx, next) => {
  await next();
});

// 路由
const router = Router();
router.get('/hello', (ctx) => {
    ctx.body = 'Hello World!';
});
app.use(router.routes());

// 静态资源访问
app.use(serve('./static'));

// 端口监听
app.listen(3000);
```

#### NestJS   

基本介绍   

不同于前两者，NestJS 是一套成体系的 NodeJS 后端开发框架。   

核心特性   

- 采用 TypeScript（JS 的超集语言） 提供优秀的类型支持
- 底层平台依赖无关，默认基于 Express 框架实现，可以使用 Express 提供的 API、对象等；同时通过底层 API 适配器，底- 层也可以替换成其它平台，比如 fastify、http://socket.io 等（目前已官方支持，但好像还不支持 koa）
- 装饰器路由，基于装饰器实现某个 API 的定义，包括路径、请求方法、入参等快速定义，实现路由去中心化
- 支持依赖注入，通过装饰器声明快速实现 Service 依赖注入进其它 Service 或者 Controller 中   

HelloWorld 示例   

使用 Nestjs 开发 Rest API，主要围绕着以下三个东西：   

- Controller 控制器，主要负责路由验证和管理；
- Service 服务，主要负责数据库 CRUD 逻辑的封装编写；
- Module 模块，主要负责整合 controllers 和 Service，也可以导出给其它模块复用。   

大致目录结构和写法如下：   

```javascript
// app.controller.ts    ****************************
import { Controller, Get } from "@nestjs/common";
import { AppService } from './app.service';

// 路由
@Controller("/")
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get("hello")
  getHello() {
    return this.appService.getHello();
  }
}


// app.service.ts    ****************************
import { Injectable } from "@nestjs/common";

@Injectable()
export class AppService {
  async getHello(): string {
    return "Hello World!";
  }
}


// app.module.ts    ****************************
import { Module } from '@nestjs/common';
import { ServeStaticModule } from '@nestjs/serve-static';
import { AppController } from './app.controller';
import { AppService } from './app.service';

@Module({
 imports: [
   // 静态资源访问
   ServeStaticModule.forRoot({ rootPath: './static' }),
  ],
  controllers: [ AppController ],
  components: [ AppService ],
})
export class appModule {}


// main.ts（约定：src根目录下且必须该文件命名）    ****************************
const app = await NestFactory.create(AppModule);
// 端口监听
app.listen(9000);
```

#### Midway

基本介绍   

Midway 是阿里推出的一款基于渐进式理念研发的 Node.js 框架。   

核心特性   

其设计方向上应该是和 NestJS 对标的，包括 TS、装饰器路由、依赖注入等基本特性基本都支持。   

不一样的是，Midway 底层默认依赖的 Koa，而不是 Express。   

HelloWorld 示例   

使用 Midway 开发 Rest API，使用方式和 NestJS 大同小异。   

但有点儿不一样的是，你无需声明 Modules 模块整合 Controllers 和 Services，然后在根 AppModule imports 声明。   

在 Midway 中，你只需要在项目内 Controller 控制器实现的地方声明 @Controller("/xxx")，最终路由 /xxx 即可生效，这点还是比较方便的。   

大致目录结构和写法如下：   

```javascript
// app.controller.ts    ****************************
import { Controller, Get, Inject } from "@midwayjs/decorator";
import { AppService } from './app.service';

// 路由
@Controller("/")
export class AppController {
  @Inject()
  appService: AppService;

  @Get("/hello")
  getHello() {
    return this.appService.getHello();
  }
}


// app.service.ts    ****************************
import { Provide } from "@midwayjs/decorator";

@Provide()
export class AppService {
  async getHello(): string {
    return "Hello World!";
  }
}


// configuration.ts（约定：src根目录下且必须该文件命名）    ****************************
import { App, Configuration } from '@midwayjs/decorator';  
import { ILifeCycle } from '@midwayjs/core';  
import * as koa from '@midwayjs/koa';

@Configuration({  
imports: [koa],  
importConfigs: ['./config/config.default')],  
})  
export class ContainerLifeCycle implements ILifeCycle {
  async onReady() {}
}


// config/config.default.ts    ****************************
import { MidwayAppInfo } from '@midwayjs/core'

export default (appInfo: MidwayAppInfo) => {
  koa: {
    // 端口监听
    port: 9000,
  }
}
```

### 小结

可以看到，现如今 NodeJS Web 框架大致分为两类：   

- 一类可以说是基础框架，包括 ExpressJS、KoaJS；
- 还有一类是更现代化一点的底层无关应用型框架，包括 NestJS、MidwayJS。   

对于 Web 服务端开发，这里没什么好纠结的，我们选择第二类，使用体验更加简单友好，场景也更全，也是未来框架架构设计的趋势。   

那么到底是选 NestJS 还是 MidwayJS 呢？   

首先，前面有说到，它们两者能力和使用方式上大同小异，所以选谁都可以。由于底层依赖框架不同（虽然很少能感知到底层），那么，熟悉 KoaJS 的可以尝试 MidwayJS，熟悉 Express 可以尝试 NestJS。   

另外，这里主要以学习为目的，所以个人想使用看看：较新的 Midway 相对 NestJS 有哪些设计上的不同以及最佳实践，所以本人选择它来学习实践。   

当然，MidwayJS 作为国产框架，那么相信大家可以加入到群聊一起探讨，快速沟通。也是非常方便的。