# Symbol

+ Symbol 不能被new

  Symbol 有constructor, 但是内部不能被new

+ 不能参与计算

```js
const obj = {
  a: 1,
  b: 2,
  [Symbol('A')]: 100,
  [Symbol('B')]: 200,
}

// 单独获取 obj 里面 Symbol 的值
const [n,m] = Object.getOwnPropertySymbols(obj)
console.log(obj[n])
```

```js
class Person {
  get [Symbol.toStringTag]() {
    return 'Person'
  }
}
const p1 = new Person()
const type = Object.prototype.toString.call(p1)
console.log(type) // => '[object Person]'
```

