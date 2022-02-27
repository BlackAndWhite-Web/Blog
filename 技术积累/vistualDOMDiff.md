# Virtual DOM Diff

## 什么是Virtual dom

真实一个DOM节点属性方法庞大，如果以真实DOM节点来做页面更新后的比较和绘制，会导致大量的重排重绘。利用js对象来代替表示DOM，该js对象就是Virtual DOM.而传统在页面更新中，需要比较的属性本来就不多，抽象出如下Virtual node 结构。

```html
<ul>
	<li class="item">1</li>
</ul>
```

```jsx
let virtualDOM = {
	tagName: 'ul',
	children: [
		{ tagName: 'li', props: { class:'item'}, children: ['1'}
	]
}
```

## 什么是diff算法

传统的tree的diff算法时间复杂度是O(n^3),  基于DOM操作，我们一般都是同层操作，很少涉及跨层移动节点。所以基于新旧virtual dom的diff比较，就是采用深度遍历同层比较。

- 深度遍历对比两个新老树，如果有不同，定义几个不同的类型(如：替换，修改属性，修改文本，移动等)， 每个节点做比较，将每个节点diff信息存储在patchs中，最后再将找出来的不同（patchs）应用在真实dom中做简单的修改。做到尽可能少的更新dom。
- 列表的key的作用，节点被复用；
    - 用index当key的问题：index做为key标记，起不到唯一性标识节点，在每次更新时，同一新旧节点不能得到复用；
    

## 同层children的比较

采用首尾指针

## 将找出的不同点渲染到真实DOM

将找出的差异，最后渲染到DOM; 

## 参考

- (15张图，20分钟吃透Diff算法核心原理，我说的！！！)[[https://juejin.cn/post/6994959998283907102#heading-10](https://juejin.cn/post/6994959998283907102#heading-10)]
- (深度剖析：如何实现一个 Virtual DOM 算法 #13)[[https://github.com/livoras/blog/issues/13](https://github.com/livoras/blog/issues/13)]