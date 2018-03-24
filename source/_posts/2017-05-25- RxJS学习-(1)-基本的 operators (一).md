---
title: RxJS 学习-(1)-基本的 operators (一)
date: 2017-05-25 10:00:00
author: Lizimeow
tags: RxJS
---
## Operators (一)

**take(n)**

取前n个元素

**first()**

first 会取 observable 送出的第一个元素之后就结束，相当于take(1)

**takeUntil( observable )**

在某件事情发生时，让一个 observable 直接发出完成信息，例：

```javascript
var source = Rx.Observable.interval(1000)
var click = Rx.Observable.fromEvent(document.body, 'click')
var example = source.takeUntil(click) //当点击时，结束sourse事件
example.subscrible({
  next: (value) => console.log(value),
  error: (err) => console.log('err',err),
  complete: () => console.log('done')
})
```
### 拖拽实例

1. 在页面上画一个box (#box)

   ```css
   /*<div id="box"></div>*/
   body{
     position: relative;
   }
   #box{
     width: 80px;
     height: 80px;
     background: rgba(1,1,1,0.2);
     position: absolute;
     cursor: all-scroll;
   }
   ```

2. 获取要监听的 DOM 元素

   ```javascript
   const box = document.querySelector('#box')
   const body = document.body
   ```

   监听 box 上的 mousedown ，监听 body 上的 mousemove 和 mouseup 

3. 用 `fromEvent` 来取得各个的 observable

   ```javascript
   const mouseDown = Rx.Observable.fromEvent(box, 'mousedown')
   const mouseMove = Rx.Observable.fromEvent(body, 'mousemove')
   const mouseUp = Rx.Observable.fromEvent(body, 'mouseup')
   ```

> 还没 subscribe 之前都不会开始监听，直到 subscribe 之后 observable 才会开始送值

4. 当鼠标在元素 (#box) 上按下左键 (mousedown) 时，开始监听鼠标移动 (mousemove) 的位置；当鼠标左键放掉(mouseup)時，结束监听鼠标移动；当鼠标移动(mousemove)被监听时，修改元素的样式

   ```javascript
   mouseDown
     .map(e => mouseMove.takeUntil(mouseUp)) //当 mouseDown 时，转成 mouseMove 事件
     .concatAll()
     .map(e => ({x:e.clientX, y:e.clientY})) //用 map 把 mousemove event 转成 x,y 的位置
     .subscribe(pos => {
       box.style.left = pos.x - 40 + 'px'
       box.style.top = pos.y - 40 + 'px'
     })
   ```



## Operators (二)

 **skip(n)**

略过前n个元素

 **takeLast(n)**

take 取前几个，takeLast 取最后几个。 takeLast 必须等到整个 observable 完成(complete)，才能知道最后的元素有哪些，並且**同步送出**，如果用 Marble Diagram 表示如下

```
source : ----0----1----2----3----4----5|
                takeLast(2)
example: ------------------------------(45)|
```

 **last()**

跟 `take(1)` 相同，我們有一个 `takeLast(1)` 的简化写法，就是 `last()` 用來取最后一个元素

**concat**

`concat` 可以把多個 observable 实例合并成一个

```javascript
var source = Rx.Observable.interval(1000).take(3);
var source2 = Rx.Observable.of(3)
var source3 = Rx.Observable.of(4,5,6)
var example = source.concat(source2, source3);
//concat 也可以当做静态方法
//var example = Rx.Observable.concat(source, source2, source3);
```

跟 concatAll 一样，必须等前一个 observable **完成 ( complete ) **，才会继续下一个

```javascript
source : ----0----1----2|
source2: (3)|
source3: (456)|
            concat()
example: ----0----1----2(3456)|
```

**startWith**

startWith 可以在 observable 的最开始加入要发送的元素，有点像 concat ，但参数不是 observable ，而是要发送的元素

```javascript
source : ----0----1----2----3--...
                startWith(0)
example: (0)----0----1----2----3--...
```

`startWith` 的值时一开始就同步发出，这个 operator 经常用来**保存程序的起始状态**

**merge**

```javascript
var source = Rx.Observable.interval(500).take(3);
var source2 = Rx.Observable.interval(300).take(6);
var example = source.merge(source2);
//也可以 var example = Rx.Observable.merge(source, source2)
example.subscribe({
    next: (value) => { console.log(value); },
    error: (err) => { console.log('Error: ' + err); },
    complete: () => { console.log('complete'); }
});
// 0
// 0
// 1
// 2
// 1
// 3
// 2
// 4
// 5
// complete
```

由上面的例子可以看出来，`merge` 把多个 observable 同时处理，也就是按照**时间顺序**处理，这跟 `concat` 一次处理一个 observable 是完全不一样的，merge 的 Marble Diagram 如下

```javascript
source : ----0----1----2|
source2: --0--1--2--3--4--5|
            merge()
example: --0-01--21-3--(24)--5|
```

merge 的 逻辑有点像 or(||) ，就是当两个 observable 其中一个被触发时都可以被处理，这很常用在一个以上的按钮具有部分相同的行为。例如，一个视频播放器有两个按钮，一个是暂停，一个是结束。这两个按钮的相同行为就是视频会被暂停，只是结束会让视频回到 00 秒。这时我们就可以把这两个按钮的事件 merge 起来处理视频暂停这件事。

```javascript
const stopVideo = Rx.Observable.merge(stopButton, endButton)
stopVidep.subScribe(() => {
  //暂停
})
```



## operators(三) ---合并

**combineLatest**

`combineLatest` 会取得各个 observable **最后** 送出的值，再输出成一个值，栗子如下

```javascript
var source = Rx.Observable.interval(500).take(3);
var newest = Rx.Observable.interval(300).take(6);
var example = source.combineLatest(newest, (x, y) => x + y);
```

Marble Diagram 如下

```javascript
source : ----0----1----2|
newest : --0--1--2--3--4--5|

    combineLatest(newest, (x, y) => x + y);

example: ----01--23-4--(56)--7|
```

`comblineLatest`可以接收多个 observable ，最后一个参数是 callback function ，这个callback function 接收的参数数量跟合并的 observable 数量相同，依照范例来说，因为我们这里合并了两个 observable ，所有后面的 callback function 就接收 x,y 两个参数，x 会接收从 source 发出来的值，y 会接收从 newest 发送出来的值。 

**最后一个重点是，一定会等两个 observable <u>都有送值</u>出来才会调用 callback！！** ，所以这段程序是这样运行的：

- newest 送出了 0 ，此时source 没有送出任何值，所以不执行callback
- source 送出了 0 ，此时 newest 最后一次送出的值为 0 ， 把这两个数传入 callback 得到 0
- newest 送出了 1 ，此时 source 最后一次的送出值为 0 ，得到 1
- newest 送出了 2 ，此时 source 最后一次的送出值为 0 ，得到 2
- source 送出了 1 ，此时 newest 最后一次送出的值为 2 ，得到 3
- newest 送出了 3 ，此时 source 最后一次的送出值为 1 ，得到 4
- source 送出了 2 ，此时 newest 最后一次的送出值为 3 ，得到 5
- newest 送出了 4 ，此时 source 最后一次的送出值为 2 ，得到 6
- newest 送出了 5 ，此时 source 最后一次的送出值为 2 ，得到 7
- newest 結束，因为 source 也结束了，所以 全部结束。

不管是 source 还是 newest 产生了值，只要另一方曾有值(最后的值)，就会执行 callback 并送出新的值。这就是` combineLatest`

`combineLatest` 常用在运算多个因子的结果，如最常见的 BMI 计算，我们身高变动时就拿上一次的体重计算新的 BMI ，当体重变动时则拿上一次的身高计算 BMI , 这就很适合用 combineLatest 来处理

**zip**

zip会取每个**相同位置**的元素并传入 callback ，也就是说每个 observable 的第n个元素会一起被传入 callback 。

```javascript
var source = Rx.Observable.interval(500).take(3);
var newest = Rx.Observable.interval(300).take(6);
var example = source.zip(newest, (x, y) => x + y);
```

```javascript
source : ----0----1----2|
newest : --0--1--2--3--4--5|
    zip(newest, (x, y) => x + y)
example: ----0----2----4|
```

过程如下：

- newest 送出第一个值为0，此时 source 没有送出第一个值，所以不会执行 callback 
- source 送出第一个值为0，newest 之前的第一个值是 0，所以执行 callback，得到0
- newest 送出第二个值为1，此时 source 没有送出第二个值，所以不会执行 callback 
- newest 送出第三个值为2，此时 source 没有送出第二个值，所以不会执行 callback 
- source 送出第二个值为1， newest 之前的第二个值是 1，所以执行 callback ，得到2
- newest 送出第四个值为3，此时 source 没有送出第四个值，所以不会执行 callback 
- source 送出第三个值为2， newest 之前的第三个值是 2，所以执行 callback ，得到4
- source 结束 example 就直接结束，因为 source 跟 newest 不会再有对应位置的值

> 建议平时不要乱用 zip ，除非真的需要。因为 zip 必须 cache 没处理的元素，当两个 observable 一个快一个慢时会 cache 非常多元素。有可能造成内存相关的问题

**withLatestFrom**

withLatestFrom 的运作方式跟 combineLatest 有点像，只是他有主从关系，只有在**主要的 observable 送出新值时**， 才会执行callback， 另一个 observable 只是在背景下运行。

```javascript
var main = Rx.Observable.from('hello').zip(Rx.Observable.interval(500), (x, y) => x);
var some = Rx.Observable.from([0,1,0,0,0,1]).zip(Rx.Observable.interval(300), (x, y) => x);
var example = main.withLatestFrom(some, (x, y) => {
    return y === 1 ? x.toUpperCase() : x;
});
```

Marble Diagram

```javascript
main   : ----h----e----l----l----o|
some   : --0--1--0--0--0--1|

withLatestFrom(some, (x, y) =>  y === 1 ? x.toUpperCase() : x);

example: ----h----e----l----L----O|
```