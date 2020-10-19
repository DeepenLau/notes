输入一个正数N，输出所有和为N的连续正数序列

例如：输入15

结果：[[1,2,3,4,5], [4,5,6], [7,8]]

(划窗算法？)

```js
// 15 => 15/2 向上取整 8, 只需取中间值以下的
function fn(num) {
  const result = []
  const last = Math.ceil(num / 2)
  
  for (let i = 8; i > 0; i--) {
    let arr = [i]
    for (let j = i - 1; j > 0; j--) {
      arr.push(j)
      if (sum(arr) === num) {
        result.push(arr)
        break
      } else if (sum(arr) > num) {
        // 超出，清空，下一轮重来
        break
      } else if (sum(arr) < num) {
        // 啥也不做，继续加
      }
    }
  }
  
  return result
}

function sum(arr) {
	return (arr[0] + arr[arr.length-1]) * arr.length / 2
}
```

