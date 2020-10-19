# 实现一个new

```js
function _new(classFn, ...args) {
  const instance = {}
  
  // ie 下不能访问 __proto__
  instance.__proto__ = classFn.prototype
  
  const result = classFn.call(instance, ...args)
  
  if (typeof result !== null && /^(object|function)$/.test(typeof result)) {
    return result
  }
  return instance
}

function Fn() {
 	this.a = 1
}

Fn.prototype.aaa = function () {
  console.log(this.a)
}

const f1 = _new(Fn, 1, 2)
f1.aaa()
```

优化

```js
if (!Object.create) {
  // 兼容全浏览器
  Object.create = function (prototype) {
    function Fn() {}
    Fn.prototype = prototype
    return new Fn
  }
}

function _new(classFn, ...args) {
  // Object.create ES2015
  // 兼容 ie9+
  const instance = Object.create(classFn.prototype)
  
  const result = classFn.call(instance, ...args)
  
  if (typeof result !== null && /^(object|function)$/.test(typeof result)) {
    return result
  }
  return instance
}

function Fn() {
 	this.a = 1
}

Fn.prototype.aaa = function () {
  console.log(this.a)
}
const f1 = _new(Fn, 1, 2)
f1.aaa()
```



