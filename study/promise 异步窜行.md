```js
const createPromise = (time, id) => () =>
  new Promise(solve =>
    setTimeout(() => {
      console.log("promise", id);
      solve();
    }, time)
  );

runPromiseByQueue([
  createPromise(3000, 1),
  createPromise(2000, 2),
  createPromise(1000, 3),
  createPromise(10, 4),
]);

function runPromiseByQueue(arr) {
  arr.reduce((prev, current) => {
    return prev.then(current)
  }, Promise.resolve())
}
```

