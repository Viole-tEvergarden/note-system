# 理解 Javascript 中的 async/await
> 原文 思否中 边城的同名文章[[理解 JavaScript 的 async/await](https://segmentfault.com/a/1190000007535316)

## 1. async 和 await 在干什么
任意一个名称都是有意义的，先从字面意思来理解。async 是“异步”的简写，而 await 可以认为是 async wait 的简写。所以应该很好理解 async 用于申明一个 function 是异步的，而 await 用于等待一个异步方法执行完成。

另外还有一个很有意思的语法规定，await 只能出现在 async 函数中。然后细心的朋友会产生一个疑问，如果 await 只能出现在 async 函数中，那这个 async 函数应该怎么调用？

如果需要通过 await 来调用一个 async 函数，那这个调用的外面必须得再包一个 async 函数，然后……进入死循环，永无出头之日……

如果 async 函数不需要 await 来调用，那 async 到底起个啥作用？
### 1.1. async 起什么作用
async 函数返回的是一个 Promise 对象。从[文档](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function)中也可以得到这个信息。async 函数（包含函数语句、函数表达式、Lambda表达式）会返回一个 Promise 对象，如果在函数中 `return` 一个直接量，async 会把这个直接量通过 `Promise.resolve()` 封装成 Promise 对象。

>`Promise.resolve(x)`  可以看作是  `new Promise(resolve => resolve(x))`  的简写，可以用于快速封装字面量对象或其他对象，将其封装成 Promise 实例。

async 函数返回的是一个 Promise 对象，所以在最外层不能用 await 获取其返回值的情况下，我们当然应该用原来的方式：`then()` 链来处理这个 Promise 对象，就像这样
```javascript
async function testAsync() {
    return "hello async";
}

const result = testAsync();
console.log(result);//promise 对象

testAsync().then(v => {
    console.log(v);    // 输出 hello async
});
```
现在回过头来想下，如果 async 函数没有返回值，又该如何？很容易想到，它会返回  `Promise.resolve(undefined)`。

联想一下 Promise 的特点——无等待，所以在没有  `await`  的情况下执行 async 函数，它会立即执行，返回一个 Promise 对象，并且，绝不会阻塞后面的语句。这和普通返回 Promise 对象的函数并无二致。

那么下一个关键点就在于 await 关键字了。

### 1.2. await 到底在等啥

一般来说，都认为 await 是在等待一个 async 函数完成。不过按[语法说明](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/await)，await 等待的是一个表达式，这个表达式的计算结果是 Promise 对象或者其它值（换句话说，就是没有特殊限定）。

因为 async 函数返回一个 Promise 对象，所以 await 可以用于等待一个 async 函数的返回值——这也可以说是 await 在等 async 函数，但要清楚，它等的实际是一个返回值。注意到 await 不仅仅用于等 Promise 对象，它可以等任意表达式的结果，所以，await 后面实际是可以接普通函数调用或者直接量的。所以下面这个示例完全可以正确运行
```javascript
function getSomething() {
    return "something";
}

async function testAsync() {
    return Promise.resolve("hello async");
}

async function test() {
    const v1 = await getSomething();
    const v2 = await testAsync();
    console.log(v1, v2);
}

test();
```
### 1.3. await 等到了要等的，然后呢

await 等到了它要等的东西，一个 Promise 对象，或者其它值，然后呢？我不得不先说，`await`  是个运算符，用于组成表达式，await 表达式的运算结果取决于它等的东西。

如果它等到的不是一个 Promise 对象，那 await 表达式的运算结果就是它等到的东西。

如果它等到的是一个 Promise 对象，await 就忙起来了，它会阻塞后面的代码，等着 Promise 对象 resolve，然后得到 resolve 的值，作为 await 表达式的运算结果。

`await` 阻塞的只是当前路径，并不阻塞其它路径的代码。不然异步就没意义了。

> 看到上面的阻塞一词，心慌了吧……放心，这就是 await 必须用在 async 函数中的原因。async 函数调用不会造成阻塞，它内部所有的阻塞都被封装在一个 Promise 对象中异步执行。

## 2. async/await 帮我们干了啥


### 2.1. async/await 的优势在于处理 then 链

单一的 Promise 链并不能发现 async/await 的优势，但是，如果需要处理由多个 Promise 组成的 then 链的时候，优势就能体现出来了（很有意思，Promise 通过 then 链来解决多层回调的问题，现在又用 async/await 来进一步优化它）。

假设一个业务，分多个步骤完成，每个步骤都是异步的，而且依赖于上一个步骤的结果。我们仍然用  `setTimeout`  来模拟异步操作：

``` javascript
// promise 实现
function doIt() {
    console.time("doIt");
    const time1 = 300;
    step1(time1)
        .then(time2 => step2(time2))
        .then(time3 => step3(time3))
        .then(result => {
            console.log(`result is ${result}`);
            console.timeEnd("doIt");
        });
}
doIt();

// await async 实现
async function doIt() {
    console.time("doIt");
    const time1 = 300;
    const time2 = await step1(time1);
    const time3 = await step2(time2);
    const result = await step3(time3);
    console.log(`result is ${result}`);
    console.timeEnd("doIt");
}

doIt();
```
## 3. Promise 出现 reject 如何处理

利用 try/catch 将 await 写在 try 中 catch 来捕获异常

``` javascript
async getFaceResult () {
	try {
	   let location = await this.getLocation(this.phoneNum);
	   console.log(location)
	} catch(err) {
	   console.log(err);
	}
}
```
## 4. 多个`await `命令`Promise`对象若不存在继发关系, 如何同时触发

多个请求的时候不使用 await，而是拿到请求的 promise，将 await 用在 promise 上，等待 promise 返回
[查看原文](https://www.cnblogs.com/3body/p/9340282.html)
```JavaScript
@action getBaseInfo = async() => {
        let baseInfo;
        try {
            baseInfo = await getBaseInfo(this.id);
            if (baseInfo) {
                this.baseInfo = baseInfo;
                // 发起请求后拿到 promise
                const visitedPromise = getVisitedCount(this.id);
                const subscribedPromise = getSubscribedCount(this.id);
                const connectionPromise = getConnection(this.id);
                // 等待 promise 返回再进行操作
                baseInfo.visitedCount = await visitedPromise;
                baseInfo.subscribedCount = await subscribedPromise;
                const connection = await connectionPromise;
                baseInfo.phoneNumber = connection.phoneNumber;
                baseInfo.email = connection.email;

                this.baseInfo = baseInfo;
            }
        } catch (e) {
            throwError(e);
        }
    };
```
