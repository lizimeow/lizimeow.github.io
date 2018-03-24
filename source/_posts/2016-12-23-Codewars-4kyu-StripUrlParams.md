---
title: CodeWars - 4kyu - Strip Url Params
date: 2016-12-23 17:20:20
tags:
     - JavaScript
---
[题目](https://www.codewars.com/kata/strip-url-params/train/javascript)

### Description:

##### Complete the method so that it does the following:

- Removes any duplicate query string parameters from the url
- Removes any query string parameters specified within the 2nd argument (optional array)

##### Examples:

```javascript
stripUrlParams('www.codewars.com?a=1&b=2&a=2') // returns 'www.codewars.com?a=1&b=2'
stripUrlParams('www.codewars.com?a=1&b=2&a=2', ['b']) // returns 'www.codewars.com?a=1'
stripUrlParams('www.codewars.com', ['b']) // returns 'www.codewars.com'
```

I coded 10+ lines but the best practices of all solutions is 2 lines... sad ....

```javascript
//my solution
function stripUrlParams(url, paramsToStrip){
	let params = url.substring(url.indexOf("?")+1)
	let k = []
	let j = params.split("&").filter((v,i)=>{
		let tem = v.split("=")[0]
		if (k.indexOf(tem)==-1 && test(paramsToStrip,tem)){
			k.push(tem)
			return v
		}
	})
	return url.substring(0,url.indexOf("?")+1)+j.join("&")
}
const test = (arr,p)=>{
	if(arr==undefined || arr.length==0)
		return true
	return arr.indexOf(p)==-1
}
```

 

```javascript
//best practices 
function stripUrlParams(url, paramsToStrip){
  return url.replace(/&?([^?=]+)=.+?/g, function(m, p1, qPos) {
    return url.indexOf(p1 + '=') < qPos || (paramsToStrip||[]).indexOf(p1) > -1 ? "": m;
   });
}
```

get了一下replace的小trick..

该好好学学正则了_(:зゝ∠)_...