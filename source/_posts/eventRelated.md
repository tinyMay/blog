---
title: 事件监听，捕获，冒泡
description: "    事件监听addEventListener与attachEvent是什么？其中的冒泡，捕获的实现机制？addEventListener与普通的onevent(如onclick）相比有什么优点?target与currentTarget有何区别？如果你也有这些疑问的话，不妨往下读读~"
categories: 前端
tags: [前端,事件监听,捕获,冒泡]
---

## addEventListener与attachEvent科普

> addEventListener()是标准的绑定事件监听函数的方法，是W3C所支持的，Chrome、FireFox、Opera、Safari、IE9.0及其以上版本都支持该函数；但是，IE8.0及其以下版本不支持该方法，它使用attachEvent()来绑定事件监听函数。所以，这种绑定事件的方法必须要处理浏览器兼容问题。

`addEventListener有三个参数`
element.addEventListener(type, listener, useCapture);

|参数|参数说明|
|--|--|
|element |要绑定事件的对象，及HTML节点，注意不能用$(body)形式,而要用原生的document.getElementById("xx")的去节点形式，用jQuery的话有$(body).on(type, handle)形式|
|type    |事件名称，不要加'on',比如'onclick'要写成'click'|
|listener|要绑定的事件监听函数，写函数名就好了，不要加括号，加括号的话绑定时就执行了，且做相应事件时不会再触发了|
|useCapture|事件监听方式，默认是false, 只能是true(捕获模式)或false(冒泡模式)|

`attachEvent有二个参数`
element.attachEvent(type, listener);（此方法没有第三个参数相关设置，不过IE的事件模型默认是在事件冒泡时执行的，也就是在useCapture等于false的时候执行）


|参数|参数说明|
|--|--|
|element |要绑定事件的对象，及HTML节点，注意不能用$(body)形式,而要用原生的document.getElementById("xx")的去节点形式，用jQuery的话有$(body).on(type, handle)形式|
|type    |事件名称，要加'on',比如'onclick'|
|listener|要绑定的事件监听函数，写函数名就好了，不要加括号，加括号的话绑定时就执行了，且做相应事件时不会再触发了|

#### 兼容IE和非IE浏览器事件绑定代码:
```
	function addEvent(obj, type, handle, useCapture) {
		try { //Chrome、FireFox、Opera、Safari、IE9.0及其以上版本
			obj.addEventListener(type, handle, useCapture);
		} catch(e) {
			try { //IE8.0及其以下版本
				obj.attachEvent('on' + type, handle);
			} catch(e) { //早期浏览器
				obj['on' + type] = handle;
			}
		}
	}
```

#### 冒泡与捕获在addEventListener中的应用

> 通俗地讲，冒泡是由内向外即由子到父，捕获是由外向内即由父到子

![捕获与冒泡演示](http://oli108cm6.bkt.clouddn.com/maopao.png)

设置Dom和函数

```
function grandpa() {
    console.log("I am  grandpa");
}

function father() {
    console.log("I am  father");
}

function child() {
    console.log("I am child");
}

function grandson() {
    console.log("I am  grandson");
}

var grandpa = document.getElementById("grandpa"); 
var father = document.getElementById("father"); 
var child = document.getElementById("child"); 
var grandson = document.getElementById("grandson");

```
为grandpa和grandson绑定事件, 使用冒泡模式，即先触发绑定了相同触发条件（如click）的子元素事件，再逐级向外触发父元素事件

```
例1:
grandpa.addEventListener("click", grandpa);（第三个参数不写就是默认false, 冒泡模式）
grandson.addEventListener("click", grandson);

点击grandson,结果为:
I am  grandson
I am  grandpa

点击grandpa,并没有涉及到到grandson区域,结果为:
I am  grandpa

```
为grandpa和grandson绑定事件, 使用捕获模式, 即先触发绑定了相同触发条件（如click）的父元素事件，再逐级向内触发子元素事件

```
grandpa.addEventListener("click", grandpa, true);
grandson.addEventListener("click", grandson, true);

点击grandson,结果为:
I am  grandpa
I am  grandson

点击grandpa,并没有涉及到到grandson区域,结果为:

I am  grandpa

```

#### 父子元素冒泡与捕获混用怎么办

在上述绑定事件的代码中，第三个参数全都设置成了true或false,那如果既有true，又有false，即有的元素设置成按事件冒泡处理，有的元素设置成按事件捕获处理，那怎么办呢？

来揭晓吧，浏览器更“喜爱”事件捕获：它会先把useCapture为false的元素绑定事件放到一边，按照事件捕获正常的顺序执行useCapture为true的元素绑定事件，最后在按照事件冒泡顺序执行useCapture为false的元素绑定事件

```
例2:
grandpa.addEventListener("click", grandpa, true);
father.addEventListener("click", father, false);
child.addEventListener("click", child, true);
grandson.addEventListener("click", grandson, false);

点击grandson时，先执行useCapture为true的元素的绑定事件，
又按照事件捕获原则，先执行grandpa的事件，再执行child的事件。
之后，再按照事件捕获顺序执行useCapture为false的事件，输出结果如下：

I am  grandpa
I am  child
I am  grandson
I am  father

点击grandpa时，并没有涉及到到father, child, grandson区域,所以输出如下：
I am grandpa

```

#### 阻止事件冒泡和捕获
我们可以利用事件对象event的stopPropagation（）方法阻止事件的进一步传播。
我们修改一下grandson函数：
```
function grandson(event) {
    // 阻止事件的进一步传播，但仍旧会执行接下来的代码
    event.stopPropagation();
    console.log("I am grandson");
}
```
沿用上面例2的事件绑定,这时我们点击grandson，控制台输出的结果是：
I am grandpa
I am child
I am grandson

```

#### addEventListener比普通的onevent(如onclick，onhover)的事件绑定区别何在呢？

1. addEventListener可以为同一种事件绑定多个函数，onevent只能绑定一种，后绑定的会覆盖掉先绑定的
2. addEventListener可以在第三个参数上自由定义捕获或冒泡方式，而onevent无此申明参数，是捕获还是冒泡在不同浏览其中可能表现不同
3. 包括HTML元素的所有DOM都可以绑定addEventListener，都可以，而只有HTML元素可以绑定onevent

#### target与currentTarget
> 简言之，target是事件的调用对象(event dispatcher)，currentTarget是事件的处理对象(event processor)
> target在事件流的目标阶段；currentTarget在事件流的捕获，目标及冒泡阶段。只有当事件流处在目标阶段的时候，两个的指向才是一样的，而当处于捕获和冒泡阶段的时候，target指向被单击的对象而currentTarget指向当前事件活动的对象（一般为父级）。

话不多说，上例子
```
例3
grandpa.addEventListener("click", grandpa, false/true);
(注意，这里除了grandpa并没有和其有包含关系的元素做相同事件click的监听函数绑定，
故第三个参数无论冒泡或捕获并无影响)

点击child，由于child在grandpa内，所以会触发grandpa（即target）绑定的click事件，此时child是currentTarget, 即target和currentTarget的指向是不同的

点击grandpa时，target和currentTarget均指向grandpa

```

