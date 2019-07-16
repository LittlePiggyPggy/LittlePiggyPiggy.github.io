---
layout: post
title: 一个 js 的浮点数问题
categories: [daily-record]
description: js 浮点数bug
keywords: JavaScript, float
---

### 问题描述

公司前端用了一个 js 函数去计算油价，大多数时候这个函数都能正常运行，但是有时候前端计算的数值会和后端有一分钱的误差  

使用的两个关键函数如下：

1. 保留两位小数的函数
```javascript
	Number.prototype.toFixed = function(len)
	{debugger;
		var add = 0;
		var s,temp;
		var s1 = this + "";
		var start = s1.indexOf(".");
		if(start>0){
			if(s1.substr(start+len+1,1)>=5)add=1; // 四舍五入，需要进位
		}
		var temp = Math.pow(10,len);
		s = Math.floor(numMulti(this, temp)) + add;
		return s/temp;
	};
```
2. 做乘法计算的函数
```javascript
	var numMulti = function(num1, num2) {
		var baseNum = 0; 
		try { 
		baseNum += num1.toString().split(".")[1].length; 
		} catch (e) { 
		} 
		try { 
		baseNum += num2.toString().split(".")[1].length; 
		} catch (e) { 
		} 
		return Number(num1.toString().replace(".", "")) * Number(num2.toString().replace(".", "")) / Math.pow(10, baseNum); 
	};
```
3. 出错原因    假设用户加200元油，单价6.42， 优惠价 5.62
```javascript
   	input = 200
   	ori_price = 6.42
   	price = 5.62
   	// 升数
   	oilmass =  changeTwoDecimal_f(inputMoney/stationPrice);
   	// 用户实际支付
   	pay = (oilmass + price).toFixed(2) // 175.06
       // 享受的优惠
       reduce = input - pay // 24.939999999999998
   	// 调用toFixed函数时，24.939999999999998 需要进位
       // numMulti(24.939999999999998 * 100) 计算时 结果又变成 2495
       reduce = reduce.toFixed(2)
```
4. 浮点数运算需要精度时，应该都转换成 整形数据 运算，（java中也可以使用BigDecimal等），显示结果时才加上小数点，才能避免各种陷阱
