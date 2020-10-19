```js
class MyPromise {
  constructor(executor) {
    this.status = 'pending'
    this.value
    this.reason
    this.fulfillCbs = []
    this.rejectCbs = []

    const resolve = (value) => {
      setTimeout(() => {
        this.status = 'resolve'
        this.value = value
        this.fulfillCbs.forEach(fulfilled => fulfilled(value))
      })
    }
    const reject = (reason) => {
      setTimeout(() => {
        this.status = 'reject'
        this.reason = reason
        this.rejectCbs.forEach(rejected => rejected(reason))
      })
    }

    executor(resolve, reject)
  }

  then(fulfilled, rejected) {
    if (this.status === 'pending') {
      this.fulfillCbs.push(fulfilled)
      this.rejectCbs.push(rejected)
    }
    if (this.status === 'resolve') {
      fulfilled(this.value)
    }
    if (this.status === 'resolve') {
      rejected(this.reason)
    }
  }
}

const p = new MyPromise((resolve, reject) => {
  setTimeout(() => {
    resolve('done')
  })
})
p.then(res => {
  console.log(res)
})
p.then(res => {
  console.log(res)
})
```

