---
title: vue3与vue2对比学习
categories: 前端
tags: node
date: 2023-7-13
---

## 1 前言
Vue2 是选项API（Options API），一个逻辑会散乱在文件不同位置（data、props、computed、watch、生命周期钩子等），导致代码的可读性变差。当需要修改某个逻辑时，需要上下来回跳转文件位置。   

Vue3 组合式API（Composition API）则很好地解决了这个问题，可将同一逻辑的内容写到一起，增强了代码的可读性、内聚性，其还提供了较为完美的逻辑复用性方案。   

### 一、vue2与vue3语法糖对比学习   
1、模板语法   
Vue3的模板语法和Vue2的类似，但有一些新的语法糖，如Teleport和Suspense。以下是Vue2和Vue3的模板语法对比：   
```javascript
<!-- Vue2 -->
<div v-if="isVisible">{{ message }}</div>

<!-- Vue3 -->
<template v-if="isVisible">
  <div>{{ message }}</div>
</template>

<!-- Vue2 -->
<div v-for="item in list" :key="item.id">{{ item.name }}</div>

<!-- Vue3 -->
<div v-for="item in list" :key="item.id">{{ item.name }}</div>

<!-- Vue2 -->
<div v-bind:class="{ active: isActive }"></div>

<!-- Vue3 -->
<div :class="{ active: isActive }"></div>

<!-- Vue3 -->
<teleport to="body">
  <div>Teleport to body</div>
</teleport>

<!-- Vue3 -->
<suspense>
  <template #default>
    <div>{{ message }}</div>
  </template>
  <template #fallback>
    <div>Loading...</div>
  </template>
</suspense>
```
2、Composition API   
Vue3引入了Composition API，它是一种新的方式来组织和重用组件逻辑。以下是Vue2和Vue3的组件定义方式对比：
```javascript
// Vue2 Options API
export default {
  data() {
    return {
      message: 'Hello, Vue2'
    };
  },
  computed: {
    reversedMessage() {
      return this.message.split('').reverse().join('');
    }
  },
  methods: {
    greet() {
      alert(this.message);
    }
  }
};

// Vue3 Composition API
import { reactive, computed } from 'vue';

export default {
  setup() {
    const state = reactive({
      message: 'Hello, Vue3'
    });

    const reversedMessage = computed(() => {
      return state.message.split('').reverse().join('');
    });

    function greet() {
      alert(state.message);
    }

    return { state, reversedMessage, greet };
  }
};
```
3、Slots
Vue3中的Slots有一些新的语法糖，如默认插槽和具名插槽的新写法。以下是Vue2和Vue3的Slots对比：
```javascript
<!-- Vue2 -->
<template>
  <div>
    <slot></slot>
  </div>
</template>

<!-- Vue3 -->
<template>
  <div>
    <slot></slot>
  </div>
</template>

<!-- Vue2 -->
<template>
  <div>
    <slot name="header"></slot>
    <slot></slot>
    <slot name="footer"></slot>
  </div>
</template>

<!-- Vue3 -->
<template>
  <div>
    <slot name="header" />
    <slot />
    <slot name="footer" />
  </div>
</template>
```
### 一、vue2与vue3分别实现一个todolist
Vue2实现todolist
```javascript
<template>
  <div>
    <h2>Vue2 Todo List</h2>
    <form @submit.prevent="addTodo">
      <input type="text" v-model="newTodo" placeholder="Enter a new todo">
      <button type="submit">Add</button>
    </form>
    <ul>
      <li v-for="(todo, index) in todos" :key="index">
        {{ todo }}
        <button @click="deleteTodo(index)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      todos: [],
      newTodo: ''
    };
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim() !== '') {
        this.todos.push(this.newTodo.trim());
        this.newTodo = '';
      }
    },
    deleteTodo(index) {
      this.todos.splice(index, 1);
    }
  }
};
</script>
```
Vue3实现todolist   
```javascript
<template>
  <div>
    <h2>Vue3 Todo List</h2>
    <form @submit.prevent="addTodo">
      <input type="text" v-model="newTodo" placeholder="Enter a new todo">
      <button type="submit">Add</button>
    </form>
    <ul>
      <li v-for="(todo, index) in todos" :key="index">
        {{ todo }}
        <button @click="deleteTodo(index)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
import { reactive } from 'vue';

export default {
  setup() {
    const state = reactive({
      todos: [],
      newTodo: ''
    });

    function addTodo() {
      if (state.newTodo.trim() !== '') {
        state.todos.push(state.newTodo.trim());
        state.newTodo = '';
      }
    }

    function deleteTodo(index) {
      state.todos.splice(index, 1);
    }

    return { state, addTodo, deleteTodo };
  }
};
</script>
```