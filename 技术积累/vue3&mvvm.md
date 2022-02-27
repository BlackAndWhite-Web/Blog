# vue3&mvvm框架

## vue3

- ref  reactive的区别；
- reactive ref这些对数组赋值的区别；
- computed; props;的传递和响应式；

## vue响应式原理

- 会拦截data返回对象上的属性一一会实例化dep来收集依赖，组件mount的时候会初始化一个watcher，watcher是Dep和组件的桥梁；该属性如果修改，会统一notify之前收集依赖的各个组件。

## 注意点：

- vue3使用proxy,对于对象和数组都不能直接赋值，否则会失去响应式；

```jsx
const state = reactive({
	arr: []
});
state.arr = [1,2,3];

const state = ref([]);
state.value = [1,2,3];

```

- 原则来说：ref基本是给基本类型；reactive是引用类型；但是ref的响应式也是通过reactive来实现的；

```jsx
const state = ref(1) => const state = reactive({ value: 1})

```

- toRef 和 toRefs
    
    可以为源响应式对象的属性，创建一个新的ref;ref的数据不是原始数据的拷贝，而是引用
    
    ```jsx
    const { mytitle } = toRefs(props)
    console.log(mytitle)
    ```
    
- 什么值需要设置为响应式
    
    视图上需要展示的数据；
    
    props传递下去，子组件可以toRef, 维持同一个引用，保持响应式。？
    
- Map WeakMap区别
    - map和对象，对象是用字符串做key的；map可以用任何类型做key;
    - map和weakmap, 都是集合的一种表述map中的 key是强映射；weakmap的key是弱映射；
- new Proxy中的receiver是被代理后的对象；
    
    ```jsx
    const person = { name: '林三心', age: 22 }
    
    const proxyPerson = new Proxy(person, {
        get(target, key, receiver) {
            return target[key]
        },
        set(target, key, value, receiver) {
            target[key] = value
        }
    })
    
    console.log(proxyPerson.name) // 林三心
    
    proxyPerson.name = 'sunshine_lin'
    
    console.log(proxyPerson.name) // sunshine_lin
    
    ```
    

## 问题

- 响应式：如果模版不需要渲染，prop只是做传递使用，定位为响应式还是非响应式；对于 props定义的是响应式的，如果数据修改，子组件重新渲染？

## todo

- 插槽；slot;
- nextTick;
    - 获取到 下一次diff后的值；
    - 什么时候用；
- [x]  Map weakMap区别
- proxy.的第四个参数 receiver;  reflect 中的target 和receiver;

## 参考

- [响应式原理]([https://zhuanlan.zhihu.com/p/88648401](https://zhuanlan.zhihu.com/p/88648401))

 【**浅析 Map 和 WeakMap 区别以及使用场景】**([https://cloud.tencent.com/developer/article/1862103](https://cloud.tencent.com/developer/article/1862103))

[**林三心画了8张图，最通俗易懂的Vue3响应式核心原理解析**]([https://juejin.cn/post/7001999813344493581#heading-15](https://juejin.cn/post/7001999813344493581#heading-15))