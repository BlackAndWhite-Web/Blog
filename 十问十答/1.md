## vue十问十答
> 1. 过滤器的作用，如何实现一个过滤器；

filter, 一般用于格式化文本，不会修改原数据，vue2中提供filter来自定义过滤器，vue3中移除了过滤器，建议使用computed或者metheds来处理数据。

> 2. 自定义指令，如何实现一个全局指令；

将一些对普通DOM元素的底层操作抽象出自定义指令， 如：v-permission, v-show等等；
```js
<template>
    <div v-fix="200"></div>
</template>

app.directive('fix', {
    mounted(el, binding) {
        el.style.position = 'fixed'
        el.style.top = binding.value + 'px'
    }
})
```
> 3. slot是什么？有什么用？原理是什么？

插槽, 用来做内容分发，将子组件中某一部分模版可以由调用方自定义；
原理： 将父组件中传递的内容存放在vm.$slot中；当渲染子组件时，遇到<slot>标签，替换父组件中的vm.$slot；

> 4. nextTick原理及作用？

nextTick:DOM更新之后，执行nexttick的回调，拿到最新的DOM;
vue基于js是单线程，浏览器的eventloop机制，创建了一个内部的异步任务队列；当改变数据的时候，不会直接更新视图；基于nextTick做了更新策略，做了性能优化。

> 5. keep-alive的理解，如何实现，缓存的是什么？

结合动态组件实现，在组件间切换时，可以不组件销毁，缓存第一次的组件实例；
组件生命周期中会多了一个actived的，unactived，来记录当前组件的激活和缓存态；

原理通过$slot拿到第一个vnode,判断缓存中是否存在该组件name的缓存；
```js
<keep-alive>
    <components :is="currentComponents" />
</keep-alive>
```

> 6. vue-router 的懒加载如何实现

懒加载：延迟加载(按需加载)
- 将不同的路由组件分块打包；如import, webpack在打包时会自动将其分割单独js;
- 在路由被访问到在加载对应的组件;

> 7. vue3有什么更新？

- 用ts将vue重写;
- 在依赖收集的监测机制：采用proxy取代了Object.definedProperty; 修复了数组索引，长度；对象属性增删中的hook;
- 参考react,将之前的组件写法采用option api; 现在采用了compsition API，可以将逻辑得到复用，少依赖this，为使用者提供了更多的灵活性。
- 模版上面，将唯一的根节点，可以使用多个根节点；

> 8. vue3中的compsition API和React Hook区别是什么？
- vue中的setup只会被执行一次; 而react中组件刷新，整个组件函数会被重新执行；
- react hook在使用时会有一些规则：只能在最顶层使用hook，不能在循环，条件或者嵌套函数中调用hook; vue中没有这个限制；
- vue中的响应式系统自动实现了依赖收集，而react hook中(useEffect useMemo)需要手动传入依赖
> 9. template和jsx有什么区别？

- jsx模版其实是一种类似于XML的模版语言，在通过babel，转译成js,所以其中的很多操作都可以使用js来操作，使得操作模版更加的简单；
- vue的模版，是vue自定义的，类似于html，暴露vue自定义的一些有限的指令来操作模版，需要记一些额外的指令api,并且指令是有限的；

> 10. vue和react的理解和异同？

- react推崇数据的不可变的；vue中思想是响应式的，基于数据是可变的；导致react中的性能优化需要手动去做，vue中是下沉到框架中去实现了。
- jsx vs templates;



