---
title: JavaScript & TS 编码风格指南
tags: 编码风格指南
---
## 1 前言
虽然本文档是针对JavaScript设计的，但是在使用各种JavaScript的预编译语言时(如TypeScript等)时，适用的部分也应遵循本文档的约定。

## 2 代码风格

### 2.1 代码格式化
1. 代码统一使用[prettier](https://github.com/prettier/prettier)进行格式化处理
2. prettier安装，各项目可固定使用某一版本。

``` shell
yarn add prettier@1.14.3 --dev --exact

npm install --save-dev --save-exact prettier@1.14.3
```

3. prettier在[IDE上的配置](https://prettier.io/docs/en/editors.html)

4. 每次提交PR时，commiter需对代码格式化，否则reviewer将拒绝合并。

### 2.2 命名
文件、类、接口、函数或变量等命名，使用大驼峰、小驼峰或全大写，不推荐使用下划线作为命名首字母

#### 2.2.1 文件
一个文件对应一组逻辑(一个类、一些功能函数的集合、样式集合)

##### 2.2.1.1 文件内部是一个类的，使用大驼峰命名。

``` file
HomeScreen.tsx
```

##### 2.2.1.2 文件内部是一些功能函数集合的，使用小驼峰命名。

``` file
screenUtil.ts
```

##### 2.2.1.3 文件内部是样式集合的，使用小驼峰命名。

``` file
screenStyle.ts
```

#### 2.2.2 类、接口

``` typescript
/* 类名使用大驼峰 */
class HomeScreen {

    /* 成员变量使用小驼峰 */
    title: string;
    
    eventFunc = (event: type): returnType => {
        /* do something */
    };
    
    /* 成员函数使用小驼峰 */
    calculate(input: type): returnType {
        /* do something */
    }
}

/* 接口名使用大驼峰 */
interface User {
    
    /* 成员变量使用小驼峰 */
    name: string;
    
    login: (input: type) => returnType;
}
```

#### 2.2.3 函数名，使用小驼峰命名。

``` typescript
function funcName(input: type): returnType {
    
}
```

#### 2.2.4 全局常量名，使用全大写，并用下划线连接单词。

``` typescript
const SCREEN_WIDTH = 375;
```

#### 2.2.5 全局变量(不推荐使用，一定要使用请勿导出)，使用大驼峰命名。

``` typescript
let HeaderHeight = 500;
```

#### 2.2.6 变量名，使用小驼峰命名。

``` typescript
let someValue;
```

#### 2.2.7 枚举类型命名

``` typescript
/* 枚举名使用大驼峰命名 */
enum LoanState {

	/* 枚举属性使用字母全大写，下划线分割单词的命名风格 */
    REPAYMENT = 1,
}
```

#### 2.2.8 其他命名规范

##### 2.2.8.1
由多个单词组成的缩写词，在命名中，根据当前命名法和出现的位置，所有字母的大小写与首字母的大小写保持一致。

``` typescript
function XMLParser(input: type): returnType {
}

function insertHTML(element: type, html: type): returnType {
}

let httpRequest = new HTTPRequest();
```

##### 2.2.8.2
类名、接口、枚举使用名词

``` typescript
class HomeScreen {
    
}

interface Personal {
    
}

enum LoanState {
    
}
```

##### 2.2.8.3
函数名使用动宾短语

``` typescript
function setState(state: type): returnType {
    
}

function calculateHeight(height: type): returnType {
    
}
```

##### 2.2.8.4
`boolean`类型的变量使用`is`或`has`开头，返回值为`boolean`类型的函数也遵循此规则

``` typescript
let isReady = false;
let hasMore = false;

function isSthTodo(): boolean {
    ...
    return true;
}
```

### 2.3 类型声明和使用
1. 所有类或接口的成员，变量、函数(参数、返回值）的声明，都必须声明类型，且不允许使用`any`。能隐式推导出类型的，可不声明类型。

``` typescript
let height: number;

interface User {
    name: string;
    age: number;
}

const PI = 3.1415926; /* 能隐式推导出类型，省略类型声明 */

let user: User = {
    name: "fmb",
    age: 12
}

/* 若函数没有返回值，需声明返回类型为void */
function setName(name: string): void {
    
}

/* 不推荐 */
let height;

user = {
    name: "xx",
    age: 1;
}

interface User {
    name: any;
    age: any;
}
```

2. 推荐使用`undefined`作为类型声明，而非`null`。

``` typescript
/* good style */
interface User {
    name: string;
    arg: number | undefined;
}

/* bad style */
interface User {
    name: string;
    arg: number | null;
}
```

### 2.4 注释

#### 2.4.1 文件
创建文件时，开发者需创建一个文件头注释，如下:

``` typescript
/**
 * @file Describe the file
 * @author authorName(mail-name@domain.com)
 */
```

#### 2.4.2 类、接口或枚举
创建类、接口或枚举时，开发者需创建一个类头注释，如下:

``` typescript
/**
 * @class Describe the class
 */
 interface User {
     
 }
```

#### 2.4.3 函数
创建函数时，开发者需创建一个函数头注释，如下:

``` typescript
/**
 * @function Describe the function
 *
 * @param {type1} p1 参数1的说明
 * @param {type2} p2 参数2的说明，比较长
 *     那就换行了.
 * @param {type3} p3 参数3的说明（可选）
 * @return {returnType} 返回值描述
 */
function foo(p1: type1, p2: type2, p3?: type3): returnType {
    let p3 = p3 || 10;
    return {
        p1: p1,
        p2: p2,
        p3: p3
    };
}
```

#### 2.4.4 其他注释
推荐使用`/* */`进行细节注释，不推荐使用`//`进行细节注释。

``` typescript
const PI = 3.14; /* good comments style */

const FML = "f**k my life"; // bad comments style
```



### 2.5 语言特性

#### 2.5.1 变量
1. 使用`let`定义变量而不是`var`
2. 每个`let`声明一个变量。

``` typescript
let a = 1; /* good style */

let a = 2, b = 1; /* bad style */
var a = 3; /* bad style */
```

#### 2.5.2 条件
1. 除`null`或`undefined`的判断可用`==`外，其余的条件判断都使用`===`，以避免隐式的类型转换。
2. 尽可能的使用简洁的条件表达式

``` typescript
if (age === 10) /* good style */
if (arg == 10) /* bad style */

let name = "abc";
/* 判断name为空 */
if (!name) /* good style */
if (name === "") /* bad style */

let isLogin = true;
if (!isLogin) /* good style */
if (isLogin == false) /* bad style */

let array: number[];
/* 判断数组非空 */
if (array.length) /* good style */
if (array.length > 0) /* bad style */

/* null 或 undefined */
if (noValue == null) /* good style */
if (noValue == undefined) /* good style */
if (hasValue) /* good style */

if (novalue === null) /* bad style */
if (novalue === undefined) /* bad style */
if (hasvalue !== undefined) /* bad style */
```

#### 2.5.3 循环

``` typescript
for (statement1; statement2; statement3) /* good style */
for (... in ...) /* bad style */
array.forEach(...) /* good style */
```

#### 2.5.4 类型
##### 2.5.4.1 类型检测
类型检测优先使用 `typeof`。对象类型检测使用 `instanceof`。`null` 或`undefined`的检测使用 `== null`。

```typescript
/* string */
typeof variable === 'string'

/* number */
typeof variable === 'number'

/* boolean */
typeof variable === 'boolean'

/* Function */
typeof variable === 'function'

/* Object */
typeof variable === 'object'

/* RegExp */
variable instanceof RegExp

/* Array */
variable instanceof Array

/* null or undefined */
variable == null
```

#### 2.5.4.2 类型转换
[建议] 转换成 string 时，使用`"" +` 或`String(sth)`。

``` typescript
let str = "" + sth; /* good style */
let str = String(sth); /* good style */

let str = new String(sth); /* bad style */
let str = sth.toString(); /* bad style */
```

[建议] 转换成 number 时，通常使用 `+str 或 Number(str)`，若要转换的字符串结尾包含非数字并期望忽略时，使用 `parseInt`(必须指定进制)或`parseFloat`。

``` typescript
/* str = "1234.12" */
let num = +str; /* good style */
let num = Number(str); /* good style */

str = "123.4hello";
let num = ParseInt(str, 10); /* good style, output is 123 */
let num = ParseFloat(str); /* good style, output is 123.4 */

let num = new Number(str) /* bad style */
```

[建议] 转换成 boolean 时，使用 `!!`。

```typescript
let num = 3.14;
let condition = !!num;
```

[建议] number 去除小数点，使用 Math.floor / Math.round / Math.ceil，不使用 parseInt。

```typescript
/* good style */
let num = 3.14;
Math.ceil(num);

/* bad style */
let num = 3.14;
parseInt(num, 10);
```

## 3 实用库介绍
### 3.1 [numeral](http://numeraljs.com/)
用于创建、格式化number类型，以及一些number处理的实用方法。

``` typescript
let num = numeral(1000000);
let value = num.value(); /* 1000 */
value = num.format('0,0.00') /* "1,000,000.00" */
```

更多详细用法详见[官方文档](http://numeraljs.com/)

### 3.2 [moment](https://momentjs.com)
用于创建、格式化时间，以及一些时间处理的实用方法。

```typescript
const timestamp = 1539571685044;
const date = new Date(timestamp);
const dateStr = "1989-06-04 11:11:11"

/* output "2018.10.15 10:48:05" */
moment(timestamp).format("YYYY.MM.DD HH:mm:ss");

/* output "2018-10-15 10:48:05" */
moment(date).format("YYYY年MM月DD日 HH:mm:ss")

/* output "1989年06月04日 11:11:11" */
moment(dateStr, "YYYY-MM-DD HH:mm:ss").format("YYYY年MM月DD日 HH:mm:ss")

...
```

更多详细用法详见[官方文档](https://momentjs.com/docs/)