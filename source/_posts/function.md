---
title: 函数之call,apply,arguments,闭包
date: 2017-08-09 11:45:20
description:
categories: 前端
tags: [前端,函数调用]
---

### 函数的实参和形参
- JavaScript 的函数定义不用指定形参的类型
- 调用传入的实参也可以是任意类型
- JavaScript甚至不检查传入实参的个数
#### 可选形参

如果调用函数的时候传入的实参比指定的形参少，剩下的形参都将设置为 undefined 值，通常使用逻辑与运算符给形参指定默认值
```
function getType(type) {
	// 如果 type 传入值则使用传入值，否则使用默认值 "normal"
    // 通常建议可选参数放在参数列表最后
	type = type || 'normal'
	// ... the rest code
	return type;
}
```

#### 可变长的实参列表：实参对象
- 当调用函数的时候传入实参人个数超过形参个数时（和上面相反），没有办法直接获得未命名值的引用。参数对象解决了这个问题.
- 在函数体内，标识符 arguments 是指向实参对象的引用，参数对象是一个 类数组对象，这样可以通过索引来访问实参了
```
	function push(arr /* optional items [, item ... [, item]] */) {
		// 注意，arguments是类数组对象，不能直接使用数组的slice方法，而是要通过Array.prototype.slice.call的方式来实现数组剥离操作，后面传入1指取出从arguments数组index为1的开始向后的所有参数，如果是普通的数组，相当于arr1.slice(1)
	    var items = Array.prototype.slice.call(arguments, 1)
	    for (var i = 0; i < items.length; i++) {
	        arr[arr.length] = items[i];
	    }
	}
	var arr1 = [1,2,3];
	push(arr1, 4,5,6);
	arr1                // => [1, 2, 3, 4, 5, 6]
```
- 既然说到类数组对象arguments, 顺便来个类数组对象与数组对象的辨别吧.
1. 类数组有哪些？ arguments, NodeList，HTML Collections,字符串
2. 类数组和数组对象都有length属性，可通过Array.prototype.xxx.call(类数组,其他参数1,其他参数2...)的方式调用slice, indexOf, lastIndexOf, forEach方法。不同的类数组会不能使用push, pop, splice, shift等方法的其中几种。
- NodeList，HTML Collections,字符串 只能使用Array.prototype.slice.call(NodeList),上面列举的方法均不能使用
- arguments可通过Array.prototype.xxx.call使用上面列举的方法
#### 闭包
> 词法作用域（lexical scoping）的执行依赖于变量作用域，这个作用域是在函数 定义时 决定的，而不是函数调用时，为了实现这种词法作用域，JavaScript 函数对象的内部状态不仅包含函数的代码逻辑，还必须引用当前的作用域链。函数对象可以通过作用域链相互关联起来，函数体内部的变量都可以保存在函数作用域内，这种特性在计算机科学文献中称为「闭包」
```
	// 例 1
	var scope = "global scope";
	function checkscope() {
	    var scope = "local scope";
	    function f() { return scope;}
	    return f(); //注意这里和例2的区别
	}
	checkscope();       // => "local scope" 注意这里函数调用和例2的区别
	// 例 2
	var scope = "global scope";
	function checkscope() {
	    var scope = "local scope";
	    function f() { return scope;}
	    return f; //注意这里和例1的区别
	}
	checkscope()()      // => "local scope" 注意这里函数调用和例1的区别
```
- JavaScript 函数的执行用到了作用域链，这个作用域链是函数 定义的时候 创建的，嵌套的函数 f() 定义在这个作用域链里，其中的变量 scope 一定是局部变量，不管在何时执行函数f()，这种绑定在执行 f() 时依然有效。因此最后一行代码返回「local scope」而不是「global scope」。简而言之，闭包的有个强大特性：它可以捕捉到局部变量（和参数），并一直保存下来

##### 趁热打铁，再来举个栗子~
```
	function counter() {
	    var n = 0;
	    return {
	        count: function() { return n++ },
	        reset: function() { n = 0 }
	    }
	}
	// 创建两个计数器
	var c = counter(), d = counter();
	c.count()       // => 0 互
	c.count()       // => 1 不
	d.count()       // => 0 干
	d.count()       // => 1 扰
	c.reset()       // => 0 重置 c
	d.count()       // => 2 不影响 d
```