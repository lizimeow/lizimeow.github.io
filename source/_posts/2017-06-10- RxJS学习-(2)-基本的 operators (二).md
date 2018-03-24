---
title:  "RxJS 学习-(2)-基本的 operators (二)"
date:   2017-06-10 20:00:00
author: Lizimeow
tags: RxJS
---

### scan

scan 其实是 Observable 版本的 reduce

```javascript
const source = Rx.Observable.from('hello')
				.zip(Rx.Observable.interval(600), (x, y) => x)
const example = source.scan((origin, next) => origin + next, '')
example.subscribe({
    next: value => console.log(value),
  	error: err => console.log(err),
  	complete: _ => console.log('complete!')
})
// h
// he
// hel
// hell
// hello
// complete!
```

Marble Diagram

```javascript
source : ----h----e----l----l----o|
    scan((origin, next) => origin + next, '')
example: ----h----(he)----(hel)----(hell)----(hello)|
```

scan 跟 reduce 最大的差别是 scan 一定会回传一个 observable 实例, 而 reduce 最后回传的值有可能是任何类型,必须看传入的 callback 才能确定 reduce 最后的返回值

### buffer

buffer 有五个相关的 operators

- buffer


- bufferCount
- bufferTime
- bufferToggle
- bufferWhen



```javascript
var source = Rx.Observable.interval(300);
var source2 = Rx.Observable.interval(1000);
var example = source.buffer(source2);

example.subscribe({
    next: value => console.log(value),
    error: err => console.log('Error: ' + err),
    complete: () => console.log('complete')
});
// [0,1,2]
// [3,4,5]
// [6,7,8]...
```

buffer 要传入一个 source2, 他会把原本的 source 送出的元素缓存在队列中, 直到 source2 送出元素时. 就会触发把缓存的元素送出

```javascript
source : --0--1--2--3--4--5--6--7..
source2: ---------0---------1--------...
            buffer(source2)
example: ---------([0,1,2])---------([3,4,5])    
```

上面的范例可以可以改成

```javascript
var source = Rx.Observable.interval(300);
var example = source.bufferTime(1000);
```

即每隔一秒送出一个元素

除了用时间做间隔, 还可以用数量做间隔

```javascript
var source = Rx.Observable.interval(300);
var example = source.bufferCount(3);
```

每隔三个数输出一次, 以上三个例子的输出都相同

举个🌰

我们可以用 buffer 做某个事件的过滤, 比如说500毫秒内连点两下才能成功印出`'success'`

```javascript
const click = Rx.Observable.fromEvent(document.body, 'click')
const example = click
	.bufferTime(500)
	.filter(arr => arr.length >= 2)
example.subscribe({
    next: v => console.log('success'),
  	error: err => { console.log('Error: ' + err); },
    complete: () => { console.log('complete'); }
})
```



UI 大概是所有非同步行为中最不好处理的, 不只是因为他直接影响了用户的体验, 更大的问题是 UI 互动常常是高频率触发的事件, delay 和 delayWhen 都是跟 UI 互动比较相关的

### delay

delay 可以延迟 observable 一开始发送元素的时间点

delay 除了可以传入毫秒之外, 也可以传入 Date 类型的参数

### delayWhen

 delayWhen 跟 delay 最大的差别是: delayWhen 可以影响每个元素, 而且 需要传一个 callback 并回传一个 observable, 🌰如下:

```javascript
const source = Rx.Observable.interval(300).take(5)
const example = source.delayWhen(x => Rx.observable.empty().delay(100 * x * x))
// 这里传进来的 x 就是 source 送出的每个值,这样我们就能对每一个做延迟
example.subscribe({
    next: value => console.log(value),
    error: err => console.log('Error: ' + err),
    complete: () => console.log('complete')
});
```

### debounce

Rx 有 debounce 跟 debounceTime , 一个是传入 observable 另一个则是传入毫秒，比較常用到的是 debounceTime, 🌰:

```javascript
const source = Rx.Observable.interval(300).take(5)
const example = source.debounceTime(1000)
example.subscribe({
  next: v => console.log(v),
  error: err => console.log(err),
  complete: () => console.log('complete')
})
// 4
// complete
```

每次收到元素, 会先把元素 cache 住并等待一段时间, 如果这段时间内已经没有收到任何元素, 则把元素送出, 如果这段时间内有收到新的元素, 则会把原本 cache 住的元素释放掉并重新计时, 不断反复

### throttle

debonce 跟 throttle 虽然都是要降低事件的触发频率, 但是行为上有很大不同

Rx 有 throttle 和 throttleTime, 一个是传入 observable 另一个则是传入毫秒，比較常用到的是 debounceTime, 🌰:

```javascript
const source = Rx.Observable.interval(300).take(5)
const example = source.throttleTime(1000)
example.subscribe({
  next: v => console.log(v),
  error: err => console.log(err),
  complete: () => console.log('complete')
})
// 0
// 4
// complete
```

跟 debounce 不同的是 throttle 会先送出元素, 等到有元素被送出就会沉默一段时间,等时间过了又会送出元素

throttle 比较适合用在连续性行为,比如说 UI 动画的运算过程,像之前做拖拉的时候,就可以加上 throttleTime(12)让 mousemove 事件不要发送的太快.

>  浏览器有一个 [requestAnimationFrame](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame) API 是专门用来做 UI 运算的，通常用這个的效果会比 throttle 好。

### distinct

顾名思义, distinct 可以用来去重
( 其实有好多 API 都是这样 看名字就能大概猜到是干啥的..

```javascript
const source = Rx.Observable.from(['a', 'b', 'c', 'a', 'b'])
            .zip(Rx.Observable.interval(300), (x, y) => x);
const example = source.distinct()
example.subscribe({
  next: v => console.log(v),
  error: err => console.log(err),
  complete: () => console.log('complete')
})
// a b c complete
```

distinct 还可以传入一个回调函数, 这个回调函数会传入一个接收到的元素, 并传回我们真正希望比对的值: 🌰:

 ```javascript
const source = Rx.Observable.from([{ value: 'a'}, { value: 'b' }, { value: 'c' }, { value: 'a' }, { value: 'c' }]))
            .zip(Rx.Observable.interval(300), (x, y) => x);
const example = source.distinct(x => x.value)
example.subscribe({
  next: v => console.log(v),
  error: err => console.log(err),
  complete: () => console.log('complete')
})
// {value: "a"}
// {value: "b"}
// {value: "c"}
// complete
 ```

### distinctUntilChanged

distinctUntilChanged 跟 distinct 一样会把相同的元素过滤掉，但 distinctUntilChanged 只会跟最后一次送出的元素比较，不会每个都比，🌰:

```javascript
const source = Rx.Observable.from(['a', 'b', 'c', 'c', 'b'])
            .zip(Rx.Observable.interval(300), (x, y) => x);
const example = source.distinctUntilChanged()
example.subscribe({
  next: v => console.log(v),
  error: err => console.log(err),
  complete: () => console.log('complete')
})
// a
// b
// c
// b
// complete
```

这里 distinctUntilChanged 只会暂存一個元素，并在收到元素时跟暂存的元素进行比较，如果一样就不送出，如果不一样就把暂存的元素换成刚接收到的新元素并送出。

```
source : --a--b--c--c--b|
            distinctUntilChanged()
example: --a--b--c-----b|

```

從 Marble Diagram 中可以看到，第二个 c 送出时刚好上一个就是 c 所以就被滤掉了，但最后一个 b 则跟上一个不同所以沒被滤掉

