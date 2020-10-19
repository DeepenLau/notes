	# async / await 原理

async / await

```js
function api(num) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(num)
    }, 1000)
  })
}

async function fn() {
  const num1 = await api(1)
  console.log(num1)
  const num2 = await api(2)
  console.log(num2)
  const num3 = await api(3)
  console.log(num3)
}

fn()
```

Generator + promise 改写

```js
function api(num) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(num)
    }, 1000)
  })
}

function async (generator) {
  const it = generator()

  const next = (prevResult) => {
    const {value: promise, done} = it.next(prevResult)
    if (done) return
    promise.then(result => {
      next(result)
    })
  }

  next()
}

async(function * fn() {
  const num1 = yield api(1)
  console.log(num1)
  const num2 = yield api(2)
  console.log(num2)
  const num3 = yield api(3)
  console.log(num3)
})
```

