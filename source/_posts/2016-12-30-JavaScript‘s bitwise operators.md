---
title: JavaScript's bitwise operators
date: 2016-12-30 14:00:00
tags:
     - JavaScript
---
在做codewars的Morse encoding 的题目的时候，发现js的位运算很奇怪_(:зゝ∠)...

然后试了一下

`console.log((5).toString(2));`

结果是 `101`

`console.log(~5)`

想象中应该是2 或者010

然而结果是`-6`... 

**Reason：**

The binary representation of numbers in JS is handled in the "Two's Complement" system. which is just a fancy way of saying that for any given set of bits,the first bit represents the sign (0 for positive,1 for negative) of the number and the rest of the bits represent the "absolute value" of the number according to the follow simple formula:

- For positive numbers and 0 ,the value tells how "far" from 0 the number is... i.e. 10 in binary(2 in decimal) means "2 above 0" ,so for a 3-bit number in Two's Complement ,the number 2(decimal) would be represented as "010"
- For negative numbers,how far above the smallest possible value that can be represented with the number of bits available ,e.g. with three bits ,the smallest possible number we can represent is -4 ,so "101"(binary) is like saying "01 more than -4" or -3.

so in the example above ,when we "flipped" the bits of the number 5,there was the extra "sign bit" on the front that got flipped as well.. so what we thought was "101" was actually "0101" and when we flipped the bits ,we got "1010" i.e. "-6"

即所有的按位操作符的操作数都会被转成补码（two's complement）形式的有符号32位整数。

所以要考虑在进行位运算的时候一定要考虑符号位..

`<<` 有符号左移

`>>` 有符号右移

`<<<` 无符号左移

`>>>`无符号右移

#### 巧用

- 判断奇偶性

  `n & 1 === 0 //true 为 奇数`

- 向下取整

  `(3.14 | 0) === 3 //true `

  `~~3.14 //3`

- 向上取整

  `-~3.14 //4`

- 交替变量

  ```
  var a = 1, b = 2;
  a ^= b; // a = a ^ b = 1 ^ 2 = 3
  b ^= a; // b = b ^ (a ^ b) = 2 ^ (1 ^ 2) = 1
  a ^= b; // a = a ^ b = 3 ^ 1 = 2
  ```

- 求 2 的 N 次方

  ` 1 << n `

- 判断数字正负

  ` (n === (n >>> 0)) ? true : false; //负数返回false `

