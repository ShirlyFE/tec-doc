# Immutable

## Immutable详解以及搭配react进行性能优化
当前的JavaScript语言存在的两个重要问题
JavaScript语言对于对象和数组，采用了引用赋值的方式，这样做的好处，就是节省了内存，我们称这样的方式是Mutable。但是这也是一切问题的万恶之源。当一个项目越来越复杂的时候，Mutable带来的内存优势，几乎就是个鸡肋般的存在了。

var a = { name: 'mapbar_front', age: 27 };
var b = a;
b.name = 'web_front';
console.log(a);//我们发现a对象也跟着改变。

第二个问题： 
虽然我们可以进行shadowCopy（浅拷贝）和deepCopy（深拷贝），但是这样会造成CPU和内存的浪费。

Immutable就是来解决这样的问题的。

Immutable Data
什么是Immutable Data，就是一旦被创建，它就不可变的一种数据结构。

对Immutable对象的任何操作（添加、修改、删除）都会生成一个新的Immutable对象。

Immutable的实现原理是——Pressistent Data Structrue（持久化数据结构），也就是使用旧数据创建新数据的时候，旧数据不变并且可以使用。

deepCopy也是一种常用的操作，为了解决它浪费性能的问题，Immutable使用了一种Structural Sharing结构共享的方式来尽量复用内存，甚至以前使用的对象也可以再次被复用。也就是创建新的对象的时候，只改变受影响的节点，其他节点使用共享的方式进行数据存储。 

!./Structural Sharing.gif!

Immutable.js
Immutable.js是解决JavaScript的引用类型存在的问题的一种解决方案，作为一个js库，它主要有这么几个核心点。

第一、重要的几个数据类型：

Map：键值对集合，对应Object。
List：可重复的有序列表。对应Array。
Set：不重复并且无序的列表。
第二、容易比较：

var map1 = Immutable.map({ a: 1,b: 1,c: 1 });
var map2 = Immutable.map({ a: 1,b: 1,c: 1 });
console.log(map1 === map2);//返回false。

使用 is 进行“值”比较。

var map1 = Immutable.map({ a: 1,b: 1,c: 1 });
var map2 = Immutable.map({ a: 1,b: 1,c: 1 });
Immutable.is(map1, map2);//true

第三、Immutable.fromJs()，把一个js对象转化为Immutable对象。Immutable.toJs()，把一个Immutable对象转化为js对象。

var $$list = Immutable.fromJS([1,2,3]);
var list = Immutable.toJS($$list);

第四、List()以及Map()用来创建List和Map类型的Immutable对象

const plainArray = [ 1, 2, 3, 4 ]
const listFromPlainArray = List(plainArray)

第五、isList()、isMap()。判断类型。

List.isList([]); // false
List.isList(List()); // true

搭配react进行性能优化
在react中，进行性能优化的方面，主要是在 shouldComponentUpdate 这个周期函数中，在默认的情况下，它总是会返回一个true，执行render方法，并且进行 virtrue DOM比较，但是这里往往进行了很多无用的渲染，并最终成为react的性能瓶颈。

当然我们也可以在 shouldComponentUpdate() 中使用使用 deepCopy 和 deepCompare 来避免无必要的 render()，但 deepCopy 和 deepCompare 一般都是非常耗性能的。

Immutable 则提供了简洁高效的判断数据是否变化的方法，只需 === 和 is 比较就能知道是否需要执行 render()，而这个操作几乎 0 成本，所以可以极大提高性能。修改后的 shouldComponentUpdate 是这样的：

import { is } from 'immutable';

shouldComponentUpdate: (nextProps = {}, nextState = {}) => {
  const thisProps = this.props || {}, thisState = this.state || {};

  if (Object.keys(thisProps).length !== Object.keys(nextProps).length ||
      Object.keys(thisState).length !== Object.keys(nextState).length) {
    return true;
  }

  for (const key in nextProps) {
    if (!is(thisProps[key], nextProps[key])) {
      return true;
    }
  }

  for (const key in nextState) {
    if (thisState[key] !== nextState[key] || !is(thisState[key], nextState[key])) {
      return true;
    }
  }
  return false;
}

## PureComponent
「只要 state 跟 props 没变，就不会 re-render，PureComponent 就会比 Component 更有效率」。但其实还有另外一句话也是对的：「如果你的 state 或 props 『永远都会变』，那 PureComponent 并不会比较快」

最后再强调一次，如果你已经预期到某个 component 的 props 或是 state 会「很频繁变动」，那你根本不用换成 PureComponent，因为你实作之后反而会变得更慢。

### 拥抱函数式编程
Immutable 本身就是函数式编程中的概念，纯函数式编程比面向对象更适用于前端开发。因为只要输入一致，输出必然一致，这样开发的组件更易于调试和组装

Immutable.is
两个 immutable 对象可以使用 === 来比较，这样是直接比较内存地址，性能最好。但即使两个对象的值是一样的，也会返回 false：

let map1 = Immutable.Map({a:1, b:1, c:1});
let map2 = Immutable.Map({a:1, b:1, c:1});
map1 === map2;             // false
为了直接比较对象的值，immutable.js 提供了 Immutable.is 来做『值比较』，结果如下：

Immutable.is(map1, map2);  // true
Immutable.is 比较的是两个对象的 hashCode 或 valueOf（对于 JavaScript 对象）。由于 immutable 内部使用了 Trie 数据结构来存储，只要两个对象的 hashCode 相等，值就是一样的。这样的算法避免了深度遍历比较，性能非常好。

