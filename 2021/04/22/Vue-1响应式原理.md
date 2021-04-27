## Vue-1

### 目录结构

-- benchmarks &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// 性能、基准测试<br>
-- dist &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// 构建打包的输出目录<br>
-- examples &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// 案例目录<br>
-- flow &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// flow语法的类型声明<br>
-- packages  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//相关的包文件<br>
-- scripts &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//所有配置文件的存放地点<br>
-- src &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// vue源码目录<br>
&nbsp;&nbsp;&nbsp;&nbsp;-- compiler &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// 编译器<br>
&nbsp;&nbsp;&nbsp;&nbsp;-- core &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// 运行时的核心包<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-- components &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // 全局组件，如keep-alive<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-- config.js &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // 默认配置<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-- global-api &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// 全局api，如Vue.use,Vue.component<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-- instance &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// Vue实例相关，如Vue的构造函数<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-- observer &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// 响应式原理<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-- util &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// 工具方法<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-- vdom &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// 虚拟DOM相关，如patch算法<br>
&nbsp;&nbsp;&nbsp;&nbsp;-- platforms &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// 平台相关的编译器代码<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-- web &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-- weex &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br>
&nbsp;&nbsp;&nbsp;&nbsp;-- server &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// 服务器渲染相关<br>
-- test &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// 测试目录<br>
-- types &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// TS类型声明<br>

### 响应式原理
在源码里面，数据响应式的处理是在初始化的过程中，即在入口函数里面的initState方法中，文件路径是`src\core\instance\init.js`<br>
在init.js文件中，如下图所示，可以看到调用了很多初始化方法，那数据响应式主要是initState()方法里面（该方法所在文件`src\core\instance\state.js`）<br>
在该文件里面，分别对prop，methods，computed，data，watch等数据进行响应式处理。本文主要用data来做说明。<br><br>
1、拿到所有的props属性，遍历所有的prop对象中的对象，给prop中的所有对象设置响应式，主要方法 `defineReactive`，该方法所在文件路径`src\core\observer\index.js`,在该方法中通过Object.defineProperty()方法设置响应式，在该方法中有get和set两个两个方法，其中get方法会拦截所有对象的读取操作，而set方法则是拦截对象的设置操作<br><br>
(1)get方法核心代码<br>
```
get: function reactiveGetter () {
  const value = getter ? getter.call(obj) : val
  // Dep.target是Dep类的一个静态属性，值为watcher。在实例化watcher时会被设置
  // 实例化watcher时会执行new Warcher时传递的回调函数
  // 而回调函数中如果有vm.key的读取行为，则会触发这里的读取拦截，从而进行依赖手机
  if (Dep.target) {
    // 依赖收集，在dep中添加watcher，同时也在watcher中添加dep
    dep.depend()
    // childOb表示对象中嵌套对象的观察者对象，如果存在也对其进行依赖收集
    if (childOb) {
      childOb.dep.depend()
      // 如果obj[key]是数组，则触发数组的响应式
      if (Array.isArray(value)) {
        // 为数组项为对象的项添加依赖
        dependArray(value)
      }
    }
  }
  return value
}
```
(2)set核心方法
```
set: function reactiveSetter (newVal) {
  // 获取之前的value值
  const value = getter ? getter.call(obj) : val
  /* eslint-disable no-self-compare */
  // 如果新值和旧值一样，则不更新，不触发响应式更新过程
  if (newVal === value || (newVal !== newVal && value !== value)) {
    return
  }
  /* eslint-enable no-self-compare */
  if (process.env.NODE_ENV !== 'production' && customSetter) {
    customSetter()
  }
  // #7981: for accessor properties without setter
  // 不存在setter。则表示只读，则直接return
  if (getter && !setter) return
  if (setter) {
    setter.call(obj, newVal)
  } else {
    val = newVal
  }
  // 对新值进行观察，让新值也是响应式的
  childOb = !shallow && observe(newVal)
  // 依赖通知更新
  dep.notify()
}
})
```
注：在get方法中，需要对对象进行递归调用，为了让所有的对象的key都能被响应式处理，并在对数组需要单独进行响应式处理<br><br>
总结： Vue响应式原理，它的核心就是通过Object.defineProperty()方法拦截数据访问和设置，那么响应式的数据分为哪些呢？它主要分为两类，一个是数组，一个是对象<br><br>
(1) 对象：遍历所有的对象属性，给每个属性设置getter和setter，从而达到拦截访问和设置的目的，如果属性值也是对象，则递归调用设置getter和setter，在数据访问的时候进行依赖收集，在dep中存储相关的watcher，在设置数据的时候通过相关的watcher去进行更新<br><br>
(2)数组： 通过拦截数组的7个可以更改自身的原型方法，从而实现响应式，在添加数据的时候进行响应式处理，然后由dep通知watcher去更新，如果是删除的话，也要由dep通知watcher去更新
【注】：数组的7个方法分别是push,pop,shift,unshift,splice,sort,reverse