---
title: CodeWars - 3kyu - Morse Encoding
date: 2016-12-29 10:26:49
tags:
     - JavaScript
---

[题目](https://www.codewars.com/kata/morse-encoding/train/javascript)

这个题目的要求就是让我们写两个方法

The first method `Morse.encode` will take a String representing the message and will return an array of **signed 32-bit integers** in **big-endian order** and in **two's complement format**. 

The second method `Morse.decode` will take an array of numbers and return the String representation of the message. 

我的代码：

```javascript
var Morse = {};
Morse.alpha = {
  'A': '10111',
  'B': '111010101',
  'C': '11101011101',
  'D': '1110101',
  'E': '1',
  'F': '101011101',
  'G': '111011101',
  'H': '1010101',
  'I': '101',
  'J': '1011101110111',
  'K': '111010111',
  'L': '101110101',
  'M': '1110111',
  'N': '11101',
  'O': '11101110111',
  'P': '10111011101',
  'Q': '1110111010111',
  'R': '1011101',
  'S': '10101',
  'T': '111',
  'U': '1010111',
  'V': '101010111',
  'W': '101110111',
  'X': '11101010111',
  'Y': '1110101110111',
  'Z': '11101110101',
  '0': '1110111011101110111',
  '1': '10111011101110111',
  '2': '101011101110111',
  '3': '1010101110111',
  '4': '10101010111',
  '5': '101010101',
  '6': '11101010101',
  '7': '1110111010101',
  '8': '111011101110101',
  '9': '11101110111011101',
  '.': '10111010111010111',
  ',': '1110111010101110111',
  '?': '101011101110101',
  "'": '1011101110111011101',
  '!': '1110101110101110111',
  '/': '1110101011101',
  '(': '111010111011101',
  ')': '1110101110111010111',
  '&': '10111010101',
  ':': '11101110111010101',
  ';': '11101011101011101',
  '=': '1110101010111',
  '+': '1011101011101',
  '-': '111010101010111',
  '_': '10101110111010111',
  '"': '101110101011101',
  '$': '10101011101010111',
  '@': '10111011101011101',
  ' ': '0' // Technically is 7 0-bits, but we assume that a space will always be between two other characters
};
Morse.encode = function(message){
	let k = message.split('').map(v => Morse.alpha[v]).join('000')
	k+="0".repeat((32-k.length%32)%32)
	return arr = k.match(/\d{32}/g).map(v => -(~parseInt(v,2)+1))
};

Morse.decode = function(integerArray){
  	return integerArray.map(v=>{
  		if(v>0){
  			return "0".repeat((32-v.toString(2).length%32)%32)+v.toString(2)
  		}else{
  			let tem = (~v).toString(2)
  			tem="0".repeat((32-tem.length%32)%32)+tem
  			return tem.split('').map(v=>{return v^1}).join('')
  		}
  	}).join('').replace(/0+$/,'').split('000').map(v=> v[0]==0?" "+check(v.substring(1)):check(v)).join('')
};
function check(s){
	for(let v in Morse.alpha){
		if(Morse.alpha[v] == s)
			return v
	}
}
```

codewars里的票数最高的答案的decode如下

果然还是要学习一个_(:зゝ∠).. 我写的check函数就显得比较冗余而且速度很慢...

```javascript
Morse.decode = function(ints) {
  var bits = ints.map(function(i) {
    return ('00000000000000000000000000000000' + (i >>> 0).toString(2)).slice(-32)
  }).join('')

  return bits.replace(/0+$/, '').split('0000000').map(function(word) {
    return word.split('000').map(function(c) {
      return Morse.bits[c]
    }).join('')
  }).join(' ')
}
Morse.bits = {}

for (var x in Morse.alpha) {
  Morse.bits[Morse.alpha[x]] = x
}
```

朋神说自动机最快_(:зゝ∠)...（然而我不会... 有空再研究一下....）

上面的bits还可以这样实现↓

```javascript
Morse.bits = Object.keys(Morse.alpha).reduce((p, n) => (p[Morse.alpha[n]] = n,p), {})
```

一年又要过去啦~~

新年快乐~~

QAQ加油呀加油~~