---
title: return Promise.resolve(res.data)和return res.data的区别
date: 2018-03-11 12:05:53
tags:
- javaScript
categories:
- 问题记录
description: promise.then()的回调函数返回Promise.resolve(res.data)和直接返回res.data有何区别
---

# 问题

一个axios异步请求函数：

```javascript
export function getDiscList() {
  const url = '/api/getDiscList'

  const data = Object.assign({}, commonParams, {
    platform: 'yqq',
    hostUin: 0,
    sin: 0,
    ein: 29,
    sortId: 5,
    needNewCode: 0,
    categoryId: 10000000,
    rnd: Math.random(),
    format: 'json'
  })

  return axios.get(url, {
    params: data
  }).then((res) => {
    return Promise.resolve(res.data)
  })
}
```

组件里调用这个异步请求获取数据：

```javascript
getDiscList().then((res) => {
          if (res.code === ERR_OK) {
            this.discList = res.data.list
          }
        })
```

**为何在请求函数中，最后的then方法里return的是Promise.resolve(res.data)，而不是直接return res.data?**

**两种return对请求的调用有什么影响吗？**



# 思考

ok，一步一步分析：

1. 先搞清楚这个请求的返回是什么：

   ```javascript
   return axios.get(url, {
       params: data
     }).then((res) => {
       return Promise.resolve(res.data)
     })
   ```

   这一坨就是getDiscList这个函数返回的东西，axios是一个ajax请求第三方库，根据官方ReadMe可知道，axios.get()方法返回一个promise，并且这个promise的resolve回调函数的参数就是请求返回的数据res

2. 那么这个return相当于这样：

   ```javascript
   return new Promise((resolve) => {
     /*异步请求*／
     。。。
       if(/*请求成功*/){
            resolve(res)
          }
   }).then((res) => {
       return Promise.resolve(res.data)
   })
   ```

   现在return的就是new Promise().then()，为什么在请求函数里就then而不是在调用请求的时候再then？我猜是由于axios.get()这个promise返回的res不是作者需要的数据，res.data才是。因此作者在这里就先then一次，相当于对请求结果做个预处理，把真正需要的res.data传出去。这样在调用请求的时候直接拿到的就是res.data。起到简化代码的目的。

   那么问题来了，既然出于这样的目的，为什么作者不直接return res.data呢？return Promise.resolve(res.data)有什么别的用意吗？

3. 接着分析，现在知道return的是一个promise调用then()方法。那这个then()方法的返回结果又是什么？

   >then方法返回一个[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)，而它的行为与then中的回调函数的返回值有关：
   >
   >- 如果then中的回调函数返回一个值，那么then返回的Promise将会成为接受状态，并且将返回的值作为接受状态的回调函数的参数值。
   >- 如果then中的回调函数返回一个已经是接受状态的Promise，那么then返回的Promise也会成为接受状态，并且将那个Promise的接受状态的回调函数的参数值作为该被返回的Promise的接受状态回调函数的参数值。

   根据官方文档，then()方法返回的也是一个promise，并且这个promise的状态，resolve函数的参数和then()方法中的回调函数的返回值有关。

4. return res.data就是文档中所说的`如果then中的回调函数返回一个值`这种情况，return Promise.resolve(res.data)就是文档中所说的`如果then中的回调函数返回一个已经是接受状态的Promise`这种情况。

   因为Promise.resolve(res.data)返回的就是一个已经是接受状态的Promise

   > `Promise.resolve(value)`方法返回一个以给定值解析后的Promise对象。但如果这个值是个thenable（即带有then方法），返回的promise会“跟随”这个thenable的对象，采用它的最终状态（指resolved/rejected/pending/settled）；否则以该值为成功状态返回promise对象。

5. 既然两个return方式最终都是使then()返回接受状态的Promise，并且这个Promise的接受状态回调函数的参数都是res.data，那为何一定要采用return Promise.resolve(res.data)这种方式呢？

# 答案

`.then((res) => {return res.data})`和`.then((res) => {return Promise.resolve(res.data)})`返回的都是一个接受状态的promise`new Promise((resolve) => {resolve(res.data)})`，那我猜测作者之所以那么写，可能是他觉得这样看起来更严谨。