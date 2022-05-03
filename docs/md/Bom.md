```js
this	指向事件函数调用者
全局作用域或普通函数中this指向全局对象window
方法调用中谁调用this就指向谁
// this 指向问题 一般情况下this的最终指向的是那个调用它的对象

// 1. 全局作用域或者普通函数中this指向全局对象window（ 注意定时器里面的this指向window） 
// 箭头函数的定时器指向当前调用者
console.log(this);

function fn() {
    console.log(this);

}
window.fn();
window.setTimeout(function() {
    console.log(this);

}, 1000);

// 2. 方法调用中谁调用this指向谁
var o = {
    sayHi: function() {
        console.log(this); // this指向的是 o 这个对象

    }
}
o.sayHi();
var btn = document.querySelector('button');
// btn.onclick = function() {
//     console.log(this); // this指向的是btn这个按钮对象

// }
btn.addEventListener('click', function() {
    console.log(this); // this指向的是btn这个按钮对象

})

// 3. 构造函数中this指向构造函数的实例
function Fun() {
    console.log(this); // this 指向的是fun 实例对象

}
var fun = new Fun();
```

```javascript
js执行机制
同步任务	同步任务都在主线程上执行，形成一个执行栈
异步任务	回调函数 定时器
先执行执行栈中的同步任务。
异步任务（回调函数）放入任务队列中。
一旦执行栈中的所有同步任务执行完毕，系统就会按次序读取任务队列中的异步任务，于是被读取的异步任务结束等待状态，进入执行栈，开始执行。
由于主线程不断的重复获得任务、执行任务、再获取任务、再执行，所以这种机制被称为事件循环（ event loop）。
```

<img src="C:\Users\86135\Pictures\微信图片_20220423185131.png" style="zoom:150%;" />

```js
立即执行函数 独立创建一个作用域 里面所有变量为局部变量
(function(){})()	(function(){}())
```

```
三大系列
offset系列 经常用于获得元素位置    offsetLeft  offsetTop
client 经常用于获取元素大小  clientWidth  clientHeight
scroll 经常用于获取滚动距离  scrollTop  scrollLeft   
注意页面滚动的距离通过 window.pageXOffset  获得
```

