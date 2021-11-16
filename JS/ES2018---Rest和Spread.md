# Rest
## 对象解构中的 rest 操作符(...)
在对象解构模式下，rest 操作符会将解构源的除了已经在对象字面量中指明的属性之外的，所有可枚举自有属性拷贝到它的运算对象中。
```JavaScript
const obj = {foo: 1, bar: 2, baz: 3};
const {foo, ...rest} = obj;
    // Same as:
    // const foo = 1;
    // const rest = {bar: 2, baz: 3};
```
如果你正在使用对象解构来处理命名参数，rest 操作符让你可以收集所有剩余参数：
```JavaScript
const  fun1  = ({param1,param2, ...temp}) => {
	console.log(param1)// 1
	console.log(param2)// 2
	console.log(temp)// {param3:'3',param4:'4'}
}

fun1({param1:'1',param2:'2',param3:'3',param4:'4'})
```
#### 语法限制
在每个对象字面量的顶层，可以使用 rest 操作符最多一次，并且必须只能在末尾出现：
```cpp
const {...rest, foo} = obj; // SyntaxError
const {foo, ...rest1, ...rest2} = obj; // SyntaxError
```
如果是嵌套结构，你可以多次使用 rest 操作符：
```cpp
const obj = {
    foo: {
        a: 1,
        b: 2,
        c: 3,
    },
    bar: 4,
    baz: 5,
};

const {foo: {a, ...rest1}, ...rest2} = obj;

// Same as:
// const a = 1;
// const rest1 = {b: 2, c: 3};
// const rest2 = {bar: 4, baz: 5};
```
## 对象字面量中的 spread 操作符 "..."
应用场景
```dart
//合并对象
const merged = {...obj1, ...obj2};
const merged = Object.assign({}, obj1, obj2);
//设置默认值
const DEFAULTS = {foo: 'a', bar: 'b'};
const userData = {foo: 1};

const data = {...DEFAULTS, ...userData};
//指定默认值
const userData = {foo: 1};
const data = {foo: 'a', bar: 'b', ...userData};
const data = Object.assign({}, {foo:'a', bar:'b'}, userData);
    // {foo: 1, bar: 'b'}

```
