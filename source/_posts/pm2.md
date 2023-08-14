---
title: 利用 PM2 部署 node.js 项目的方法教程
categories: 前端
tags: node
date: 2023-8-14
---

## 1 前言  
  
PM2是一个Node.js进程管理器，可用于管理和部署Node.js项目。它可以在多个进程之间负载平衡，自动重启崩溃的进程并进行日志记录。在这篇攻略中，我们将介绍如何使用PM2来部署和管理Node.js项目。   

### 1 安装

PM2是通过npm安装的，因此您需要在本地安装Node.js和npm。安装Node.js和npm后，在终端中运行以下命令来安装PM2：   

```bash
npm install -g pm2
```

### 2 用法

- 启动一个进程

要启动一个进程，请在终端中运行以下命令：   

```bash
pm2 start app.js
```

其中，app.js是您要运行的Node.js应用程序的文件名。   

- 列出所有进程

要查看所有运行的进程，请运行以下命令：

```bash
 pm2 list
```

- 停止一个进程

要停止一个进程，请运行以下命令：   

```bash
pm2 stop app_name_or_id
```

其中，app_name_or_id是您要停止的进程的名称或ID。

- 重启一个进程

要重启一个进程，请运行以下命令：

```bash
pm2 restart app_name_or_id
```

其中，app_name_or_id是您要重启的进程的名称或ID。   

- 监视进程

可以使用以下命令监视指定应用的日志：   

```bash
pm2 logs app_name_or_id
```

### 示例

以下是一个示例展示如何使用PM2来部署和管理Node.js项目。

- 示例一：部署一个简单的HTTP服务器

首先，创建一个名为app.js的文件，内容如下：

```javascript
const http = require('http');
const server = http.createServer(function(req, res) {
  res.writeHead(200);
  res.end('Hello, World!');
});

server.listen(3000, function() {
  console.log('Server listening on port 3000');
});
```

上面的代码创建了一个简单的HTTP服务器，当用户访问该服务器时，服务器返回“Hello, World!”消息。现在，使用PM2来启动和管理该应用程序：

```bash
pm2 start app.js --name myapp
```

上面的命令将启动名为myapp的PM2进程，该进程将在端口3000上侦听HTTP请求。   

现在，您可以用以下命令对应用程序进行一些操作：   

```bash
 pm2 list
 pm2 stop myapp
 pm2 restart myapp
 pm2 logs myapp
```

- 示例二：部署一个使用Express.js框架的Web应用程序

创建一个名为app.js的文件，内容如下：   

```javascript
const express = require('express');
const app = express();

app.get('/', function(req, res) {
  res.send('Hello, World!');
});

app.listen(3000, function() {
  console.log('Server listening on port 3000');
});
```

上面的代码创建了一个基于Express.js框架的Web应用程序。现在，使用PM2来启动和管理该应用程序：   

```bash
pm2 start app.js --name myapp
```

上面的命令将启动名为myapp的PM2进程，该进程将在端口3000上侦听HTTP请求。

现在，您可以用以下命令对应用程序进行一些操作：

```bash
pm2 list
pm2 stop myapp
pm2 restart myapp
pm2 logs myapp
```

### 结论

PM2是一个强大而灵活的工具，可用于管理和部署Node.js应用程序。希望本攻略能为您提供有关如何使用PM2的基本概述。

