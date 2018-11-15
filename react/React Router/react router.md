# React Router

## history

TIL how to use location.replace() and location.assign() to change URLs programmatically in the browser.

location.assign() causes the browser to navigate to the URL provided. The provided URL is added to the browser’s history: a user can click the back button and navigate back to the page they were on before assign() was called.

location.replace() behaves almost identically to location.assign(), however instead of adding the provided URL to the browser’s history, replace() replaces the current URL in history with the one provided. This effectively erases the current URL from the browser’s history: the user won’t be able to click the back button to navigate back to the page they were on before replace() was called.

A small, but Important, Detail
The argument to either assign() or replace() can be a full URL, or a relative URI.

If a relative URI is passed, the browser will append the relative URI to the end of the base path for the current domain. A leading slash / in the provided URI will result in the entire pathname being replaced with the provided URI. If no leading / is provided, only the last portion of the pathname will be replaced! This caveat confused me for quite some time when I was trying to programmatically manipulate history in the context of a react-router application.

Check out the following examples to see the behavior:
```
// Full URL
// current URL: http://localhost:3000/users/1
location.assign('https://developer.mozilla.org/en-US/docs/Web/API/Location/assign').
// new URL: https://developer.mozilla.org/en-US/docs/Web/API/Location/assign

// Relative URI with leading slash
// current URL: http://localhost:3000/users/1
location.assign('/accounts/1').
// new URL: http://localhost:3000/accounts/1

// Relative URI without leading slash
// current URL: http://localhost:3000/users/1
location.assign('2').
// new URL: http://localhost:3000/users/2
```

## 自己动手实现react router
为什么需要路由系统
最开始的时候网页都是多页面的，后来随着 ajax 技术的出现，才慢慢有了像 React、Vue 等 SPA 框架，当然，缺少路由系统的 SPA 框架有其存在的弊端：

用户在使用过程中，url 不会发生变化，那么用户在进行多次跳转之后，如果一不小心刷新了页面，又会回到最开始的状态，用户体验极差。
由于缺乏路由，不利于 SEO，搜索引擎进行收录。
主流的前端路由系统是通过 hash 或 history 来实现的，下面我们一探究竟。

Hash 路由
url 上的 hash 以 # 开头，原本是为了作为锚点，方便用户在文章导航到相应的位置。因为 hash 值的改变不会引起页面的刷新，聪明的程序员就想到用 hash 值来做单页面应用的路由，并且当 url 的 hash 发生变化的时候，可以触发相应 hashchange 回调函数。

所以我们可以写一个 Router 对象，代码如下：

class Router {
  constructor() {
    this.routes = {};
    this.currentUrl = '';
  }
  route(path, callback) {
    this.routes[path] = callback || function() {};
  }
  updateView() {
    this.currentUrl = location.hash.slice(1) || '/';
    this.routes[this.currentUrl] && this.routes[this.currentUrl]();
  }
  init() {
    window.addEventListener('load', this.updateView.bind(this), false);
    window.addEventListener('hashchange', this.updateView.bind(this), false);
  }
}
routes 用来存放不同路由对应的回调函数
init 用来初始化路由，在 load 事件发生后刷新页面，并且绑定 hashchange 事件，当 hash 值改变时触发对应回调函数
<div id="app">
  <ul>
    <li>
      <a href="#/">home</a>
    </li>
    <li>
      <a href="#/about">about</a>
    </li>
    <li>
      <a href="#/topics">topics</a>
    </li>
  </ul>
  <div id="content"></div>
</div>
<script src="js/router.js"></script>
<script>
  const router = new Router();
  router.init();
  router.route('/', function () {
    document.getElementById('content').innerHTML = 'Home';
  });
  router.route('/about', function () {
    document.getElementById('content').innerHTML = 'About';
  });
  router.route('/topics', function () {
    document.getElementById('content').innerHTML = 'Topics';
  });
</script>
在对应 html 中，只要将所有链接路径前加 # 即可做成软路由，而不去触发刷新页面。

读者可以尝试并查看效果：

yarn hash
// open http://localhost:8080


值得注意的是在第一次进入页面的时候，需要触发一次 onhashchange 事件，保证页面能够正常显示，用 hash 在做路由跳转的好处在于简单实用，便于理解，但是它虽然解决解决单页面应用路由控制的问题，但是在 url 却引入 # 号，不够美观。

History 路由
History 路由是基于 HTML5 规范，在 HTML5 规范中提供了 history.pushState || history.replaceState 来进行路由控制。

当你执行 history.pushState({}, null, '/about') 时候，页面 url 会从 http://xxxx/ 跳转到 http://xxxx/about 可以在改变 url 的同时，并不会刷新页面。

先来简单看看 pushState 的用法，参数说明如下：

state：存储 JSON 字符串，可以用在 popstate 事件中
title：现在大多浏览器忽略这个参数，直接用 null 代替
url：任意有效的 URL，用于更新浏览器的地址栏
这么说下来 history 也有着控制路由的能力，然后，hash 的改变可以出发 onhashchange 事件，而 history 的改变并不会触发任何事件，这让我们无法直接去监听 history 的改变从而做出相应的改变。

所以，我们需要换个思路，我们可以罗列出所有可能触发 history 改变的情况，并且将这些方式一一进行拦截，变相地监听 history 的改变。

对于一个应用而言，url 的改变(不包括 hash 值得改变)只能由下面三种情况引起：

点击浏览器的前进或后退按钮
点击 a 标签
在 JS 代码中触发 history.push(replace)State 函数
只要对上述三种情况进行拦截，就可以变相监听到 history 的改变而做出调整。针对情况 1，HTML5 规范中有相应的 onpopstate 事件，通过它可以监听到前进或者后退按钮的点击，值得注意的是，调用 history.push(replace)State 并不会触发 onpopstate 事件。

经过分析，下面就简单实现一个 history 路由系统，代码如下：

class Router {
  constructor() {
    this.routes = {};
    this.currentUrl = '';
  }
  route(path, callback) {
    this.routes[path] = callback || function() {};
  }
  updateView(url) {
    this.currentUrl = url;
    this.routes[this.currentUrl] && this.routes[this.currentUrl]();
  }
  bindLink() {
    const allLink = document.querySelectorAll('a[data-href]');
    for (let i = 0, len = allLink.length; i < len; i++) {
      const current = allLink[i];
      current.addEventListener(
        'click',
        e => {
          e.preventDefault();
          const url = current.getAttribute('data-href');
          history.pushState({}, null, url);
          this.updateView(url);
        },
        false
      );
    }
  }
  init() {
    this.bindLink();
    window.addEventListener('popstate', e => {
      this.updateView(window.location.pathname);
    });
    window.addEventListener('load', () => this.updateView('/'), false);
  }
}
Router 跟之前 Hash 路由很像，不同的地方在于 init 初始化函数，首先需要获取所有特殊的链接标签，然后监听点击事件，并阻止其默认事件，触发 history.pushState 以及更新相应的视图。

另外绑定 popstate 事件，当用户点击前进或者后退的按钮时候，能够及时更新视图，另外当刚进去页面时也要触发一次视图更新。

修改相应的 html 内容

<div id="app">
  <ul>
    <li><a data-href="/" href="#">home</a></li>
    <li><a data-href="/about" href="#">about</a></li>
    <li><a data-href="/topics" href="#">topics</a></li>
  </ul>
  <div id="content"></div>
</div>
<script src="js/router.js"></script>
<script>
  const router = new Router();
  router.init();
  router.route('/', function() {
    document.getElementById('content').innerHTML = 'Home';
  });
  router.route('/about', function() {
    document.getElementById('content').innerHTML = 'About';
  });
  router.route('/topics', function() {
    document.getElementById('content').innerHTML = 'Topics';
  });
</script>
跟之前的 html 基本一致，区别在于用 data-href 来表示要实现软路由的链接标签。

当然上面还有情况 3，就是你在 JS 直接触发 pushState 函数，那么这时候你必须要调用视图更新函数，否则就是出现视图内容和 url 不一致的情况。

setTimeout(() => {
  history.pushState({}, null, '/about');
  router.updateView('/about');
}, 2000);
读者可以尝试并查看效果：

yarn history
// open http://localhost:8080

React-router 用法
React-router 的版本从 2 到 3 再到现在的 4， API 变化天翻地覆，这里我们以最新的 v4 来举例。

在 v4 版本中，提供的路由能力都是以组件的形式进行呈现，由 react-router-dom 来提供，你不需再安装 react-router，因为 react-router-dom 已经包含了这个库。下面先通过 react-router-dom 写一个简单的例子。

import { BrowserRouter as Router, Link, Route } from 'react-router-dom';
const HomeView = () => <div>Home</div>;
const AboutView = () => <div>About</div>;
const TopicsView = ({ match }) => (<div>
  <h2>Topics</h2>
  <ul>
    <li><Link to={`${match.url}/topic1`} >Topic1</Link></li>
    <li><Link to={`${match.url}/topic2`} >Topic2</Link></li>
    <li><Link to={`${match.url}/topic3`} >Topic3</Link></li>
  </ul>
  <Route path={ `${match.url}/topic1` } component={ () => <div>Topic1</div> } /> 
  <Route path={ `${match.url}/topic2` } component={ () => <div>Topic2</div> } /> 
  <Route path={ `${match.url}/topic3` } component={ () => <div>Topic3</div> } /> 
</div>)

class App extends Component {
  render() {
    return (
      <Router>
        <div>
          <ul>
            <li><Link to="/">home</Link></li>
            <li><Link to="/about">about</Link></li>
            <li><Link to="/topics">topics</Link></li>
          </ul>
          <Route exact path="/" component={HomeView} />
          <Route path="/about" component={AboutView} />
          <Route path="/topics" component={TopicsView} />
          <Route component={() => <div>Always show</div>} />
        </div>
      </Router>
    );
  }
}
这是 RR4 的标准用法，实际运行效果如下：


可以看出，当 path 匹配上路由时则显示 component，匹配不上则不显示，如果没有 path 字段则默认一直显示，而 exact 字段则表示必须要完全匹配，避免像 path='/' 匹配上 path='/about' 这样的情况。

所谓的局部刷新，其本质在于：当路由发生变化时，跟当前 url 匹配的 component 正常渲染，跟当前 url 不匹配的 component 渲染为 null，没错，就是这么简单粗暴。

有了奋斗的方向后，下面我们就围绕了如何构建 Router，Link，Route 这三个组件来实现 Hash 和 History 的前端路由系统。

基于 React 的 Hash 路由系统
有了前面的理论基础，相信实现基于 React 的 Hash 路由系统应该不是什么难事吧，首先是 Link 组件的实现：

export class Link extends Component {
  render() {
    const { to, children } = this.props;
    return <a href={`#${to}`}>{children}</a>;
  }
}
简单地返回一个 a 标签，并且在链接前面加 # 代表软路由，并不是真正意义的跳转。接着是 Route 组件的实现：

export class Route extends Component {
  componentWillMount() {
    window.addEventListener('hashchange', this.updateView, false);
  }
  componentWillUnmount() {
    window.removeEventListener('hashchange', this.updateView, false);
  }
  updateView = () => {
    this.forceUpdate();
  }
  render() {
    const { path, exact, component } = this.props;
    const match = matchPath(window.location.hash, { exact, path });
    if (!match) {
      return null;
    }
    if (component) {
      return React.createElement(component, { match });
    }
    return null;
  }
}
实现的方式也不难：

当注册 Route 组件的时候，将组件更新的回调函数添加到 hashchange 事件中，而组件卸载时候移除组件更新的回调函数避免内存泄漏。
这样当页面 hash 值变化时就会触发所有注册的回调函数的执行，使所有 Route 组件都去更新实例。
在 render 函数的实现中，有个 matchPath 函数来判断当前路径是否与该组件对应的路径匹配，如果匹配不上，则返回 null，如果匹配上了，就渲染该 Route 组件对应的 Component 组件。
至于这个 matchPath 的实现：

function matchPath(hash, options) {
  // 截断 hash 首位的 #
  hash = hash.slice(1);
  const { exact = false, path } = options;
  // 如果没有传入 path，代表始终匹配
  if (!path) {
    return {
      path: null,
      url: hash,
      isExact: true
    };
  }
  const match = new RegExp(path).exec(hash);
  if (!match) {
    // 什么都没匹配上
    return null;
  }
  const url = match[0];
  const isExact = hash === url;
  if (exact && !isExact) {
    // 匹配上了，但不是精确匹配
    return null;
  }
  return {
    path,
    url,
    isExact
  };
}
这样一个简单的 Hash 路由就已经实现了，当然，我们在初次加载页面的时候 hash 值是不带 /，所以导致无法加载 Index 组件，所以我们在 HashRouter 组件中添加一次 hash 的变化，这样就保证的首次加载页面的准确性。

export class HashRouter extends Component {
  componentDidMount() {
    window.location.hash = '/';
  }
  render() {
    return this.props.children;
  }
}
到目前为止，基于 React 的 Hash 路由系统就已经实现了，我们可以将之前 React-router 的例子拿来进行测试，别忘了将组件引入路径改成我们自己写好的目录，读者也可以自己手动进行尝试。

cd hash_router_with_react/
yarn 
yarn start 
实现效果如下：


基于 React 的 Hash 路由系统实现比较简单，而 History 版的就会相对复杂一些，下面就来实现吧。

基于 React 的 History 路由系统
根据前面的 history 路由的实现，基于 React 的路由也是十分类似。


图片出处：单页面应用路由实现原理：以 React-Router 为例

大致分为两步：

在初始化的过程中，将所有 Route 实例添加到一个 instances 数组，并且为每个组件都绑定 popstate 事件。
在三种触发路由更新的途径结束后，遍历所有 instances 中的实例，强制重新渲染，从而达到更新的目的。
代码实现如下：首先是创建 instances 数组

// 注册 component 实例
const instances = [];
const register = (component) => instances.push(component);
const unregister = (component) => instances.splice(instances.indexOf(component), 1);
Route 组件大致相同，不同在于注册和卸载钩子函数的不同：初始化时需添加到 instances 中和绑定 popstate 事件。

export class Route extends Component {
  componentWillMount() {
    window.addEventListener('popstate', this.handlePopState);
    register(this);
  }
  componentWillUnmount() {
    window.removeEventListener('popstate', this.handlePopState);
    unregister(this);
  }
  handlePopState = () => {
    this.forceUpdate();
  };
  // ...
}
Link 组件实现：需要阻止默认事件并且当点击的时候需要广播所有实例强制触发更新。

export class Link extends Component {
  handleClick = e => {
    e.preventDefault();
    const { to } = this.props;
    window.history.pushState({}, null, to);
    instances.forEach(instance => instance.forceUpdate());
  };
  render() {
    const { to, children } = this.props;
    return (
      <a href={to} onClick={this.handleClick}>
        {children}
      </a>
    );
  }
}
其它的内容跟之前的 Hash 路由差不多，读者可以自行查看：

cd history_router_with_react/
yarn 
yarn start 
实现效果：


虽然点击前进后退按钮并不会触发所有组件更新，但上面的实现效果可能也不太优雅，因为单页应用中如果存在大量 Route 组件时，每次点击 Link 链接时候都需要迫使所有 Route 组件进行重渲染。

## 从0开始实现React Router

history api
现代浏览器提供了 提供了对history栈中内容的操作的api。 重要的有 pushState, replaceState。

var stateObj = { foo: "bar" };
history.pushState(stateObj, "page 2", "bar.html");
这将使浏览器地址栏显示为 http://xxxx/bar.html，但并不会导致浏览器加载 bar.html ，甚至不会检查bar.html 是否存在。

var stateObj = { foo: "bar" };
history.replaceState(stateObj, "page 2", "bar.html");
这也将使浏览器地址显示 http://xxxx/bar.html， 也不会加载。 浏览器也不会检查bar.html 是否存在。 
pushState和replaceState的区别在于 回退的时候。 push是在history栈中加了一个记录， 而repalce是替换一个记录。 
这两个方法都接收3个参数， 分别是：

状态对象 — 状态对象state是一个JavaScript对象。
标题
URL — 该参数定义了新的历史URL记录。新URL必须与当前URL同源。
监听url改变
每当url改变的时候，视图view对应改变，就是一个基本的路由了。 通过history api可以很方便的修改url。 现在我们需要做的是监听每次url的改变，从而达到修改view的目的， 我们需要对history对象进行一些封装， 从而达到监听的目的。 代码如下：

const his = window.history
class History {
    constructor() {
        this.listeners = []
    }

    push = path => {
        his.pushState({}, "", path);
        this.notifyAll()
    }

    listen = listener => {
        this.listeners.push(listener)
        return () => {
            this.listeners = this.listeners.filter(ele => ele !== listener)
        }
    }

    notifyAll = () => {
        this.listeners.forEach(lis => {
            lis()
        })
    }
}

export default new History()
声明一个History类， 可以注册listener， 当push一个地址的时候， History类底层调用history.push 方法去修改路由， 然后通知注册的listener。 这样在路由改变的时候，我们的处理函数就可以第一时间的进行处理了。

Route
react-router v4.0 里面最重要的组件莫过于Route。 这个组件接收一个path属性， 一旦url和path匹配， 就展示这个Route指定的组件。页面上一次可以展示出多个Route组件，只要Route的path属性和url匹配。 
我们的Route 是一样的。 为了实现这个效果， 我们需要在每次url变化的时候检测新的url和path是否匹配,一旦匹配，就展示对应的组件。这就需要每个Route组件在初始化的时候在History上注册一个监听器， 从而让Route可以及时的响应url变化 
代码如下：

class Route extends Component {
    constructor(props) {
        super(props)
        this.state = {
            match: matchPath(...)
        }
        this.unlisten = history.listen(this.urlChange)
    }

    componentWillUnmount() {
        this.unlisten()
    }

    urlChange = () => {
        const pathname = location.pathname
        this.setState({
            match: matchPath(...)
        })
    }

    render() {
        const { match } = this.state
        if(!match) return

        // 具体的渲染...
    }
}
matchPath
Route的path 可以是字符串或者正则表达式。 除此之外Route还有 exact（精确匹配）， strict（结尾/）， sensitive(大小写)这3个属性。 他们共同决定了一个匹配url的正则表达式PathReg（这里的匹配规则和react-router是完全一样的）。

我们假定path， exact， strict， sensitive属性是不可改变的（实际上来说， 也的确没有修改它们的必要）。 这样话， 我们就可以在Route组件初始化的时候生成这个PathReg。

const compilePath = (pattern = '/', options) => {
    const { exact = false, strict = false, sensitive = false } = options
    const keys = []
    const re = pathToRegexp(pattern, keys, { end: exact, strict, sensitive })
    return { re, keys }
}

class Route extends Component {
    static propTypes = {
        path: PropTypes.string,
        component: PropTypes.func,
        render: PropTypes.func,
        exact: PropTypes.bool,
        strict: PropTypes.bool,
    }

    constructor(props) {
        super(props)

        this.pathReAndKeys = compilePath(props.path, {
            exact: props.exact,
            strict: props.strict,
            sensitive: props.sensitive
        })
        this.state = {
            match: matchPath(...)
        }
        this.unlisten = history.listen(this.urlChange)
    }
 }
我们在组件的constructor里面， 预先生成了pathReAndKeys。然后在每次matchPath的时候， 直接使用这个正则。 mathPath的代码如下：

const matchPath = (pathname, props, pathReAndKeys) => {
    const { path = '/', exact = false } = props
    const { re, keys } = pathReAndKeys
    const match = re.exec(pathname)

    if (!match)
        return null

    const [ url, ...values ] = match
    const isExact = pathname === url

    if (exact && !isExact)
        return null

    return {
        path, // the path pattern used to match
        url: path === '/' && url === '' ? '/' : url, // the matched portion of the URL
        isExact, // whether or not we matched exactly
        params: keys.reduce((memo, key, index) => {
            memo[key.name] = values[index]
            return memo
        }, {})
    }
}
当路由不匹配的时候， 直接返回null。 否则返回一个匹配信息的对象。 例如：

url	path	match.params
/user	/user	{}
/user/12	/user/:id	{id: '12'}
/user/12/update	/user/:id/:op	{id: 12, op: 'update'}
这里处理路径用了path-to-regexp 这个库。

渲染
匹配路径只是过程， 渲染出对应的view才是最终的目标！Route组件提供了 3个属性： component， render， children。 具体用法如下：

// one
class A extends Component {
    render() {
        return <div>A</div>
    }
}

// Route默认会把匹配的信息注入到组件A的props
<Route path='/a' component={A} /> 

// two
<Route path='/a' render={({match}) => (<div>A</div>)}

// three Route默认会把匹配的信息注入到组件A的props
<Route>
    <A>
</Route>
完整的Route 代码如下：

import React, { Component } from 'react'
import PropTypes from 'prop-types'
import { compilePath, matchPath } from './util'
import history from './history'

class Route extends Component {
    static propTypes = {
        path: PropTypes.string,
        component: PropTypes.func,
        render: PropTypes.func,
        exact: PropTypes.bool,
        strict: PropTypes.bool,
    }

    constructor(props) {
        super(props)

        this.pathReAndKeys = compilePath(props.path, {
            exact: props.exact,
            strict: props.strict,
            sensitive: props.sensitive
        })
        this.state = {
            match: matchPath(location.pathname, props, this.pathReAndKeys)
        }
        this.unlisten = history.listen(this.urlChange)
    }

    componentWillReceiveProps(nextProps) {
        const {path, exact, strict} = this.props
        if (nextProps.path !== path || nextProps.exact !== exact || nextProps.strict !== strict) {
            console.warn("you should not change path, exact, strict props")
        }
    }

    componentWillUnmount() {
        this.unlisten()
    }

    urlChange = () => {
        const pathname = location.pathname
        this.setState({
            match: matchPath(pathname, this.props, this.pathReAndKeys)
        })
    }

    render() {
        const { match } = this.state
        if(!match) return

        const { children, component, render } = this.props

        if (component) {
            const Comp = component
            return <Comp match={match}/>
        }
        if (render) {
            return render({ match })
        }

        return React.cloneElement(React.Children.only(children), { match })
    }
}

export default Route
Link
与react-router相同。 我们同样提供一个Link组件， 来实现 “声明式”的路由跳转。 Link本质上来说就是一个原生的a标签， 然后在点击的时候history.push到to属性所指定的地址去。


import React, { Component } from 'react'
import history from './history'

export default class Link extends Component {

    handleClick = e => {
        const { onClick, to } = this.props
        if (onClick){
            onClick(e)
        }

        e.preventDefault()
        history.push(to)
    }

    render() {
        return (
            <a  {...this.props} onClick={this.handleClick}/>
        )

    }
}

## Resource
[单页面应用路由实现原理：以 React-Router 为例](https://github.com/youngwind/blog/issues/109)

[从0开始实现 react-router](https://juejin.im/entry/5a4d9cc4518825146b10f253)

[剖析单页面应用路由实现原理剖析单页面应用路由实现原理](https://juejin.im/entry/5a2cbd51f265da430b7b2b82)