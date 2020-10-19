# instanceOf 的实现

原理：构造函数的原型以及圆形链是否能找到和实例对象的 `__proto__`相等

```js
function instance_of(example, classFunc) {

  let proto = Object.getPrototypeOf(example)

  while (proto) {
    if (proto === classFunc.prototype) {
      return true
    }

    proto = Object.getPrototypeOf(proto)
  }

  return false

}
```

