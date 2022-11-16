### 类型注解
```TypeScript
function printLabel1(labelledObj: string) {
  console.log(labelledObj.label);
}
function printLabel(labelledObj: { label: string }) {
  console.log(labelledObj.label);
}

let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);
```

### 接口：一种契约
```TypeScript
interface Person {
  firstName: string, //必须属性
  lastName?: string, //可选属性
  readonly age: number，//只读，函数中不可修改
  [attr: string]: any，//任意属性，定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集
}

function greeter (person: Person) {
  return 'Hello, ' + person.firstName + ' ' + person.lastName
}

let user = {
  firstName: 'Yee',
  lastName: 'Huang'
}

console.log(greeter(user))
```
### 函数类型
>为了使用接口表示函数类型，我们需要给接口定义一个调用签名。它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型。
```TypeScript
/* 
接口可以描述函数类型(参数的类型与返回的类型)
*/

interface SearchFunc {
  (source: string, subString: string): boolean
}
```