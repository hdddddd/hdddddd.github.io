<!--
 * @Description: 手写实现call、apply和bind
 * @Version: 2.0
 * @Autor: huangdi8
 * @Date: 2021-04-14 15:25:48
 * @LastEditors: huangdi8
 * @LastEditTime: 2021-04-14 16:41:46
-->
### 手写实现call、apply和bind

#### 前言
  在日常工作中，常常会用到this这个关键字，在一些工作中希望能够改变this的指向，那call、apply和bind最常被用来改变函数this的指向问题。接下来说说它的原理。

#### 概述
  首先说说apply和call，它们的共同点是能够改变函数执行的上下文，将一个对象的方法交给另一个对象去执行，并且是立即执行，所以要调用apply或者call方法，则必须要是一个函数。那它们的区别主要是在参数上，call方法接收的是一个参数列表，而apply方法接收的是一个数组
  ```
  function call(thisArgs, args1, args2, ...)

  function apply(thisArgs, [argsArray])
  ```
  在上列的两个示例中，this会指向thisArgs对象
  bind方法会返回一个新的函数，永久的改变this指向的函数，因此它改变this的指向之后不会立即执行
  ```
  function bind(thisArgs, args1, args2, ...)
  ```
  在上栗中this指向thisArgs对象，它返回的是一个新的函数，并拥有this值和初始参数

#### 手写实现
  1、call的实现<br>
  ```
  Function.prototype.my_call = function(obj) {
    if(obj === null || obj === undefined) {
      obj = window
    } else {
      obj = Object(obj)
    }
    // 创建一个临时变量，临时存储函数，并将this指向该函数
    obj.fn = this
    // 将arguments类数组转为数组
    let args = [...arguments].slice(1)
    // 将参数赋值给该函数
    let result = obj.fn(...args)
    delete obj.fn
    return result
  }
  ```
  2、apply的实现<br>
  ```
  Function.prototype.my_apply = function(obj, arr) {
    if(obj === null || obj === undefined) {
      obj = window
    } else {
      obj = Object(obj)
    }
    obj.fn = this
    let result
    if (!arr) {
      result = obj.fn()
    } else {
      result = obj.fn(...arr)
    }
    delete obj.fn
    return result
  }
  ```
  3、bind的实现<br>
  ```
  Function.prototype.my_bind = function(obj) {
    if (typeof this !== "function") {
      throw new Error("Function.prototype.bind - what is trying to be   bound is not callable")
    }
    let args = [...arguments].slice(1)
    // 将源函数存储起来
    let fn = this
    let fn_ = function() {}
    let bound = functon () {
      let params = [...arguments]
      // 判断是否是bound的实例，是则指向this，否则指向obj
      const isNew = this instanceof bound
      fn.apply(isNew ? this : obj, args.concat(params))
    }
    // 复制源函数的prototype给bound
    bound.prototype = Object.create(fn.prototype)
    return bound
  }
  ```
