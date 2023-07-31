---
title: TypeScript 泛型与重载
categories: 前端
tags: javascript
date: 2023-8-01
---

## 前言
泛型可以用来增强代码的灵活性和重用性。它允许在代码编写阶段不指定具体的数据类型，而在使用阶段再确定数据类型。这在C#和Java等强类型语言有着广泛的应用。

### 范型的应用场景   
泛型的主要应用方式为泛型函数、泛型对象以及泛型接口。主要针对这三种方式说下泛型的应用场景以及技巧。

1.泛型函数   

泛型函数允许我们在函数定义时使用类型参数，这些类型参数可以在函数体内表示不特定的类型，从而使函数可以接受多种类型的参数或返回多种类型的值。这样，我们可以在调用泛型函数时根据传入的类型来动态地确定函数的行为。   
以下是一段简单的泛型函数使用示例：
```javascript

function identity<T>(arg: T): T { return arg; } // 使用泛型函数 let result1 = 

identity<number>(42); // result1 的类型为 number let result2 = 

identity<string>('Hello'); // result2 的类型为 string

```
上图包含一个 identity 方法，我们在方法上定义了一个T的变量，T会捕获arg的类型，这样T就使用了该参数类型，之后我们定义T为函数的返回类型。这样在上诉定义中参数与返回类型就保持一致了。

下面是axios对泛型的应用：

```javascript
export class Axios {
  constructor(config?: AxiosRequestConfig);
  defaults: AxiosDefaults;
  interceptors: {
    request: AxiosInterceptorManager<InternalAxiosRequestConfig>;
    response: AxiosInterceptorManager<AxiosResponse>;
  };
  getUri(config?: AxiosRequestConfig): string;
  request<T = any, R = AxiosResponse<T>, D = any>(config: AxiosRequestConfig<D>): Promise<R>;
  get<T = any, R = AxiosResponse<T>, D = any>(url: string, config?: AxiosRequestConfig<D>): Promise<R>;
  delete<T = any, R = AxiosResponse<T>, D = any>(url: string, config?: AxiosRequestConfig<D>): Promise<R>;
  head<T = any, R = AxiosResponse<T>, D = any>(url: string, config?: AxiosRequestConfig<D>): Promise<R>;
  options<T = any, R = AxiosResponse<T>, D = any>(url: string, config?: AxiosRequestConfig<D>): Promise<R>;
  post<T = any, R = AxiosResponse<T>, D = any>(url: string, data?: D, config?: AxiosRequestConfig<D>): Promise<R>;
  put<T = any, R = AxiosResponse<T>, D = any>(url: string, data?: D, config?: AxiosRequestConfig<D>): Promise<R>;
  patch<T = any, R = AxiosResponse<T>, D = any>(url: string, data?: D, config?: AxiosRequestConfig<D>): Promise<R>;
  postForm<T = any, R = AxiosResponse<T>, D = any>(url: string, data?: D, config?: AxiosRequestConfig<D>): Promise<R>;
  putForm<T = any, R = AxiosResponse<T>, D = any>(url: string, data?: D, config?: AxiosRequestConfig<D>): Promise<R>;
  patchForm<T = any, R = AxiosResponse<T>, D = any>(url: string, data?: D, config?: AxiosRequestConfig<D>): Promise<R>;
}
```

上图是axios的类的定义，其中get，delete等请求方法都是用泛型函数来定义的。其主要原因是请求参数的范围和值不确定性。   

以get函数为例：   

```javascript
get<T = any, R = AxiosResponse<T>, D = any>(url: string, config?: AxiosRequestConfig<D>): Promise<R>;
```

a.<T = any, R = AxiosResponse<T>, D = any>: 这是函数的泛型部分。它引入了三个泛型类型参数：T、R 和 D。泛型允许函数在处理不同类型的数据时保持类型安全。   
 - T: 参数 T 表示从服务器接收到的响应数据类型。它有一个默认类型 any，这意味着如果调用者没有指定数据类型，它将默认为 any 类型。   
 - R: 参数 R 表示函数的返回类型。它的默认类型是 AxiosResponse<T>，这意味着函数将返回一个包含类型为 T 的数据的 Axios 响应对象。
 - D: 参数 D 表示可以与 GET 请求一起发送的请求数据类型。它的默认类型是 any，表示请求数据是可选的，如果提供了请求数据，它可以是任何类型。   

b.(url: string, config?: AxiosRequestConfig<D>): 这是函数的参数：   
 - url: 它是一个必需参数，类型为 string，表示将发送 GET 请求的 URL。
 - config: 它是一个可选参数，类型为 AxiosRequestConfig<D>。AxiosRequestConfig 是 Axios 提供的一个类型，在这里 D 表示可以与 GET 请求一起发送的数据的类型。

c.: Promise<R>: 函数返回一个 Promise，其类型为 R，R 的类型由泛型参数 R 决定。大多数情况下，它将是一个 AxiosResponse<T> 类型的对象。   

简而言之，这个函数签名允许你使用 Axios 发起 HTTP GET 请求，并且可以指定期望的响应数据类型 (T) 以及要发送的请求数据类型 (D)。返回的响应对象会被包装在一个 Promise 中，其返回类型默认为 AxiosResponse<T>。   

2.泛型接口  

泛型接口是在类定义中可以将一些不确定的类型抽离出来。通过使用泛型接口能够使一套类的定义更好的适配不同类型的数据。一

下是一个简单的例子:

```javascript
interface MyGenericObject<T> { key: string; value: T; } // 使用泛型对象
const strObject: MyGenericObject<string> = { key: "name", value: "John", };
const numObject: MyGenericObject<number> = { key: "age", value: 30, };
```

在axios的泛型类例子：

在泛型函数axios类的举例中我们看到了很多泛型接口的使用。下图为最常见的AxiosResponse<T>的定义：

```javascript
export interface AxiosResponse<T = any, D = any> {
  data: T;
  status: number;
  statusText: string;
  headers: RawAxiosResponseHeaders | AxiosResponseHeaders;
  config: InternalAxiosRequestConfig<D>;
  request?: any;
}
export interface InternalAxiosRequestConfig<D = any> extends AxiosRequestConfig<D> {
  headers: AxiosRequestHeaders;
}
```

以上泛型变量使用的过程为： 

a.<T = any, D = any> 为泛型定义的部分，定义变量T、D并设置默认值为any。   
b.用T标识date的响应的数据类型。如果未指定数据类型可以为任意数据类型。   
c.将变量D传递给泛型接口InternalAxiosRequestConfig表示config的请求数据类型。   

3.泛型对象  

理解了上面泛型函数以及泛型接口，泛型对象就很好解释了。即通过使用泛型，可以在对象的属性、方法或类中抽象出一些类型，并且在创建或使用对象时，指定具体的类型参数，从而使对象适用于不同类型的数据。直接上例子：

```javascript
class MyGenericClass<T> {
  private data: T;

  constructor(value: T) {
    this.data = value;
  }

  getValue(): T {
    return this.data;
  }
}
```

在这个例子中，我们定义了一个泛型类 MyGenericClass，它有一个类型参数 T。在类中的属性和方法都可以使用泛型类型参数 T，使得类可以适用于不同类型的数据。

axios中泛型对象的例子：

```javascript
export class AxiosError<T = unknown, D = any> extends Error {
  constructor(
      message?: string,
      code?: string,
      config?: InternalAxiosRequestConfig<D>,
      request?: any,
      response?: AxiosResponse<T, D>
  );

  config?: InternalAxiosRequestConfig<D>;
  code?: string;
  request?: any;
  response?: AxiosResponse<T, D>;
  isAxiosError: boolean;
  status?: number;
  toJSON: () => object;
  cause?: Error;
  static from<T = unknown, D = any>(
    error: Error | unknown,
    code?: string,
    config?: InternalAxiosRequestConfig<D>,
    request?: any,
    response?: AxiosResponse<T, D>,
    customProps?: object,
): AxiosError<T, D>;
  static readonly ERR_FR_TOO_MANY_REDIRECTS = "ERR_FR_TOO_MANY_REDIRECTS";
  static readonly ERR_BAD_OPTION_VALUE = "ERR_BAD_OPTION_VALUE";
  static readonly ERR_BAD_OPTION = "ERR_BAD_OPTION";
  static readonly ERR_NETWORK = "ERR_NETWORK";
  static readonly ERR_DEPRECATED = "ERR_DEPRECATED";
  static readonly ERR_BAD_RESPONSE = "ERR_BAD_RESPONSE";
  static readonly ERR_BAD_REQUEST = "ERR_BAD_REQUEST";
  static readonly ERR_NOT_SUPPORT = "ERR_NOT_SUPPORT";
  static readonly ERR_INVALID_URL = "ERR_INVALID_URL";
  static readonly ERR_CANCELED = "ERR_CANCELED";
  static readonly ECONNABORTED = "ECONNABORTED";
  static readonly ETIMEDOUT = "ETIMEDOUT";
}
```

### 泛型约束

在定义泛型变量的时候我们可以规定变量的类型局限在某个可定的范围称之为泛型约束。 常见的泛型约束：

1.使用 extends 关键字进行类型约束 泛型约束（Generic Constraints）是 TypeScript 中的一种功能，它允许我们对泛型类型参数进行限制，从而在泛型操作中应用特定的类型或属性约束。   

使用泛型约束，我们可以确保泛型类型参数符合某些要求，例如，具有特定的属性或属于某个类的实例。这有助于增加代码的类型安全性，避免潜在的错误，并在编译时捕获类型不符合要求的问题。   

下面是几个常见的泛型约束示例：   

1.使用 extends 关键字进行类型约束   

```javascript
// 定义一个泛型函数，要求传入的参数实现了 length 属性
function logLength<T extends { length: number }>(obj: T) {
  console.log(obj.length);
}

logLength("Hello"); // Output: 5
logLength([1, 2, 3]); // Output: 3
logLength({ length: 10 }); // Output: 10
logLength(42); // Error: Argument of type 'number' is not assignable to parameter of type '{ length: number }'.
```

2.使用 keyof 关键字进行属性约束

```javascript
// 定义一个泛型函数，要求传入的对象具有指定属性
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}

const person = { name: "John", age: 30 };
console.log(getProperty(person, "name")); // Output: "John"
console.log(getProperty(person, "age")); // Output: 30
console.log(getProperty(person, "email")); // Error: Argument of type '"email"' is not assignable to parameter of type '"name" | "age"'.
```

3.使用自定义接口约束 
 
```javascript
// 自定义一个接口作为泛型约束
interface Printable {
  print(): void;
}

// 定义一个泛型函数，要求传入的参数实现了 Printable 接口
function printItem<T extends Printable>(item: T) {
  item.print();
}

class Book implements Printable {
  print() {
    console.log("Printing book...");
  }
}

class Magazine implements Printable {
  print() {
    console.log("Printing magazine...");
  }
}

printItem(new Book()); // Output: "Printing book..."
printItem(new Magazine()); // Output: "Printing magazine..."
printItem({ print: () => console.log("Custom item") }); // Output: "Custom item"

```

### 重载

函数重载是一种允许我们为同一个函数提供多个不同的类型签名的特性。这样，在调用函数时，TypeScript 将根据传入的参数类型自动选择对应的函数实现。函数重载可以增加代码的类型安全性，同时提供更好的代码提示和可读性。   

函数重载的实现需要按照一定的规则来定义函数的多个类型签名。以下是函数重载的基本实现步骤：   

 - 定义函数的多个类型签名，每个签名包含不同的参数类型和返回类型。   
 - 实现函数体时，根据参数类型选择正确的实现。在实现部分，只需要提供一个实际的函数体，TypeScript 会根据传入的参数类型自动匹配对应的重载签名。

 下面是一个简单的函数重载示例，展示如何实现一个函数用于计算数组元素的总和：

```javascript
// 函数重载
function calculateSum(arr: number[]): number;
function calculateSum(arr: string[]): string;
function calculateSum(arr: any[]): any {
  let sum = 0;
  for (const num of arr) {
    sum += num;
  }
  return sum;
}

const numbersSum = calculateSum([1, 2, 3, 4, 5]);
console.log(numbersSum); // Output: 15

const stringsSum = calculateSum(['1', '2', '3', '4', '5']);
console.log(stringsSum); // Output: "12345"

```

在上面的示例中，我们定义了一个名为 calculateSum 的函数，它有两个函数签名，一个用于接受 number 类型的数组参数，另一个用于接受 string 类型的数组参数。然后，在实际的函数体中，我们对传入的数组进行遍历求和，并返回结果。TypeScript 会根据传入的参数类型自动选择对应的函数签名来执行。

通过函数重载，我们可以根据不同的参数类型提供不同的实现，从而实现更加灵活和类型安全的函数。   

重载在axios的使用例子：   

```javascript
export interface AxiosInstance extends Axios {
  <T = any, R = AxiosResponse<T>, D = any>(config: AxiosRequestConfig<D>): Promise<R>;
  <T = any, R = AxiosResponse<T>, D = any>(url: string, config?: AxiosRequestConfig<D>): Promise<R>;
}

// 重载函数的使用
export interface AxiosStatic extends AxiosInstance {
  create(config?: CreateAxiosDefaults): AxiosInstance;
  Cancel: CancelStatic;
  CancelToken: CancelTokenStatic;
  Axios: typeof Axios;
  AxiosError: typeof AxiosError;
  HttpStatusCode: typeof HttpStatusCode;
  readonly VERSION: string;
  isCancel: typeof isCancel;
  all: typeof all;
  spread: typeof spread;
  isAxiosError: typeof isAxiosError;
  toFormData: typeof toFormData;
  formToJSON: typeof formToJSON;
  CanceledError: typeof CanceledError;
  AxiosHeaders: typeof AxiosHeaders;
}

```

### 结尾

泛型和重载是 TypeScript 中强大而灵活的特性，它们共同提高了代码的类型安全性和灵活性。