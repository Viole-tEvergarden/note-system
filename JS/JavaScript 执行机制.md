## JavaScript 执行机制
>受益于 [这一次，彻底弄懂 JavaScript 执行机制](https://juejin.cn/post/6844903512845860872)
>
![](https://user-gold-cdn.xitu.io/2017/11/21/15fdd88994142347?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

导图要表达的内容用文字来表述的话：

-   同步和异步任务分别进入不同的执行"场所"，同步的进入主线程，异步的进入Event Table并注册函数。
-   当指定的事情完成时，Event Table会将这个函数移入Event Queue。
-   主线程内的任务执行完毕为空，会去Event Queue读取对应的函数，进入主线程执行。
-   上述过程会不断重复，也就是常说的Event Loop(事件循环)。

```JavaScript
setTimeout(function() { 
	console.log('setTimeout'); 
}) 
new Promise(function(resolve) { 
	console.log('promise'); 
	resolve()
}).then(function() { 
	console.log('then'); 
}) 
console.log('console');

// promise console  then setTimeout
```
因为 `then`函数直接分发到微任务Event Queue
而 setTimeout 需要先去 Event Table 注册 然后才在过了0秒之后 进入 Event Queue
