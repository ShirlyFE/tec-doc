# React VS Redux VS Mobox

## Presentational and Container Components
You’ll find your components much easier to reuse and reason about if you divide them into two categories. I call them Container and Presentational components* but I also heard Fat and Skinny, Smart and Dumb, Stateful and Pure, Screens and Components, etc. These all are not exactly the same, but the core idea is similar.


React 只是 DOM 的一个抽象层，并不是 Web 应用的完整解决方案。有两个方面，它没涉及。
    代码结构
    组件之间的通信

使用 Flux 组织代码和安排内部逻辑，使得你的应用更易于开发和维护。

Flux 是一种架构思想，专门解决软件的结构问题。它跟MVC 架构是同一类东西，但是更加简单和清晰

Flux将一个应用分成四个部分。
    View： 视图层
    Action（动作）：视图层发出的消息（比如mouseClick）
    Dispatcher（派发器）：用来接收Actions、执行回调函数
    Store（数据层）：用来存放应用的状态，一旦发生变动，就提醒Views要更新页面

Flux 的最大特点，就是数据的"单向流动"。
    用户访问 View
    View 发出用户的 Action
    Dispatcher 收到 Action，要求 Store 进行相应的更新
    Store 更新后，发出一个"change"事件
    View 收到"change"事件后，更新页面

[flux 架构入门教程](http://www.ruanyifeng.com/blog/2016/01/flux.html)

## Redux
Redux 是 JavaScript 状态容器，提供可预测化的状态管理
Reducer 函数最重要的特征是，它是一个纯函数。也就是说，只要是同样的输入，必定得到同样的输出。
    纯函数是函数式编程的概念，必须遵守以下一些约束。
        不得改写参数
        不能调用系统 I/O 的API
        不能调用Date.now()或者Math.random()等不纯的方法，因为每次会得到不一样的结果
    由于 Reducer 是纯函数，就可以保证同样的State，必定得到同样的 View。但也正因为这一点，Reducer 函数里面不能改变 State，必须返回一个全新的对象
    ```
    // State 是一个对象
    function reducer(state, action) {
        return Object.assign({}, state, { thingToChange });
        // 或者
        return { ...state, ...newState };
    }

    // State 是一个数组
    function reducer(state, action) {
        return [...state, newItem];
    }
    ```
    最好把 State 对象设成只读。你没法改变它，要得到新的 State，唯一办法就是生成一个新对象。这样的好处是，任何时候，与某个 View 对应的 State 总是一个不变的对象。

### store.subscribe()
    Store 允许使用store.subscribe方法设置监听函数，一旦 State 发生变化，就自动执行这个函数。
    ```
    import { createStore } from 'redux';
    const store = createStore(reducer);

    store.subscribe(listener);
    显然，只要把 View 的更新函数（对于 React 项目，就是组件的render方法或setState方法）放入listen，就会实现 View 的自动渲染。
    store.subscribe方法返回一个函数，调用这个函数就可以解除监听。

    let unsubscribe = store.subscribe(() =>
        console.log(store.getState())
    );

    unsubscribe();
    ```

### 下面是createStore方法的一个简单实现，可以了解一下 Store 是怎么生成的。
```
const createStore = (reducer) => {
  let state;
  let listeners = [];

  const getState = () => state;

  const dispatch = (action) => {
    state = reducer(state, action);
    listeners.forEach(listener => listener());
  };

  const subscribe = (listener) => {
    listeners.push(listener);
    return () => {
      listeners = listeners.filter(l => l !== listener);
    }
  };

  dispatch({});

  return { getState, dispatch, subscribe };
};
```

## Reducer 的拆分
Reducer 函数负责生成 State。由于整个应用只有一个 State 对象，包含所有数据，对于大型应用来说，这个 State 必然十分庞大，导致 Reducer 函数也十分庞大。
请看下面的例子。
```
const chatReducer = (state = defaultState, action = {}) => {
  const { type, payload } = action;
  switch (type) {
    case ADD_CHAT:
      return Object.assign({}, state, {
        chatLog: state.chatLog.concat(payload)
      });
    case CHANGE_STATUS:
      return Object.assign({}, state, {
        statusMessage: payload
      });
    case CHANGE_USERNAME:
      return Object.assign({}, state, {
        userName: payload
      });
    default: return state;
  }
};
```
上面代码中，三种 Action 分别改变 State 的三个属性。
ADD_CHAT：chatLog属性
CHANGE_STATUS：statusMessage属性
CHANGE_USERNAME：userName属性
这三个属性之间没有联系，这提示我们可以把 Reducer 函数拆分。不同的函数负责处理不同属性，最终把它们合并成一个大的 Reducer 即可。
```
const chatReducer = (state = defaultState, action = {}) => {
  return {
    chatLog: chatLog(state.chatLog, action),
    statusMessage: statusMessage(state.statusMessage, action),
    userName: userName(state.userName, action)
  }
};
```
上面代码中，Reducer 函数被拆成了三个小函数，每一个负责生成对应的属性。
这样一拆，Reducer 就易读易写多了。而且，这种拆分与 React 应用的结构相吻合：一个 React 根组件由很多子组件构成。这就是说，子组件与子 Reducer 完全可以对应。
Redux 提供了一个combineReducers方法，用于 Reducer 的拆分。你只要定义各个子 Reducer 函数，然后用这个方法，将它们合成一个大的 Reducer。
```
import { combineReducers } from 'redux';

const chatReducer = combineReducers({
  chatLog,
  statusMessage,
  userName
})

export default todoApp;
```
上面的代码通过combineReducers方法将三个子 Reducer 合并成一个大的函数。
这种写法有一个前提，就是 State 的属性名必须与子 Reducer 同名。如果不同名，就要采用下面的写法。
```
const reducer = combineReducers({
  a: doSomethingWithA,
  b: processB,
  c: c
})
```

// 等同于
```
function reducer(state = {}, action) {
  return {
    a: doSomethingWithA(state.a, action),
    b: processB(state.b, action),
    c: c(state.c, action)
  }
}
```
总之，combineReducers()做的就是产生一个整体的 Reducer 函数。该函数根据 State 的 key 去执行相应的子 Reducer，并将返回结果合并成一个大的 State 对象。
```
const combineReducers = reducers => {
  return (state = {}, action) => {
    return Object.keys(reducers).reduce(
      (nextState, key) => {
        nextState[key] = reducers[key](state[key], action);
        return nextState;
      },
      {} 
    );
  };
};
```

## Middleware
为了理解中间件，让我们站在框架作者的角度思考问题：如果要添加功能，你会在哪个环节添加？
（1）Reducer：纯函数，只承担计算 State 的功能，不合适承担其他功能，也承担不了，因为理论上，纯函数不能进行读写操作。
（2）View：与 State 一一对应，可以看作 State 的视觉层，也不合适承担其他功能。
（3）Action：存放数据的对象，即消息的载体，只能被别人操作，自己不能进行任何操作。
想来想去，只有发送 Action 的这个步骤，即store.dispatch()方法，可以添加功能

举例来说，要添加日志功能，把 Action 和 State 打印出来，可以对store.dispatch进行如下改造。
```
let next = store.dispatch;
store.dispatch = function dispatchAndLog(action) {
  console.log('dispatching', action);
  next(action);
  console.log('next state', store.getState());
}
```
上面代码中，对store.dispatch进行了重定义，在发送 Action 前后添加了打印功能。这就是中间件的雏形。
中间件就是一个函数，对store.dispatch方法进行了改造，在发出 Action 和执行 Reducer 这两步之间，添加了其他功能。

```
export default function applyMiddleware(...middlewares) {
  return (createStore) => (reducer, preloadedState, enhancer) => {
    var store = createStore(reducer, preloadedState, enhancer);
    var dispatch = store.dispatch;
    var chain = [];

    var middlewareAPI = {
      getState: store.getState,
      dispatch: (action) => dispatch(action)
    };
    chain = middlewares.map(middleware => middleware(middlewareAPI));
    dispatch = compose(...chain)(store.dispatch);

    return {...store, dispatch}
  }
}
```

## react-redux
React-Redux 将所有组件分成两大类：UI 组件（presentational component）和容器组件（container component）。
UI 组件有以下几个特征。
    只负责 UI 的呈现，不带有任何业务逻辑
    没有状态（即不使用this.state这个变量）
    所有数据都由参数（this.props）提供
    不使用任何 Redux 的 API

容器组件的特征恰恰相反。
    负责管理数据和业务逻辑，不负责 UI 的呈现
    带有内部状态
    使用 Redux 的 API
React-Redux 提供connect方法，用于从 UI 组件生成容器组件。connect的意思，就是将这两种组件连起来。
```
    import { connect } from 'react-redux'
    const VisibleTodoList = connect()(TodoList);
```
上面代码中，TodoList是 UI 组件，VisibleTodoList就是由 React-Redux 通过connect方法自动生成的容器组件。
但是，因为没有定义业务逻辑，上面这个容器组件毫无意义，只是 UI 组件的一个单纯的包装层。为了定义业务逻辑，需要给出下面两方面的信息。
（1）输入逻辑：外部的数据（即state对象）如何转换为 UI 组件的参数
（2）输出逻辑：用户发出的动作如何变为 Action 对象，从 UI 组件传出去。
因此，connect方法的完整 API 如下。
```
    import { connect } from 'react-redux'

    const VisibleTodoList = connect(
    mapStateToProps,
    mapDispatchToProps
    )(TodoList)
```

上面代码中，connect方法接受两个参数：mapStateToProps和mapDispatchToProps。它们定义了 UI 组件的业务逻辑。前者负责输入逻辑，即将state映射到 UI 组件的参数（props），后者负责输出逻辑，即将用户对 UI 组件的操作映射成 Action

mapStateToProps会订阅 Store，每当state更新的时候，就会自动执行，重新计算 UI 组件的参数，从而触发 UI 组件的重新渲染

mapDispatchToProps是connect函数的第二个参数，用来建立 UI 组件的参数到store.dispatch方法的映射。也就是说，它定义了哪些用户的操作应该当作 Action，传给 Store。它可以是一个函数，也可以是一个对象

如果mapDispatchToProps是一个函数，会得到dispatch和ownProps（容器组件的props对象）两个参数。
```
const mapDispatchToProps = (
  dispatch,
  ownProps
) => {
  return {
    onClick: () => {
      dispatch({
        type: 'SET_VISIBILITY_FILTER',
        filter: ownProps.filter
      });
    }
  };
}
```

从上面代码可以看到，mapDispatchToProps作为函数，应该返回一个对象，该对象的每个键值对都是一个映射，定义了 UI 组件的参数怎样发出 Action。
如果mapDispatchToProps是一个对象，它的每个键名也是对应 UI 组件的同名参数，键值应该是一个函数，会被当作 Action creator ，返回的 Action 会由 Redux 自动发出。

### <Provider> 组件
connect方法生成容器组件以后，需要让容器组件拿到state对象，才能生成 UI 组件的参数。
一种解决方法是将state对象作为参数，传入容器组件。但是，这样做比较麻烦，尤其是容器组件可能在很深的层级，一级级将state传下去就很麻烦。
React-Redux 提供Provider组件，可以让容器组件拿到state。
```
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import todoApp from './reducers'
import App from './components/App'

let store = createStore(todoApp);

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

上面代码中，Provider在根组件外面包了一层，这样一来，App的所有子组件就默认都可以拿到state了。
它的原理是React组件的context属性，请看源码。

class Provider extends Component {
  getChildContext() {
    return {
      store: this.props.store
    };
  }
  render() {
    return this.props.children;
  }
}

Provider.childContextTypes = {
  store: React.PropTypes.object
}
上面代码中，store放在了上下文对象context上面。然后，子组件就可以从context拿到store，代码大致如下。

class VisibleTodoList extends Component {
  componentDidMount() {
    const { store } = this.context;
    this.unsubscribe = store.subscribe(() =>
      this.forceUpdate()
    );
  }

  render() {
    const props = this.props;
    const { store } = this.context;
    const state = store.getState();
    // ...
  }
}

VisibleTodoList.contextTypes = {
  store: React.PropTypes.object
}
React-Redux自动生成的容器组件的代码，就类似上面这样，从而拿到store



## react-router
Router组件本身只是一个容器，真正的路由要通过Route组件定义。

import { Router, Route, hashHistory } from 'react-router';

render((
  <Router history={hashHistory}>
    <Route path="/" component={App}/>
  </Router>
), document.getElementById('app'));

[react-router 使用教程](http://www.ruanyifeng.com/blog/2016/05/react_router.html)


## Typescript vs Flow
Typescript可以基于定义的类型进行类型检查，Flow可以推断变量类型


### 静态类型系统与 Lint 工具的关系
Code linting is a type of static analysis that is frequently used to find problematic patterns or code that doesn’t adhere to certain style guidelines.
Lint 工具是一种团队协作时的风格规范工具。Lint 工具无法基于类型对程序进行静态分析

TypeScript 的语法设计首先考虑的就是兼容 JavaScript，或者说对 JavaScript 的语法做扩展。基本上是在 JavaScript 的基础之上增加了一些类型标记语法，以实现静态类型分析。把这些类型标注语法去掉之后，仍是一个标准的 JavaScript 语言

### 类型系统的益处
+ 及时检测类型错误
+ 抽象
类型系统的另一个优点是强化规范编程，TypeScript 提供了简便的方式定义接口。这一点在大型软件开发时尤为重要，一个系统模块可以抽象的看做一个 TypeScript 定义的接口

用带清晰接口的模块来结构化大型系统，这是一种更为抽象的设计形式。接口设计（讨论）与最终实现方式无关，对接口思考得越抽象越有利。

换句话说就是让设计脱离实现，最终体现出一种 IDL（接口定义语言，Interface Define Language），让程序设计回归本质。
+ 文档
读程序时类型标注也有用处，不止是说人在读的时候。基于类型定义 IDE 可以对我们进行很多辅助，比如找到一个函数所有的使用，编写代码时对参数进行提示等等。
可以自动根据类型标注产生文档，甚至都不需要编写注释

### 原理
TypeScript体系结构：AST导向
TypeScript将遍历您的程序并构建已知类型的表。当它发现值和表达式时，它会立即为其分配类型。当TypeScript发现一个未知的类型时，它必须立即作出决定，这意味着将其分配给any或抛出错误。

Flow 架构：图形导向
Flow 将建立一个你所有的值和表达式及其彼此之间的关系的图表。然后，它将开始为每个值和表达式分配类型。如果它找到一个未知的类型，它将使它成为一个“开放”类型，稍后再回来判断。

一旦Flow具有您的程序的完整蓝图，它将开始连接所有点，从一个值连接到另一个值地跟踪类型。打开类型接受流入它们的所有值的类型 - 生成的类型称为 “推断类型”。



### Issues
[Why redux suggests to only connect to top level components?](https://stackoverflow.com/questions/34214208/why-redux-suggests-to-only-connect-to-top-level-components)

[contextTypes/ childContextTypes/getChildContext ](https://segmentfault.com/a/1190000002878442)


[Optimizing React: Virtual DOM explained](https://evilmartians.com/chronicles/optimizing-react-virtual-dom-explained)

[Flow和TypeScript之间的区别和优劣](https://robin-front.github.io/2017/06/14/compare-with-Flow-and-TypeScript.html)

[flux vs redux vs mobx](http://zhenhua-lee.github.io/react/state-manage.html)

[Redux 还是 Mobx，让我来解决你的困惑！](https://segmentfault.com/a/1190000011148981)