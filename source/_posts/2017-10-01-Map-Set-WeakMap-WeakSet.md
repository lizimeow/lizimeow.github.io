---
title: Map Set WeakMap WeakSet
date: 2017-10-01 23:48:22
tags:
---

### Set

Set 是 ES6 新增的有序列表集合, 他不会包含重复项. Set 支持 :

- add(item) 方法，用来向 Set 添加任意类型的元素，如果已经添加过则自动忽略
- has(item) 方法用来检测 Set 中是否存在指定元素
- delete(item) 方法用来从 Set 中删除指定元素
- clear() 用来清空 Set
- 获取 Set 集合长度用 size 属性

Set 调用 add, has, delete 等方法时对 key 进行的比较，不做类型转换。可以认为使用「===」进行比较，当然也不全是「===」：

- Set 中，NaN 只能添加一次（虽然NaN === NaN 返回 false）；
- Set 中，「-0」和「0 或 +0」可以同时存在，因为符号不一样（虽然 -0 === 0 或 -0 === +0 返回 true）；

### Map

Map 是有序键值对集合, 键值对的 key 和 value 可以是任何类型的元素

- 通过 `map.set(key, value)` 可以为 Map 添加新的键值对, 如果设置的 key 已经存在的话 ,就会用新的 value 覆盖.
- get(key) 方法用来获取指定的 key 的值
- map 的 has(key), delete(key), clear() 方法和 size 属性与 set 相似

### 迭代

可以用 `for...of`来遍历 set 跟 map

返回可迭代对象的几个方法: entries(), keys(), values() 

- 直接遍历 set 和 map 相当于遍历entries()
- keys() 和values() 返回 key 和 value 的集合

### weakMap

WeakMap 相对于普通的 Map，也是键值对集合，只不过 WeakMap 的 key 只能是非空对象

WeakMap 对它的 key 仅保持**弱引用**，也就是说它不阻止垃圾回收器回收它所引用的 key

WeakMap 最大的好处是可以避免内存泄漏。一个仅被 WeakMap 作为 key 而引用的对象，会被垃圾回收器回收掉。

WeakMap 拥有和 Map 类似的 set(key, value) 、get(key)、has(key)、delete(key) 和 clear() 方法，但没有 size 属性，也没有任何与迭代有关的方法。

### weakSet

`WeakSet` 对象是一些对象值的集合, 并且其中的每个对象值都只能出现一次.

它和 `Set`对象的区别有两点:

- `WeakSet` 对象中只能存放**对象引用**, 不能存放值, 而 `Set` 对象都可以.
- `WeakSet` 对象中存储的对象值都是被弱引用的, 如果没有其他的变量或属性引用这个对象值, 则这个对象值会被当成垃圾回收掉. 正因为这样, `WeakSet` 对象是无法被枚举的, 没有办法拿到它包含的所有元素.
