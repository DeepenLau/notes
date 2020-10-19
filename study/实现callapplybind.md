# 实现 call / apply / bind

```js
// call
Function.prototype.call = function (context, ...args) {
  // 这里 == 包括 undefined 因为 null == undefiled => true
  context == null ? window : null
  if (!/^(object|function)$/.test(typeof context)) {
    // 把基本类型值变成所属类的引用类型值
    // context = new context.constructor(context) / 不支持 Symbol Bigint
    context = new Object(context)
  }
  
  const fn = Symbol('key')
  context[fn] = this
  const result = context[fn](...args)
  delete context[fn]
  return result
}

Function.prototype.apply = function (context, args) {
  return this.call(context, ...args)
}

Function.prototype.bind = function (context, ...outerArgs) {
  const fn = this
  return function (...innerArgs) {
    return fn.call(context, ...outerArgs, ...innerArgs)
  }
}

function fn () {
 console.log(this.a)
}

var obj = {
  a: 1
}

fn.call(obj)  
```

