# requestIdlePromise
> A POC for requestIdleCallback used Promise

目前类似的调度方案，无非都是去模拟 RIC，react 通过 RAF 模拟，vue 通过 runtime + import() 实现，还有通过 Date + setTimeOut 模拟的

这里给出一个另类实现，通过 Promise + 锁机制实现

它负责做的事情很简单，就是负责高优先级的任务尽可能快的执行，低优先级的任务有空就执行

### Uage
```js
const sm = new Scheduler
const call = key => sm.wrap(() => console.log(key))

const highTask = async () => {
  await call('high task')
}

const lowTask = async i => {
  await call(i)
}
const runLow = async () => {
  while (sm.isIdle()) { //isIdle 作为循环条件
    await sm.requestIdlePromise() //等待空闲时间
    await new Promise(res => setTimeout(res, 500))
    await lowTask(i)
  }
}

const runHigh = async () => {
  await highTask()
}

runLow()

document.querySelector('button').onclick = runHigh()
```


