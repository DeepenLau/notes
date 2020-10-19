## 数据类型

 ```js
NaN == NaN // => false
Object.is(NaN, NaN) // => true

let res = parseFloat('left:200px') // => NaN
parseFloat('.210px') // => 0.21
// parseFloat 从左边第一个开始找数字，第一个不是数字就 NaN
if (res = 200) {
	alert(200) 
} else if (res === NaN) {
  alert(NaN)
} else if (typeof res === 'numnber') {
  alert('number')
} else {
  alert('Ivalid Number')
}
 ```

### 数据类型检测

+ typeof 只能检测一些基本类型
  + 000 对象 
  + 1 整数
  + 010 浮点数
  + 100 字符串
  + 110 布尔
+ instanceof
+ constructor
+ Object.prototype.toString.call([value])

### 数据类型转换

1. 对象 == 字符串，对象 .toString() 变为字符串 (先valueOf 获取原始值，在toString 转化为字符串)
2. Null == undefined 相等 但是和其他值比较就不相等
3. NaN==NaN 不相等
4. 其他情况都变成数字比较

> 对象的 toString 方法是用来检测数据类型的，不是用来转化字符串 => [object Object]

#### 把其他类型转换为数字

+ Number 机制：

```js
Number(undefined) // => NaN
Number(Symbol(10)) // => 报错
Number(BigInt(10)) // => 10
Number(10n) // => 10

// 先valueOf找原始值，没有的话就toString，最后字符串转数字
Number({}) // => NaN
```

+ parseInt 机制：

  parseFloat 先转字符串，然后从左边第一个开始找有效数字字符，遇到第一个不是数字就 NaN，不再继续查找，把有效数字转换为数字

  ```js
  1 + {} // => '1[object Object]'
  // {} 需要先toStirng，然后字符串遇到 + 变字符串连接
  ++i / i++ / +i 会认为是数学运算
  
  // {} 会认为是一个代码块，不参与运算
  {}+0 // => 0
  
  ({}+0) // => '[object Object]0'
  
  // {} 会认为是一个代码块，不参与运算
  // +{} 数学运算 Number(({}).toString()) => NaN
  {}+{} // => NaN
  
  ```

  ```js
  let arr = [10.18,0,10,25,23]
  arr = arr.map(parseInt)
  console.log(arr)
  
  parseInt(10.18, 0)
  parseInt(0, 1)
  parseInt(10, 2)
  parseInt(25, 3)
  parseInt(23, 4)
  ```

  ```js
  {} + {} // [object Object][object Object]
  {} + 0 // 0
  0 + {} // NaN
  [] + {} // '' + [object Object]
  {} + [] // 0
  ```

  

### 变量提升

```js
var a = 0
if (true) {
  a = 1
  function a() {}
  a = 21
  console.log(a) // 21
}
console.log(a) // 1
```

```js
// 推荐函数表达式创建函数
// 1.因为这样在变量提升阶段只会声明 func ，不会赋值，执行到该行时候在定义
// 2.在代码块里面的 function 声明函数有变量提升问题，很混乱
var func = function A() { // 推荐匿名函数具名化
 // A 只能在里面访问，外面不能访问
 console.log(A)
}
```

```js
console.log('go') // = 'go'
console.log(a) // 报错，上面会执行，发生在执行代码阶段
let a = 1
```

```js
console.log('go')
let a = 1  // 报错，上面不执行，发生在词法解析阶段，Uncaught SyntaxError: Identifier 'a' has already been declared
let a = 2
```

```js
function fn1(x) {
  return function (y) {
    console.log((++x) + y)
  }
}
var f = fn1(6)
f(7) // 返回的函数一直被外部f引用，不被释放
fn(8)(9) // 返回的函数临时不被释放，在 “9” 调用之后，返回的函数会释放
f(10)
```

```js
// 新版本浏览器，会在这里把 fn 变量提升，但是不定义 fn = undefined
// 旧版本浏览器，会在这里把 fn 变量提升+定义 fn: function(){}
if (true) {
  function fn() {}
}
```

```js
var a = 1;
if (true) {
  a = 2;
  // 代码块里声明 function / let / const , 会在当前代码块创建一个私有上下文（EC），function 要变量提升（声明+定义）
  // 执行到这里的时候，改行以上的对 a 操作会映射到全局的 a 一份
  function a () { }; 
  a = 3;
  console.log(a) // 3
}
console.log(a) // 2
```

```js
// 暂时性死区（浏览器遗留bug）基于typepf检测一个未被声明的变量，不会报错，结果是undefined
console.log(typeof a) // => undefined
```

```js
// const 和 let 形成的块级作用域不能约束var
if (true) {
  let n = 12
  var m = 13
  console.log(m, n) // 13,12
}
console.log(m) // 13
console.log(n) // 报错
```

```js
// let 构建 for 循环

```

```js
// 事件代理机制，性能提升40%
```

```js
闭包就是私有上下文里面关联的堆内存（function）被外部调用（占用），导致私有上下文（EC(BLOCK/ANY)）无法被GC回收栈内存空间，达到私有变量被保存&保护的作用

```





