执行环境是javascript中最为重要的概念之一，执行环境定义了变量或函数有权访问其他数据
当执行环境内的代码执行完毕后，该环境被销毁，保存其中的变量和函数也随之销毁，如果是全局环境，需所有程序执行完毕或网页完毕后才会销毁。

当代码在一个环境中执行的时候，就会形成一种叫做作用域链的东西，它的用途是保证对执行环境中有访问权限的变量和函数进行有序访问（指按照规则层次来访问），作用域链的前端，就是执行环境的变量对象。

垃圾回收：
javascript具有自动垃圾回收机制，一旦数据不再使用，可以将其设为"null"来释放引用

循环引用
　　一个很简单的例子：一个DOM对象被一个Javascript对象引用，与此同时又引用同一个或其它的Javascript对象，这个DOM对象可能会引发内存泄露。这个DOM对象的引用将不会在脚本停止的时候被垃圾回收器回收。要想破坏循环引用，引用DOM元素的对象或DOM对象的引用需要被赋值为null。

闭包
在闭包中引入闭包外部的变量时，当闭包结束时此对象无法被垃圾回收（GC）。
```
var a = function() {
  var largeStr = new Array(1000000).join('x');
  return function() {
    return largeStr;
  }
}();
```

DOM泄露
当原有的COM被移除时，子结点引用没有被移除则无法回收。
```
var select = document.querySelector;
var treeRef = select('#tree');

//在COM树中leafRef是treeFre的一个子结点
var leafRef = select('#leaf'); 
var body = select('body');

body.removeChild(treeRef);

//#tree不能被回收入，因为treeRef还在
//解决方法:
treeRef = null;

//tree还不能被回收，因为叶子结果leafRef还在
leafRef = null;

//现在#tree可以被释放了。
```

Timers计（定）时器泄露
定时器也是常见产生内存泄露的地方：
```
for (var i = 0; i < 90000; i++) {
  var buggyObject = {
    callAgain: function() {
      var ref = this;
      var val = setTimeout(function() {
        ref.callAgain();
      }, 90000);
    }
  }

  buggyObject.callAgain();
  //虽然你想回收但是timer还在
  buggyObject = null;
}
```

堆内存存放引用值，栈内存存放固定类型值。“引用”是一个指向对象实际位置的指针


## 详解闭包
闭包 是指有权访问另一个函数作用域中的变量的函数，创建闭包的最常见的方式就是在一个函数内创建另一个函数，通过另一个函数访问这个函数的局部变量

一般函数执行完毕后，局部活动对象就被销毁，内存中仅仅保存全局作用域,但闭包会使变量始终保存在内存中，如果不当使用会增大内存消耗
```
 function aaa() {  
    var a = 1;  
    return function(){
    alert(a++)
    };  
}         
var fun = aaa();  
fun();// 1 执行后 a++，，然后a还在~  
fun();// 2   
fun = null;//a被回收！！ 
```


应用闭包场合主要是为了：设计私有的方法和变量

闭包的特性
闭包有三个特性：
    1.函数嵌套函数
    2.函数内部可以引用外部的参数和变量
    3.参数和变量不会被垃圾回收机制回收

闭包的好处是：
    1.希望一个变量长期驻扎在内存中
    2.避免全局变量的污染
    3.私有成员的存在
闭包的缺点就是常驻内存，会增大内存使用量，使用不当很容易造成内存泄露

javascript的垃圾回收原理：
（1）、在javascript中，如果一个对象不再被引用，那么这个对象就会被GC回收； 
（2）、如果两个对象互相引用，而不再被第3者所引用，那么这两个互相引用的对象也会被回收。


内存泄露问题
由于IE的js对象和DOM对象使用不同的垃圾收集方法，因此闭包在IE中会导致内存泄露问题，也就是无法销毁驻留在内存中的元素
```
function closure(){
    var oDiv = document.getElementById('oDiv');//oDiv用完之后一直驻留在内存中
    oDiv.onclick = function () {
        alert('oDiv.innerHTML');//这里用oDiv导致内存泄露
    };
}
closure();
//最后应将oDiv解除引用来避免内存泄露
function closure(){
    var oDiv = document.getElementById('oDiv');
    var test = oDiv.innerHTML;
    oDiv.onclick = function () {
        alert(test);
    };
    oDiv = null;
}
```