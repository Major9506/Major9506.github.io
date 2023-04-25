---
title: 如何更优雅的使用localstorage和sessionstorage
categories: 前端
tags: node
date: 2023-4-25
---

## 1 前言
localstorage和sessionstorage是前端开发时绕不开的两个本地存储方式，如何防止storage被滥用或者说更优雅的使用，以下是日常开发中的总结与分享。

### 一、优化思路及方法

1. 封装localStorage方法

封装localStorage方法可以帮助我们更好地管理localStorage。我们可以将常用的localStorage方法封装成一个对象，然后调用相应的方法即可。

```javascript
const Storage = {
  set(key, value) {
    localStorage.setItem(key, JSON.stringify(value));
  },
  get(key) {
    return JSON.parse(localStorage.getItem(key));
  },
  remove(key) {
    localStorage.removeItem(key);
  },
  clear() {
    localStorage.clear();
  }
}
```

2. 使用JSON.stringify和JSON.parse

localStorage只能存储字符串，如果我们要存储对象或数组，需要使用JSON.stringify将其转换为字符串，再使用JSON.parse将其转换为对象或数组。

```javascript
// 存储对象
const user = { name: '张三', age: 18 };
localStorage.setItem('user', JSON.stringify(user));

// 获取对象
const userStr = localStorage.getItem('user');
const userObj = JSON.parse(userStr);
console.log(userObj); // { name: '张三', age: 18 }
```

3. 设置过期时间

localStorage没有过期时间，如果我们要设置过期时间，需要手动实现。我们可以在存储数据时，同时存储一个过期时间，然后在获取数据时，判断是否过期。

```javascript
// 存储数据并设置过期时间（10秒）
const data = { name: '李四', age: 20 };
localStorage.setItem('data', JSON.stringify({
  value: data,
  expire: Date.now() + 10000
}));

// 获取数据并判断是否过期
const dataStr = localStorage.getItem('data');
if (dataStr) {
  const { value, expire } = JSON.parse(dataStr);
  if (Date.now() < expire) {
    console.log(value);
  } else {
    localStorage.removeItem('data');
  }
}
```

4. 使用try-catch

localStorage在某些情况下可能会抛出异常，比如用户禁用了localStorage或者localStorage已满。为了避免出现异常导致程序崩溃，我们可以使用try-catch来捕获异常。

```javascript
try {
  localStorage.setItem('key', 'value');
} catch (e) {
  console.log('存储失败');
}
```

5. 最大化利用localStorage容量

localStorage的容量有限，如果我们要存储大量数据，需要最大化利用localStorage容量。一种方法是使用压缩算法将数据压缩后再存储，另一种方法是分片存储数据，将数据分成多个小块分别存储。

```javascript
// 压缩存储数据
import LZString from 'lz-string';
const data = { name: '王五', age: 22 };
const compressedData = LZString.compress(JSON.stringify(data));
localStorage.setItem('data', compressedData);

// 解压缩获取数据
const compressedData = localStorage.getItem('data');
const dataStr = LZString.decompress(compressedData);
const dataObj = JSON.parse(dataStr);
console.log(dataObj); // { name: '王五', age: 22 }

// 分片存储数据
const data = { name: '赵六', age: 24 };
const dataStr = JSON.stringify(data);
const chunkSize = 1024 * 1024; // 每个分片的大小为1MB
const chunks = [];
for (let i = 0; i < dataStr.length; i += chunkSize) {
  chunks.push(dataStr.slice(i, i + chunkSize));
}
chunks.forEach((chunk, index) => {
  localStorage.setItem(`data-${index}`, chunk);
});

// 获取数据并合并
const chunks = [];
for (let i = 0; i < localStorage.length; i++) {
  const key = localStorage.key(i);
  if (key.startsWith('data-')) {
    chunks.push(localStorage.getItem(key));
  }
}
const dataStr = chunks.join('');
const dataObj = JSON.parse(dataStr);
console.log(dataObj); // { name: '赵六', age: 24 }
```

### 二、封装一个操作storage的库

以下是一个基于JavaScript的封装库，支持对localStorage和sessionStorage进行数据的读写操作，并提供了对称加密和过期时间的设置：

```javascript
class Storage {
  constructor(storageType) {
    this.storage = window[storageType];
  }

  set(key, value, options = {}) {
    const { expires, encrypt } = options;

    if (encrypt) {
      value = this._encrypt(value);
    }

    const data = {
      value,
      expires: expires ? new Date().getTime() + expires : null
    };

    this.storage.setItem(key, JSON.stringify(data));
  }

  get(key, options = {}) {
    const { defaultValue, decrypt } = options;

    const data = JSON.parse(this.storage.getItem(key));

    if (!data) return defaultValue;

    if (data.expires && new Date().getTime() > data.expires) {
      this.storage.removeItem(key);
      return defaultValue;
    }

    let value = data.value;

    if (decrypt) {
      value = this._decrypt(value);
    }

    return value;
  }

  remove(key) {
    this.storage.removeItem(key);
  }

  clear() {
    this.storage.clear();
  }

  _encrypt(value) {
    const key = CryptoJS.enc.Utf8.parse('my-secret-key');
    const iv = CryptoJS.enc.Utf8.parse('my-initialization-vector');
    const encrypted = CryptoJS.AES.encrypt(value, key, { iv });
    return encrypted.toString();
  }

  _decrypt(value) {
    const key = CryptoJS.enc.Utf8.parse('my-secret-key');
    const iv = CryptoJS.enc.Utf8.parse('my-initialization-vector');
    const decrypted = CryptoJS.AES.decrypt(value, key, { iv });
    return decrypted.toString(CryptoJS.enc.Utf8);
  }
}

// 使用示例
const storage = new Storage('localStorage');
storage.set('username', 'John', { expires: 60 * 60 * 1000, encrypt: true }); // 存储1小时后过期，并加密存储
const username = storage.get('username', { defaultValue: 'Guest', decrypt: true }); // 获取存储的数据，并解密读取
storage.remove('username'); // 删除存储的数据
storage.clear(); // 清空存储的所有数据
```

需要注意的是，这里的密钥和向量只是示例，实际使用时应该使用更加安全的密钥和向量，并且应该保证密钥的安全性。

### 三、常见的开源npm库

下面是几个操作Storage的常见npm库及其基本使用方法：

1. store

store是一个轻量级的本地存储库，可以在浏览器和Node.js环境中使用。store使用localStorage或sessionStorage作为存储介质，可以存储各种类型的数据，包括对象、数组、字符串等。store的API简单易用，可以方便地进行数据的增删改查操作。

安装：

```
npm install store
```

使用：

```javascript
import store from 'store';

// 存储数据
store.set('key', 'value');

// 获取数据
const value = store.get('key');
console.log(value);

// 删除数据
store.remove('key');

// 清空数据
store.clearAll();
```

2. local-storage-fallback

local-storage-fallback是一个基于localStorage和cookie的本地存储库，可以在不支持localStorage的浏览器中使用。local-storage-fallback使用cookie作为存储介质，可以存储各种类型的数据，包括对象、数组、字符串等。local-storage-fallback的API简单易用，可以方便地进行数据的增删改查操作。

安装：

```
npm install local-storage-fallback
```

使用：

```javascript
import storage from 'local-storage-fallback';

// 存储数据
storage.setItem('key', 'value');

// 获取数据
const value = storage.getItem('key');
console.log(value);

// 删除数据
storage.removeItem('key');

// 清空数据
storage.clear();
```

