---
title: TypeScript 装饰器
categories: 前端
tags: javascript
date: 2023-8-07
---

### 装饰器概念   

装饰器是 typescript 中的一种特殊的类型声明，实际上 js 装饰器目前也已经提案了，目前处于 stage2 的阶段。顾名思义，装饰器起到了对数据的装饰(加工的)作用，可以被附加到类、方法、访问器、属性、参数上。   

装饰器使用 @expression 的形式使用，expression 是一个函数，会在运行时被调用，被装饰的数据会作为参数传入到这个函数中。   

如下述代码中的 decorator 就是一个装饰器函数，接收一个 target 参数，decorator 装饰器修饰了 Animal 这个类，那么 Animal 类就被作为 target 参数传入到了 decorator 函数中。   

```javascript
function decorator(target: any) {
  target.say = function () {
    console.log('hello!')
  }
}

@decorator
class Animal {
  static say: Function;
  constructor() {
  }
}

Animal.say() // hello!  
```
### 装饰器工厂   

上面装饰器其实有一种缺点，就是除了传入要装饰的数据之外，装饰器本身的功能不能通过传参去自定义，想要通过传参自定义装饰器的功能，我们可以使用装饰器工厂。   

装饰器工厂通过 @expression(args) 形式使用，装饰器工厂中的 expression 会返回一个装饰器函数，args 是用户想自定义传入的参数。   

如下述代码中 giveSay 就是一个装饰器工厂，接收一个参数 name，通过这个参数用户可以传入自定义想传入的数据。返回的装饰器函数接收 target 参数，使用装饰器工厂所修饰的数据（如下面代码中的 Animal1 和 Animal2） 会被作为 target 参数传入。

```javascript
function giveSay(name: string) {
  return function(target: any) {
      target.say = function () {
      console.log('hello! My name is ' + name)
    }
  }
}

@giveSay('Yuanbao')
class Animal1 {
  static say: Function;
  constructor() {
  }
}

Animal1.say() // hello! My name is Yuanbao

@giveSay('Facai')
class Animal2 {
  static say: Function;
  constructor() {
  }
}

Animal2.say() // hello! My name is Facai
```

### 装饰器类型

#### 类装饰器

类装饰器在类声明之前被声明（紧靠着类声明），它应用于类构造函数，可以用来监视，修改或替换类定义。单纯通过文字可能比较难理解，下面我们通过几个例子来看类装饰器的应用。   

##### 监视及修改类

如下代码，我们可以打印一下当 decorator 装饰器装饰一个类 Animal 时，接收到的参数：   

```javascript
function decorator(...args) {
  console.log(args)
}

@decorator
class Animal { 
    name = 'cat';
}
```

通过打印结果我们可以得知，装饰器作用于类时，接收到的参数就是类的构造函数，从而我们可以拿到类构造函数里面的参数及方法，起到监视类的作用。   

同理，装饰器的第一个参数是类的构造函数，那么也可以对类进行修改，覆盖、添加或者删除类里面的属性及方法：   

```javascript
function decorator(target: any) {
  target.say = function () {
    console.log('hello!')
  }
  target.run = function () {
    console.log('I am running.')
  }
}

@decorator
class Animal {
  static say: Function;
  constructor() {
  }
}

Animal.say() // hello!
Animal.run() // I am running.
```

##### 替换类定义

我们看一下当类装饰器有返回值的情况下会发生什么：   

```javascript
function returnStr(target) {  
  return 'hello world~';  
}  
  
function returnClass(target) {  
  return target;  
}  
  
@returnStr  
class ClassA { }  
  
@returnClass  
class ClassB { }  
  
console.log('ClassA:', ClassA); // ClassA: hello world~
console.log('ClassB:', ClassB); // ClassB: ƒ ClassB()
```

从打印结果可知，当类装饰器有返回值时，返回的值会替换原有的类的定义。   

#### 方法/访问器装饰器

将类方法和访问器装饰器放在一起讲，是因为这俩的装饰器用法相同，类方法/访问器装饰器接收三个参数：   

- 参数1：对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
- 参数2：成员的名字。
- 参数3：成员的属性描述符，详见 [descriptor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty#description "descriptor")   

JS 基础比较好的同学，看到这三个参数，可能立马会联想到 Object.defineProperty() 这个方法，接收的参数是一模一样的。      

```javascript
function readonly(target, name, descriptor){
  descriptor.writable = false
  return descriptor;
}

class Animal {
  @readonly
  name() { return 'PeiQi' }
}

console.log(Object.getOwnPropertyDescriptor(Animal, 'name'))
// { value:"Animal", writable:false, enumerable:false, configurable:true}
```

如果一个类方法装饰器有返回值，则返回值会被用作方法的属性描述符：      

```javascript
function readonly(target, name, descriptor){
  return { writable: false }
}

class Animal {
  @readonly
  name() { return 'PeiQi' }
}

console.log(Object.getOwnPropertyDescriptor(Animal, 'name'))
// { value:"Animal", writable:false, enumerable:false, configurable:true}
```
#### 属性装饰器

属性装饰器用于装饰类属性，接收 2 个参数：   

- 参数1：对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
- 参数2：成员的名字。

属性描述符不会做为参数传入属性装饰器，这与 TypeScript 是如何初始化属性装饰器的有关。 因为目前没有办法在定义一个原型对象的成员时描述一个实例属性，并且没办法监视或修改一个属性的初始化方法。返回值也会被忽略。因此，属性描述符只能用来监视类中是否声明了某个名字的属性。      

```javascript
const keys = []

function recordProperty(target, name){
  keys.push(name)
}

class Animal {
  @recordProperty
  name = 'zlx'

  @recordProperty
  age = 25
}参数装饰器用于修饰类方法的参数

console.log(keys)
// ["name", "age"]
```

#### 参数装饰器

参数装饰器用于修饰类方法的参数，接收 3 个参数：   

- 参数1：对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。   
- 参数2：成员的名字。   
- 参数3：参数在函数参数列表中的索引。   

参数装饰器只能用来监视一个方法的参数是否被传入，返回值会被忽略。   

```javascript
function ageDecorator(target, name, index){
  console.log(name, index)
}

class Animal {
  say(name: string, @ageDecorator age?: number) {
  }
}

// say 1
```

### 装饰器执行顺序

- 首先执行实例相关：参数装饰器 > 方法装饰器 > 访问器装饰器 > 属性装饰器   
- 然后执行静态相关：参数装饰器 > 方法装饰器 > 访问器装饰器 > 属性装饰器   
- 然后执行构造函数的参数装饰器   
- 最后是类装饰器   
- 多个装饰器装饰同一个数据时，从下往上依次执行      

```javascript
function staticParamsDecorator(target, name, index) {
  console.log('static params decorator')
}

function staticFuncDecorator(target, name, descriptor) {
  console.log('static func decorator')
}

function staticPropertyDecorator(target, name) {
  console.log('static property decorator')
}

function instanceParamsDecorator(target, name, index) {
  console.log('instance params decorator')
}

function instanceFuncDecorator(target, name, descriptor) {
  console.log('instance func decorator')
}

function instancePropertyDecorator(target, name) {
  console.log('instance property decorator')
}

function constructorParamsDecorator(target, name, index) {
  console.log('constructor params decorator')
}

function classDecorator1(target) {
  console.log('class decorator1')
}
function classDecorator2(target) {
  console.log('class decorator2')
}

@classDecorator1
@classDecorator2
class Animal {
  constructor(@constructorParamsDecorator options) {

  }

  @staticPropertyDecorator
  static Name = 'zlx'

  @staticFuncDecorator
  static Say(@staticParamsDecorator name: string) {

  }

  @instancePropertyDecorator
  age = 11

  @instanceFuncDecorator
  run(@instanceParamsDecorator time: number) {
  }
}

// instance property decorator
// instance params decorator
// instance func decorator
// static property decorator
// static params decorator
// static func decorator
// constructor params decorator
// class decorator2
// class decorator1
```