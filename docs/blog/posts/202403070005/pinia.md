
---
authors:
  - 3zhuyuning
date: 2024-03-7
tags:
  - Vue
categories:
  - Frontend
---


# Pinia学习笔记
- [Pinia学习笔记](#pinia学习笔记)
  - [前前前言](#前前前言)
  - [什么是Pinia](#什么是pinia)
    - [store与Pinia的关系](#store与pinia的关系)
  - [Pinia的配置](#pinia的配置)
  - [Pinia的使用](#pinia的使用)
    - [前言](#前言)
    - [State部分](#state部分)
      - [使用mutations来修改state](#使用mutations来修改state)
      - [使用actions来修改state](#使用actions来修改state)
    - [Getter部分](#getter部分)
      - [向 getter 传递参数](#向-getter-传递参数)
    - [Action部分](#action部分)
  - [该笔记中用到所有例子整合的index.js文件代码](#该笔记中用到所有例子整合的indexjs文件代码)
## 前前前言
该学习笔记只最粗略的讲述Pinia的各项功能应如何实现，**所有想法观点仅代表个人理解，不一定百分百正确**，笔记中使用的代码均使用过或来自官方文档(部分代码来自ChatGpt)，且官方代码诸如变量的命名均已修改为我之前使用的样例一致，结尾的index.js文件代码复制即用，但是如有报错别骂我，我心里脆弱，可能是哪里cv错了
## 什么是Pinia
在迎来Vue3的更新后，Pinia是尤雨溪强推的一个项目，与Vue2时期的VueX相比，Pinia使用起来更为简便,以下是Vue官方对Pinia的介绍：
<!-- more -->



> Pinia 是 Vue 的专属状态管理库，它允许你跨组件或页面共享状态。

就我个人使用下来的理解是，Pinia的作用就是允许你在Vue项目中使用全局变量，更简单的满足使用者跨组件传数据的需求而不是再使用prop等来跨组件传递数据，该笔记仅讲述如何使用Pinia来满足自己的相关需求，而不讲述更深层的逻辑之类，直接跟着该笔记中的步骤走即可实现较基本的Pinia功能(cv加改相关变量名即可)

### store与Pinia的关系
在学习的使用Pinia的过程中总是会出现Store这个词，以下是gpt的回答
>在前端应用中，store 是一个用于管理应用状态的对象或者实例。在 Vue.js 中，通常会使用 Vuex 或者 Pinia 来实现状态管理，而这些状态管理库中的核心概念就是 store。

我个人理解为store就是负责储存应用状态即各种共享变量即函数的一个容器，而Pinia就是实现Store这一概念的工具
## Pinia的配置
与Router相同，Pinia的使用同样需要在相关文件中去配置，但是其实在创建Vue项目的时候终端会有提供是否配置Pinia的选项，如果你已经配置好了，请跳转到[Pinia的使用](#pinia的使用)部分
如果你还没有进行Pinia的配置，请按以下简单的步骤进行Pinia的配置：
1.首先，用你喜欢的包管理器安装Pinia:
```
yarn add pinia
# 或者使用 npm
npm install pinia
```

2.在Vue项目的src下创建store文件夹(规范命名)，并在store(s)文件夹下创建index.js文件(或其他名字,笔记以index.js为例)，在index.js文件中：
```
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
  state: () => {
    return {
      //在该处存储你的state状态(即全局变量)
      username:'aaa'，
      users: [],
      count:1,
      userData;null, //留空用于储存用户数据
    }
  }
})
```
注意以上的**useUserStore**与**user**均为使用者自定义的ID
>user:在示例index.js中是该store的名字

>useUserStore:在示例index.js中是通过defineStore方法创建的变量，其中包含了使用者在**store**中定义的状态、getters、actions等(Pinia官方文档建议该变量应以use开头Store结尾)。

3.最后我们在main.js文件中完成挂载即可:
```
import { createPinia } from 'pinia'
const pinia = createPinia()
app.use(pinia)
```
## Pinia的使用
### 前言
结合Vue官方文档，我个人将Pinia分为下面三个主要部分(仅代表个人观点)，同样接下来将讲述如何使用这三个部分来实现使用者的需求。
### State部分
State我个人认为就是储存Vue项目全局变量的地方，你可以在该部分设定你想在不同组件高频率使用的数据，如基本的用户信息或者后端传过来的token，State分为在index.js的初始化及在Vue组件中使用两部分来说明，同样只讲述我目前所涉及到的较简单的需求。
1.在index.js中的配置：
```
state: () => {
    return {
    //你可以在return中存储你想在全局使用的数据，按以下形式存储你需要的数据即可
    //注意通常并不需要声明数据类型，Pinia会自动推断数据类型
    //如果你想规定State返回的数据类型也是可以做到的，在Pinia官方文档的State-Typescript部分有具体例子，该处不做详细介绍
      username:'aaa',
      users: [],
      count:1,
    }
  }
```
2.以/src/views/data.vue组件为例，展示如何在组件中读取username变量:
```
<script setup>
import { useUserStore } from '@/stores/index';//引入我们在index.js文件中创建的Store

const store = useUserStore() //创建store实例
store.username = 'aaa' //读取store实例中的username属性
```
```
<templete>
{{ store.username }}
```
虽然在Vue的官方实例中可以直接修改State中的数据如下：
```
const store = useStore()

store.count++
```
我在询问ChatGpt后得到的答案如下:
>在 Vue 组合式 API 中，useStore 函数返回的是一个引用到 Pinia store 实例的代理对象。通过这个代理对象，您可以直接访问和修改 store 中的状态数据，包括 state 中的数据。这种直接访问和修改 state 的方式是 Vue 组合式 API 的一个特性，它是为了简化开发流程，使得在组件中访问和修改 state 更加便捷。

>在组合式 API 中，您可以直接通过代理对象访问和修改 state 中的数据，而不需要使用 mutations 或 actions。这样可以减少冗余的代码，并且使得代码更加清晰和简洁。

>然而，需要注意的是，虽然直接访问和修改 state 中的数据在某些情况下很方便，但这种方式可能会导致状态的变化不可追踪，并且破坏了单向数据流的原则。因此，在开发大型应用或者需要更严格的数据管理时，推荐使用 mutations 或 actions 来修改 state，这样能够更好地追踪状态的变化，并且使得代码更加可维护和可预测。

大意是不安全(我也不是很看的明白)因此我个人认为还是通过1.使用mutations 2.使用actions两种方法来修改State较好
#### 使用mutations来修改state
index.js部分:
```
// 在 store 中定义 mutation
const useUser = defineStore('user', {
  state: () => ({
    username: ''
  }),
  mutations: {
    setUsername(state, newUsername) {
      state.username = newUsername;
    }
  }
});
```

在组件中:
```
<template>
  <button @click="updateUsername">更新用户名</button>
</template>

<script setup>
import { useUserStore } from '@/stores/index'

const store = useUserStore(); // 获取store实例

const updateUsername = () => {
  store.commit('setUsername', 'newUsername'); // 提交名为setUsername的mutation，并传递'newUsername'作为参数
}
</script>
```

#### 使用actions来修改state
在index.js中(该代码我并未完全理解，摘自gpt，故不放在末尾index.js的代码中了):
```

import axios from 'axios';

const useUser = defineStore('user', {
  state: () => ({
    username: ''
  }),
  actions: {
    async fetchUser() {
      try {
        // 发送网络请求
        const response = await axios.get('https://api.example.com/user');
        const data = response.data;

        // 提交 mutation 来修改 state
        this.setUsername(data.username);
      } catch (error) {
        console.error('Error fetching user:', error);
        // 处理错误
      }
    },
    setUsername(newUsername) {
      // 提交 mutation 来修改 state
      this.$patch({ username: newUsername });
    }
  }
});
```
综上，通常在Action中执行异步操作(目前我只知道一个网络请求)，再使用mutation修改state状态，因此，简单的操作使用mutation即可，复杂的操作使用Action来修改state的状态

### Getter部分
>Getter 完全等同于 store 的 state 的计算值。可以通过 defineStore() 中的 getters 属性来定义它们。

这是Pinia官方文档中对Getter的介绍，我个人认为Getter就是一个以State为参数的函数，它将返回一个对State的计算值，但是我目前并未具体用到需要使用Getter的需求，因此接下来介绍的是最最最基本的Getter的使用。
1.在index.js中配置Getter(Pinia官方文档的代码，我觉得已经非常明了就直接copy过来)：
```
getters: {
    // 自动推断出返回类型是一个 number
    //在此处可以看出Getter是一个接收state为参数的函数，它将返回一个state的计算值
    doubleCount(state) {  
      return state.count * 2
    },
    // 返回类型**必须**明确设置
    //此处可看出它也可以不依赖state来使用
    doublePlusOne(): number {
      // 整个 store 的 自动补全和类型标注 ✨
      return this.doubleCount + 1
    },
  },
```

2.在组件中使用Getter:
```
<templete>
{{store.doubleCount}}
{{store.doublePlusOne}}

<script setup>
import { useUserStore } from '@/stores/index';
const store = useUserStore()
```
Getter不仅仅是只能做简单的数学运算，Getter本身虽然只能接收state或者不接收参数，但它返回的函数可以接收外部的参数，因此可以实现组件向Getter传递参数的功能，以下将对Pinia官方文档的**向 getter 传递参数**部分做个人解读
#### 向 getter 传递参数
```
export const useUserStore = defineStore('user', {
  state:() => {
    users:[] //users为一个表单，自行补充上相关数据带有id属性的元素user
  },
  getters: {
    getUserById: (state) => {
      return (userId) => state.users.find((user) => user.id === userId)
    },
  },
})
```
对getUserById的return部分做研究，发现其返回一个接收参数**userId**的无名函数，我们将该函数拆解开来理解：
```
(userId) => state.users.find((user) => user.id === userId)
```
为无名函数加个名字MatchId:
```
const MatchId = (userId) => {
    return state.users.find((user) => user.id === userId)
}
```
ps.其中的find为Javascript数组的一个内置函数用于查找数组中满足条件的第一个元素，并返回该元素，若找不到则返回undefined，格式如下:
```
array.find(callback(element[, index[, array]])[, thisArg])
callback：一个用于测试每个元素的函数，它可以接受三个参数：
element：当前正在被处理的元素。
index（可选）：当前正在处理的元素的索引。
array（可选）：调用 find 方法的数组。
thisArg（可选）：在执行 callback 函数时使用的 this 值。
```
接下来单独看find函数部分
```
state.users:被遍历的数组
(user) => user.id === userId:回调函数，用于匹配最开始接收的参数userId是否在users表单中，若匹配则返回匹配的user.id，若不匹配则返回undefined
```
结合以上我们可以理清通过Getter检验userId的逻辑:返回一个函数来接收userId将其投入find函数中遍历检验其是否在users表单中，实现用户ID验证的功能
接下来是其在组件中如何使用(copy自Pinia的官方文档，并结合该笔记中的例子做部分修改):
```
<script setup>
import { useUserStore } from '@/stores/index'
const store = useUserStore()
const { getUserById } = storeToRefs(store) //将Getter getUserById变为响应式对象，方便其在组件中使用
// 请注意，你需要使用 `getUserById.value` 来访问
// <script setup> 中的函数
</script>

<template>
  <p>User 2: {{ getUserById(2) }}</p>
</template>
```
### Action部分
>类似 getter，action 也可通过 this 访问整个 store 实例，并支持完整的类型标注(以及自动补全✨)。不同的是，action 可以是异步的，你可以在它们里面 await 调用任何 API，以及其他 action！

以上是Pinia官方文档中对Action的介绍，我个人总结下来认为它的特点在它是异步的，这使得它可以完成更复杂的操作，我个人认为前后端交接的大部分数据都是通过通过Action来完成储存的
与Getter相似，Action中存储函数，不同的是它是异步函数，因此要声明为async函数(以官方文档的registerUser函数为例):
```
import { mande } from 'mande'

const api = mande('/api/users')//mande函数将axios函数变为一个实例'/api/users'为对应的url

export const useUserStore = defineStore('user', {
  state: () => ({
    userData: null,
    // ...
  }),

  actions: {
    async registerUser(login, password) {
      try {
        this.userData = await api.post({ login, password })//在该处调用HTTP库的post函数并在接收数据后将其赋给userData储存在store中
        showTooltip(`Welcome back ${this.userData.name}!`) //这里的showTooltip为Vue官方编写的一个方法
      } catch (error) {
        showTooltip(error)
        // 让表单组件显示错误
        return error
      }
    },
  },
})
```
以及该函数在组件中的使用:
```
<template>
  <button @click="registerUser">注册用户</button>
</template>

<script setup>
import { useUserStore } from '@/stores/index';

const { registerUser } = useUserStore();
</script>

```

## 该笔记中用到所有例子整合的index.js文件代码
```
import { defineStore } from 'pinia'

import { mande } from 'mande'

const api = mande('/api/users')

export const useUserStore = defineStore('user', {
  state: () => {
    return {
      //在该处存储你的state状态(即全局变量),以username为例
      username:'aaa'，
      users: [],
      count:1,
      userData;null, //留空用于储存用户数据
    }
  },
  getters: {
    doubleCount(state) {  
      return state.count * 2
    },
    doublePlusOne(): number {
      return this.doubleCount + 1
    },
    getUserById: (state) => {
      return (userId) => state.users.find((user) => user.id === userId)
    },
  },
  mutations: {
    setUsername(state, newUsername) {
      state.username = newUsername;
    }
  }，
  actions: {
    async registerUser(login, password) {
      try {
        this.userData = await api.post({ login, password })
        showTooltip(`Welcome back ${this.userData.name}!`) 
      } catch (error) {
        showTooltip(error)
        return error
      }
    },
  },
})
```