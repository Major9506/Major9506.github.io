---
title: Express 框架中使用 TypeScript
categories: 前端
tags: node
date: 2024-2-28
---

## 1 前言

本文主要记录使用 Express 框架时，如何使用 TypeScript 进行开发。

### 2 初始化项目

首先，我们需要初始化一个 Express 项目，并安装 TypeScript 相关依赖：

```bash
mkdir myapp
cd myapp
npm init -y
npm install express typescript ts-node @types/express    # 安装 TypeScript 相关依赖
```

### 3 配置 TypeScript       

1. 创建 `tsconfig.json` 文件，并配置 TypeScript 编译选项：

```json
{
  "compilerOptions": {    
    "target": "es6",        // 编译目标
    "module": "commonjs",    // 模块规范
    "outDir": "./dist",      // 编译输出目录
    "strict": true,          // 开启严格模式
    "esModuleInterop": true,  // 允许使用 import/export 语法
    "skipLibCheck": true,     // 跳过类型检查 .d.ts 文件
    "forceConsistentCasingInFileNames": true  // 文件名大小写敏感
  },
  "include": ["src/**/*"]    // 指定编译目录
}
```

2. 创建 `src` 目录，并在其中创建 `index.ts` 文件，作为项目入口文件：

   ```typescript
   import express from 'express';

   const app = express();

   app.get('/', (req, res) => {
     res.send('Hello World!');        
   });

   app.listen(3000, () => {
     console.log('Server is running on port 3000');
   });
   ```

3. 在 `package.json` 文件中，添加 `start` 脚本，用于启动项目：

   ```json
   {
     "name": "express-ts",
     "version": "1.0.0",
     "description": "",
     "main": "index.js",
     "scripts": {
       "build": "npx tsc",
       "start": "node dist/index.js",
       "dev": "ts-node src/index.ts",
       "test": "echo \"Error: no test specified\" && exit 1"
     },
     "keywords": [],
     "author": "",
     "license": "ISC",
     "dependencies": {
       "express": "^4.17.1",
       "typescript": "^4.1.3",
       "ts-node": "^9.0.0",
       "@types/express": "^4.17.9"
     }
   }
   ```

4. 运行 `npm dev` 命令，启动项目。

### 4 类型注解

TypeScript 是一个强类型语言，因此我们需要对函数参数和返回值进行类型注解。

```typescript
import express from 'express';

const app = express();

app.get('/', (req: express.Request, res: express.Response) => {
  res.send('Hello World!');        
});

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
```

### 5 路由参数

Express 路由支持动态参数，我们可以利用 TypeScript 的类型注解来定义路由参数类型。

```typescript
import express from 'express';

const app = express();

app.get('/users/:id', (req: express.Request, res: express.Response) => {
  const id = req.params.id;
  res.send(`User ${id}`);        
});


app.listen(3000, () => {      
  console.log('Server is running on port 3000');
});
```
      

### 6 响应体

Express 响应体是一个 stream，我们可以利用 TypeScript 的类型注解来定义响应体类型。 

```typescript                                                                                
import express from 'express';


const app = express();

app.get('/users', (req: express.Request, res: express.Response) => {
  const users = [
    { id: 1, name: 'Alice' },
    { id: 2, name: 'Bob' },
    { id: 3, name: 'Charlie' }
  ];
  res.status(200).json(users);
});


app.listen(3000, () => {      
  console.log('Server is running on port 3000');
});
```

### 7 错误处理


Express 内置了错误处理机制，我们可以利用 TypeScript 的类型注解来定义错误处理函数类型。

```typescript
import express from 'express';                


const app = express();                

app.get('/users/:id', (req: express.Request, res: express.Response) => {
  const id = req.params.id;
  const user = getUserById(id);
  if (!user) {
    res.status(404).send('User not found');        
  } else {
    res.send(`User ${user.name}`);        
  }
});

function getUserById(id: string): { id: string, name: string } | undefined {
  const users = [
    { id: '1', name: 'Alice' },
    { id: '2', name: 'Bob' },
    { id: '3', name: 'Charlie' }
  ];
  return users.find(user => user.id === id);
}

app.use((err: any, req: express.Request, res: express.Response, next: express.NextFunction) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});

app.listen(3000, () => {      
  console.log('Server is running on port 3000');
});
``` 

### 8 总结

本文介绍了使用 Express 框架时，如何使用 TypeScript 进行开发。TypeScript 提供了强类型、自动补全、代码提示等功能，可以帮助我们更好地编写代码，提高开发效率。

本文使用的 Express 版本为 4.17.1，TypeScript 版本为 4.1.3。 Express 4.x 版本与 TypeScript 3.x 版本不兼容，因此需要安装 `@types/express` 类型定义文件。

Express 4.x 版本与 TypeScript 4.x 版本兼容，但需要注意一些 breaking changes。

本文仅涉及 Express 框架的部分内容，TypeScript 还有很多特性值得我们去探索。

## 参考

- [Express 官网](https://expressjs.com/)
- [TypeScript 官网](https://www.typescriptlang.org/)
- [Express with TypeScript](https://expressjs.com/en/guide/typescript.html)
- [TypeScript 入门教程](https://www.tslang.cn/docs/home.html)
