---
layout: pages
title: Vuex 学习之路
date: 2018-04-01 17:05:03
tags:
      - 前端框架
categories: 前端
---
# Mutation ✨
更改 Vuex 的store 状态的唯一方法是提交 mutation
```js
const store = new Vuex.store({
    state: {
        count: 1
    },
    mutations: {
        increment(state) {   //这里 increment 是 Mutation 的一个类型（type）
            //变更状态
            state.count++
        }
    }
});
```
但不能直接调用 mutation handler，需要以相应的 type 调用 **store.commit** 方法：
```js
store.commit('increment')
```
### Mutation 必须是同步的
举个栗子 🌰
```js
mutations: {
    increment() {
        api.callAsyncMethod(() => {
            state.count++;
    }
}
```
在回调函数内部的任何状态的变化是追踪不到的

# Action ✨
Action 类似于 Mutation，但是不同的地方在于：

1. Action 提交的是 Mutation，而不是直接改变状态

2. Action 可以包含任意异步操作

注册一个简单的 Action:
```js
const store = new Vuex.store({
    state: {
        count: 0
    },
    mutations: {
        increment(state) {
            state.count++;
        }
    },
    action: {
        increment(context) {
            context.commit('increment');
        }
    }
});
```
Mutation 通过 store.commit()来提交， Action 则通过 store.dispatch() 来触发
```js
store.dispatch('increment');
```
