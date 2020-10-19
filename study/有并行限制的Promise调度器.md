https://juejin.im/post/6854573217013563405

```js
class Scheduler {
  add(promiseCreator) {
    this.arr = []
    this.arr.push(promiseCreator)
  }
  
  run() {
    const runs = this.arr.slice(0,2)
    runs.forEach(fn => fn())
  }
}
   
const timeout = time => new Promise(resolve => {
  setTimeout(resolve, time);
})
  
const scheduler = new Scheduler();
  
const addTask = (time,order) => {
  scheduler.add(() => timeout(time).then(()=>console.log(order)))
}

addTask(1000, '1');
addTask(500, '2');
addTask(300, '3');
addTask(400, '4');

scheduler.run()

// output: 2 3 1 4
```

```js
class Scheduler {
  arr = []

  add(promiseCreator) {
    this.arr.push(promiseCreator);
  }

  run(limit) {
    const runs = this.arr.splice(0, limit);
    runs.forEach((fn) => fn().then(() => {
      this.run(1)
    }))
  }
}

const timeout = (time) =>
  new Promise((resolve) => {
    setTimeout(resolve, time);
  });

const scheduler = new Scheduler();

const addTask = (time, order) => {
  scheduler.add(() => timeout(time).then(() => console.log(order)));
};

addTask(1000, "1");
addTask(500, "2");
addTask(300, "3");
addTask(400, "4");

scheduler.run(2);
```

