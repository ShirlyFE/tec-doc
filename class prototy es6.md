在面向类的语言里，继承指从父类复制属性到子类的行为。在 JavaScript 里，没发生这种复制的操作，事实上这就是原型继承与类继承相比的一个主要优势。

es6和es5继承的区别

大多数浏览器的ES5实现之中，每一个对象都有__proto__属性，指向对应的构造函数的prototype属性。Class作为构造函数的语法糖，同时有prototype属性和__proto__属性，因此同时存在两条继承链。

（1）子类的__proto__属性，表示构造函数的继承，总是指向父类。

（2）子类prototype属性的__proto__属性，表示方法的继承，总是指向父类的prototype属性。

class A {
}

class B extends A {
}

B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
上面代码中，子类B的__proto__属性指向父类A，子类B的prototype属性的__proto__属性指向父类A的prototype属性。

这样的结果是因为，类的继承是按照下面的模式实现的:

class A {
}

class B {
}

// B的实例继承A的实例
Object.setPrototypeOf(B.prototype, A.prototype);

// B继承A的静态属性
Object.setPrototypeOf(B, A);
Object.setPrototypeOf的简单实现如下：

Object.setPrototypeOf = function (obj, proto) {
  obj.__proto__ = proto;
  return obj;
}

这两条继承链，可以这样理解：作为一个对象，子类（B）的原型（__proto__属性）是父类（A）；作为一个构造函数，子类（B）的原型（prototype属性）是父类的实例。


多态（Polymorphism）按字面的意思就是“多种状态”。在面向对象语言中，接口的多种不同的实现方式即为多态。这是标准定义，在c++中实现多态的方式有虚函数、抽象类、模板，在java中更粗暴，所有函数都是“虚”的，子类都可以重写，当然java中没有虚函数的概念，我们暂且把相同签名的、子类和父类可以有不同实现的函数称之为虚函数，虚函数和模版（java中的范型）是支持多态的主要方式，因为javascript中没有模版，所以下面我们只讨论虚函数，下面先看一个例子：

function Person(name,age){
 this.name=name
 this.age=age
}
Person.prototype.toString=function(){
 return "I am a Person, my name is "+ this.name
}
function Man(name,age){
  Person.apply(this,arguments)
}
Man.prototype = Object.create(Person.prototype);
Man.prototype.toString=function(){
  return "I am a Man, my name is"+this.name;
}
var person=new Person("Neo",19)
var man1=new Man("Davin",18)
var man2=new Man("Jack",19)
> person+""
> "I am a Person, my name is Neo"
> man1+""
> "I am a Man, my name isDavin"
> man1<man2 //期望比较年龄大小 1
> false
上面例子中，我们分别在子类和父类实现了toString方法，其实，在js中上述代码原理很简单，对于同名函数，子类会覆父类的，这种特性其实就是虚函数，只不过js中不区分参数个数，也不区分参数类型，只看函数名称，如果名称相同就会覆盖。现在我们来看注释1，我们期望直接用比较运算符比较两个man的大小（按年龄），怎么实现？在c++中有运算符重载，但java和js中都没有，所幸的是，js可以用一种变通的方法来实现：

function Person(name,age){
 this.name=name
 this.age=age
}
Person.prototype.valueOf=function(){
 return this.age
}
function Man(name,age){
  Person.apply(this,arguments)
}

Man.prototype = Object.create(Person.prototype);
var person=new Person("Neo",19)
var man1=new Man("Davin",18)
var man2=new Man("Jack",19)
var man3=new Man("Joe",19)

>man1<19//1
>true
>person==19//2
>true
>man1<man2//3
>true
>man2==man3 //4 注意
>true
>person==man2//5
>false
其中1、2、3、5在所有js vm下结果都是确定的。但是4并不一定！javascript规定，对于比较运算符，如果一个值是对象，另一个值是数字时，会先尝试调用valueOf，如果valueOf未指定，就会调用toString；如果是字符串时，则先尝试调用toString，如果没指定，则尝试valueOf，如果两者都没指定，将抛出一个类型错误异常。如果比较的两个值都是对象时，则比较的时对象的引用地址，所以若是对象，只有自身＝＝＝自身，其它情况都是false。现在我们回过头来看看示例代码，前三个都是标准的行为。而第四点取决于浏览器的实现，如果严格按照标准，这应该算是chrome的一个bug ,但是，我们的代码使用时双等号，并非严格相等判断，所以浏览器的相等规则也会放宽。值得一提的是5，虽然person和man2 age都是19，但是结果却是false。总结一下，chrome对相同类的实例比较策略是先会尝试转化，然后再比较大小，而对非同类实例的比较，则会直接返回false，不会做任何转化。 所以我的建议是：如果数字和类实例比较，永远是安全的，可以放心玩，如果是同类实例之间，可以进行非等比较，这个结果是可以保证的，不要进行相等比较，结果是不能保证的，一般相等比较，变通的做法是：

var equal= !(ob1<ob2||ob1>ob2) 
//不小于也不大于，就是等于，前提是比较操作符两边的对象要实现valueOf或toString

