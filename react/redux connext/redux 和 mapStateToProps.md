function createStore (state, stateChanger) {
  const listeners = []
  const subscribe = (listener) => listeners.push(listener)
  const getState = () => state
  const dispatch = (action) => {
    state = stateChanger(state, action) // 覆盖原对象
    listeners.forEach((listener) => listener())
  }
  return { getState, dispatch, subscribe }
}
它的使用方式是：

let appState = {
  title: {
    text: 'React.js 小书',
    color: 'red',
  },
  content: {
    text: 'React.js 小书内容',
    color: 'blue'
  }
}

function stateChanger (state, action) {
  switch (action.type) {
    case 'UPDATE_TITLE_TEXT':
      return {
        ...state,
        title: {
          ...state.title,
          text: action.text
        }
      }
    case 'UPDATE_TITLE_COLOR':
      return {
        ...state,
        title: {
          ...state.title,
          color: action.color
        }
      }
    default:
      return state
  }
}

const store = createStore(appState, stateChanger)
...
我们再优化一下，其实 appState 和 stateChanger 可以合并到一起去：

function stateChanger (state, action) {
  if (!state) {
    return {
      title: {
        text: 'React.js 小书',
        color: 'red',
      },
      content: {
        text: 'React.js 小书内容',
        color: 'blue'
      }
    }
  }
  switch (action.type) {
    case 'UPDATE_TITLE_TEXT':
      return {
        ...state,
        title: {
          ...state.title,
          text: action.text
        }
      }
    case 'UPDATE_TITLE_COLOR':
      return {
        ...state,
        title: {
          ...state.title,
          color: action.color
        }
      }
    default:
      return state
  }
}
stateChanger 现在既充当了获取初始化数据的功能，也充当了生成更新数据的功能。如果有传入 state 就生成更新数据，否则就是初始化数据。这样我们可以优化 createStore 成一个参数，因为 state 和 stateChanger 合并到一起了：

function createStore (stateChanger) {
  let state = null
  const listeners = []
  const subscribe = (listener) => listeners.push(listener)
  const getState = () => state
  const dispatch = (action) => {
    state = stateChanger(state, action)
    listeners.forEach((listener) => listener())
  }
  dispatch({}) // 初始化 state
  return { getState, dispatch, subscribe }
}
createStore 内部的 state 不再通过参数传入，而是一个局部变量 let state = null。createStore 的最后会手动调用一次 dispatch({})，dispatch 内部会调用 stateChanger，这时候的 state 是 null，所以这次的 dispatch 其实就是初始化数据了。createStore 内部第一次的 dispatch 导致 state 初始化完成，后续外部的 dispatch 就是修改数据的行为了。

我们给 stateChanger 这个玩意起一个通用的名字：reducer，不要问为什么，它就是个名字而已，修改 createStore 的参数名字：

function createStore (reducer) {
  let state = null
  const listeners = []
  const subscribe = (listener) => listeners.push(listener)
  const getState = () => state
  const dispatch = (action) => {
    state = reducer(state, action)
    listeners.forEach((listener) => listener())
  }
  dispatch({}) // 初始化 state
  return { getState, dispatch, subscribe }
}
这是一个最终形态的 createStore，它接受的参数叫 reducer，reducer 是一个函数，细心的朋友会发现，它其实是一个纯函数（Pure Function）。

reducer
createStore 接受一个叫 reducer 的函数作为参数，这个函数规定是一个纯函数，它接受两个参数，一个是 state，一个是 action。

如果没有传入 state 或者 state 是 null，那么它就会返回一个初始化的数据。如果有传入 state 的话，就会根据 action 来“修改“数据，但其实它没有、也规定不能修改 state，而是要通过上节所说的把修改路径的对象都复制一遍，然后产生一个新的对象返回。如果它不能识别你的 action，它就不会产生新的数据，而是（在 default 内部）把 state 原封不动地返回。

reducer 是不允许有副作用的。你不能在里面操作 DOM，也不能发 Ajax 请求，更不能直接修改 state，它要做的仅仅是 —— 初始化和计算新的 state。

现在我们可以用这个 createStore 来构建不同的 store 了，只要给它传入符合上述的定义的 reducer 即可：

function themeReducer (state, action) {
  if (!state) return {
    themeName: 'Red Theme',
    themeColor: 'red'
  }
  switch (action.type) {
    case 'UPATE_THEME_NAME':
      return { ...state, themeName: action.themeName }
    case 'UPATE_THEME_COLOR':
      return { ...state, themeColor: action.themeColor }
    default:
      return state
  }
}

const store = createStore(themeReducer)
...

我们从一个简单的例子的代码中发现了共享的状态如果可以被任意修改的话，那么程序的行为将非常不可预料，所以我们提高了修改数据的门槛：你必须通过 dispatch 执行某些允许的修改操作，而且必须大张旗鼓的在 action 里面声明。

这种模式挺好用的，我们就把它抽象出来一个 createStore，它可以产生 store，里面包含 getState 和 dispatch 函数，方便我们使用。

后来发现每次修改数据都需要手动重新渲染非常麻烦，我们希望自动重新渲染视图。所以后来加入了订阅者模式，可以通过 store.subscribe 订阅数据修改事件，每次数据更新的时候自动重新渲染视图。

接下来我们发现了原来的“重新渲染视图”有比较严重的性能问题，我们引入了“共享结构的对象”来帮我们解决问题，这样就可以在每个渲染函数的开头进行简单的判断避免没有被修改过的数据重新渲染。

我们优化了 stateChanger 为 reducer，定义了 reducer 只能是纯函数，功能就是负责初始 state，和根据 state 和 action 计算具有共享结构的新的 state。

createStore 现在可以直接拿来用了，套路就是：

// 定一个 reducer
function reducer (state, action) {
  /* 初始化 state 和 switch case */
}

// 生成 store
const store = createStore(reducer)

// 监听数据变化重新渲染页面
store.subscribe(() => renderApp(store.getState()))

// 首次渲染页面
renderApp(store.getState()) 

// 后面可以随意 dispatch 了，页面自动更新
store.dispatch(...)
现在的代码跟 React.js 一点关系都没有，接下来我们要把 React.js 和 Redux 结合起来，用 Redux 模式帮助管理 React.js 的应用状态。

如果一个组件的渲染只依赖于外界传进去的 props 和自己的 state，而并不依赖于其他的外界的任何数据，也就是说像纯函数一样，给它什么，它就吐出（渲染）什么出来。这种组件的复用性是最强的，别人使用的时候根本不用担心任何事情，只要看看 PropTypes 它能接受什么参数，然后把参数传进去控制它就行了。

我们把这种组件叫做 Pure Component，因为它就像纯函数一样，可预测性非常强，对参数（props）以外的数据零依赖，也不产生副作用。这种组件也叫 Dumb Component，因为它们呆呆的，让它干啥就干啥。写组件的时候尽量写 Dumb Component 会提高我们的组件的可复用性。

到这里思路慢慢地变得清晰了，我们需要高阶组件帮助我们从 context 取数据，我们也需要写 Dumb 组件帮助我们提高组件的复用性。所以我们尽量多地写 Dumb 组件，然后用高阶组件把它们包装一层，高阶组件和 context 打交道，把里面数据取出来通过 props 传给 Dumb 组件。

!./redux connect mapStateToProps.png!

我们把这个高阶组件起名字叫 connect，因为它把 Dumb 组件和 context 连接（connect）起来了：

import React, { Component } from 'react'
import PropTypes from 'prop-types'

export connect = (WrappedComponent) => {
  class Connect extends Component {
    static contextTypes = {
      store: PropTypes.object
    }

    // TODO: 如何从 store 取数据？

    render () {
      return <WrappedComponent />
    }
  }

  return Connect
}
connect 函数接受一个组件 WrappedComponent 作为参数，把这个组件包含在一个新的组件 Connect 里面，Connect 会去 context 里面取出 store。现在要把 store 里面的数据取出来通过 props 传给 WrappedComponent。

但是每个传进去的组件需要 store 里面的数据都不一样的，所以除了给高阶组件传入 Dumb 组件以外，还需要告诉高级组件我们需要什么数据，高阶组件才能正确地去取数据。为了解决这个问题，我们可以给高阶组件传入类似下面这样的函数：

const mapStateToProps = (state) => {
  return {
    themeColor: state.themeColor,
    themeName: state.themeName,
    fullName: `${state.firstName} ${state.lastName}`
    ...
  }
}
这个函数会接受 store.getState() 的结果作为参数，然后返回一个对象，这个对象是根据 state 生成的。mapStateTopProps 相当于告知了 Connect 应该如何去 store 里面取数据，然后可以把这个函数的返回结果传给被包装的组件：

import React, { Component } from 'react'
import PropTypes from 'prop-types'

export const connect = (mapStateToProps) => (WrappedComponent) => {
  class Connect extends Component {
    static contextTypes = {
      store: PropTypes.object
    }

    render () {
      const { store } = this.context
      let stateProps = mapStateToProps(store.getState())
      // {...stateProps} 意思是把这个对象里面的属性全部通过 `props` 方式传递进去
      return <WrappedComponent {...stateProps} />
    }
  }

  return Connect
}

connect 现在是接受一个参数 mapStateToProps，然后返回一个函数，这个返回的函数才是高阶组件。它会接受一个组件作为参数，然后用 Connect 把组件包装以后再返回。 connect 的用法是：

...
const mapStateToProps = (state) => {
  return {
    themeColor: state.themeColor
  }
}
Header = connect(mapStateToProps)(Header)
...

有些朋友可能会问为什么不直接 const connect = (mapStateToProps, WrappedComponent)，而是要额外返回一个函数。这是因为 React-redux 就是这么设计的，而个人观点认为这是一个 React-redux 设计上的缺陷，这里有机会会在关于函数编程的章节再给大家科普，这里暂时不深究了。

我们把上面 connect 的函数代码单独分离到一个模块当中，在 src/ 目录下新建一个 react-redux.js，把上面的 connect 函数的代码复制进去，然后就可以在 src/Header.js 里面使用了：

import React, { Component } from 'react'
import PropTypes from 'prop-types'
import { connect } from './react-redux'

class Header extends Component {
  static propTypes = {
    themeColor: PropTypes.string
  }

  render () {
    return (
      <h1 style={{ color: this.props.themeColor }}>React.js 小书</h1>
    )
  }
}

const mapStateToProps = (state) => {
  return {
    themeColor: state.themeColor
  }
}
Header = connect(mapStateToProps)(Header)

export default Header
可以看到 Header 删掉了大部分关于 context 的代码，它除了 props 什么也不依赖，它是一个 Pure Component，然后通过 connect 取得数据。我们不需要知道 connect 是怎么和 context 打交道的，只要传一个 mapStateToProps 告诉它应该怎么取数据就可以了。同样的方式修改 src/Content.js：

import React, { Component } from 'react'
import PropTypes from 'prop-types'
import ThemeSwitch from './ThemeSwitch'
import { connect } from './react-redux'

class Content extends Component {
  static propTypes = {
    themeColor: PropTypes.string
  }

  render () {
    return (
      <div>
        <p style={{ color: this.props.themeColor }}>React.js 小书内容</p>
        <ThemeSwitch />
      </div>
    )
  }
}

const mapStateToProps = (state) => {
  return {
    themeColor: state.themeColor
  }
}
Content = connect(mapStateToProps)(Content)

export default Content
connect 还没有监听数据变化然后重新渲染，所以现在点击按钮只有按钮会变颜色。我们给 connect 的高阶组件增加监听数据变化重新渲染的逻辑，稍微重构一下 connect：

export const connect = (mapStateToProps) => (WrappedComponent) => {
  class Connect extends Component {
    static contextTypes = {
      store: PropTypes.object
    }

    constructor () {
      super()
      this.state = { allProps: {} }
    }

    componentWillMount () {
      const { store } = this.context
      this._updateProps()
      store.subscribe(() => this._updateProps())
    }

    _updateProps () {
      const { store } = this.context
      let stateProps = mapStateToProps(store.getState(), this.props) // 额外传入 props，让获取数据更加灵活方便
      this.setState({
        allProps: { // 整合普通的 props 和从 state 生成的 props
          ...stateProps,
          ...this.props
        }
      })
    }

    render () {
      return <WrappedComponent {...this.state.allProps} />
    }
  }

  return Connect
}
我们在 Connect 组件的 constructor 里面初始化了 state.allProps，它是一个对象，用来保存需要传给被包装组件的所有的参数。生命周期 componentWillMount 会调用调用 _updateProps 进行初始化，然后通过 store.subscribe 监听数据变化重新调用 _updateProps。

为了让 connect 返回新组件和被包装的组件使用参数保持一致，我们会把所有传给 Connect 的 props 原封不动地传给 WrappedComponent。所以在 _updateProps 里面会把 stateProps 和 this.props 合并到 this.state.allProps 里面，再通过 render 方法把所有参数都传给 WrappedComponent。

mapStateToProps 也发生点变化，它现在可以接受两个参数了，我们会把传给 Connect 组件的 props 参数也传给它，那么它生成的对象配置性就更强了，我们可以根据 store 里面的 state 和外界传入的 props 生成我们想传给被包装组件的参数。

现在已经很不错了，Header.js 和 Content.js 的代码都大大减少了，并且这两个组件 connect 之前都是 Dumb 

在重构 ThemeSwitch 的时候我们发现，ThemeSwitch 除了需要 store 里面的数据以外，还需要 store 来 dispatch：

...
  // dispatch action 去改变颜色
  handleSwitchColor (color) {
    const { store } = this.context
    store.dispatch({
      type: 'CHANGE_COLOR',
      themeColor: color
    })
  }
...
目前版本的 connect 是达不到这个效果的，我们需要改进它。

想一下，既然可以通过给 connect 函数传入 mapStateToProps 来告诉它如何获取、整合状态，我们也可以想到，可以给它传入另外一个参数来告诉它我们的组件需要如何触发 dispatch。我们把这个参数叫 mapDispatchToProps：

const mapDispatchToProps = (dispatch) => {
  return {
    onSwitchColor: (color) => {
      dispatch({ type: 'CHANGE_COLOR', themeColor: color })
    }
  }
}
和 mapStateToProps 一样，它返回一个对象，这个对象内容会同样被 connect 当作是 props 参数传给被包装的组件。不一样的是，这个函数不是接受 state 作为参数，而是 dispatch，你可以在返回的对象内部定义一些函数，这些函数会用到 dispatch 来触发特定的 action。

调整 connect 让它能接受这样的 mapDispatchToProps：

export const connect = (mapStateToProps, mapDispatchToProps) => (WrappedComponent) => {
  class Connect extends Component {
    static contextTypes = {
      store: PropTypes.object
    }

    constructor () {
      super()
      this.state = {
        allProps: {}
      }
    }

    componentWillMount () {
      const { store } = this.context
      this._updateProps()
      store.subscribe(() => this._updateProps())
    }

    _updateProps () {
      const { store } = this.context
      let stateProps = mapStateToProps
        ? mapStateToProps(store.getState(), this.props)
        : {} // 防止 mapStateToProps 没有传入
      let dispatchProps = mapDispatchToProps
        ? mapDispatchToProps(store.dispatch, this.props)
        : {} // 防止 mapDispatchToProps 没有传入
      this.setState({
        allProps: {
          ...stateProps,
          ...dispatchProps,
          ...this.props
        }
      })
    }

    render () {
      return <WrappedComponent {...this.state.allProps} />
    }
  }
  return Connect
}
在 _updateProps 内部，我们把store.dispatch 作为参数传给 mapDispatchToProps ，它会返回一个对象 dispatchProps。接着把 stateProps、dispatchProps、this.props 三者合并到 this.state.allProps 里面去，这三者的内容都会在 render 函数内全部传给被包装的组件。

另外，我们稍微调整了一下，在调用 mapStateToProps 和 mapDispatchToProps 之前做判断，让这两个参数都是可以缺省的，这样即使不传这两个参数程序也不会报错。

这时候我们就可以重构 ThemeSwitch，让它摆脱 store.dispatch：

import React, { Component } from 'react'
import PropTypes from 'prop-types'
import { connect } from './react-redux'

class ThemeSwitch extends Component {
  static propTypes = {
    themeColor: PropTypes.string,
    onSwitchColor: PropTypes.func
  }

  handleSwitchColor (color) {
    if (this.props.onSwitchColor) {
      this.props.onSwitchColor(color)
    }
  }

  render () {
    return (
      <div>
        <button
          style={{ color: this.props.themeColor }}
          onClick={this.handleSwitchColor.bind(this, 'red')}>Red</button>
        <button
          style={{ color: this.props.themeColor }}
          onClick={this.handleSwitchColor.bind(this, 'blue')}>Blue</button>
      </div>
    )
  }
}

const mapStateToProps = (state) => {
  return {
    themeColor: state.themeColor
  }
}
const mapDispatchToProps = (dispatch) => {
  return {
    onSwitchColor: (color) => {
      dispatch({ type: 'CHANGE_COLOR', themeColor: color })
    }
  }
}
ThemeSwitch = connect(mapStateToProps, mapDispatchToProps)(ThemeSwitch)

export default ThemeSwitch
光看 ThemeSwitch 内部，是非常清爽干净的，只依赖外界传进来的 themeColor 和 onSwitchColor。但是 ThemeSwitch 内部并不知道这两个参数其实都是我们去 store 里面取的，它是 Dumb 的。这时候这三个组件的重构都已经完成了，代码大大减少、不依赖 context，并且功能和原来一样。



我们要把 context 相关的代码从所有业务组件中清除出去，现在的代码里面还有一个地方是被污染的。那就是 src/index.js 里面的 Index：

...
class Index extends Component {
  static childContextTypes = {
    store: PropTypes.object
  }

  getChildContext () {
    return { store }
  }

  render () {
    return (
      <div>
        <Header />
        <Content />
      </div>
    )
  }
}
...
其实它要用 context 就是因为要把 store 存放到里面，好让子组件 connect 的时候能够取到 store。我们可以额外构建一个组件来做这种脏活，然后让这个组件成为组件树的根节点，那么它的子组件都可以获取到 context 了。

我们把这个组件叫 Provider，因为它提供（provide）了 store

在 src/react-redux.js 新增代码：

export class Provider extends Component {
  static propTypes = {
    store: PropTypes.object,
    children: PropTypes.any
  }

  static childContextTypes = {
    store: PropTypes.object
  }

  getChildContext () {
    return {
      store: this.props.store
    }
  }

  render () {
    return (
      <div>{this.props.children}</div>
    )
  }
}
Provider 做的事情也很简单，它就是一个容器组件，会把嵌套的内容原封不动作为自己的子组件渲染出来。它还会把外界传给它的 props.store 放到 context，这样子组件 connect 的时候都可以获取到。

可以用它来重构我们的 src/index.js：

...
// 头部引入 Provider
import { Provider } from './react-redux'
...

// 删除 Index 里面所有关于 context 的代码
class Index extends Component {
  render () {
    return (
      <div>
        <Header />
        <Content />
      </div>
    )
  }
}

// 把 Provider 作为组件树的根节点
ReactDOM.render(
  <Provider store={store}>
    <Index />
  </Provider>,
  document.getElementById('root')
)
这样我们就把所有关于 context 的代码从组件里面删除了。

回顾一下：

React.js 除了状态提升以外并没有更好的办法帮我们解决组件之间共享状态的问题，而使用 context 全局变量让程序不可预测。通过 Redux 的章节，我们知道 store 里面的内容是不可以随意修改的，而是通过 dispatch 才能变更里面的 state。所以我们尝试把 store 和 context 结合起来使用，可以兼顾组件之间共享状态问题和共享状态可能被任意修改的问题。

第一个版本的 store 和 context 结合有诸多缺陷，有大量的重复逻辑和对 context 的依赖性过强。我们尝试通过构建一个高阶组件 connect 函数的方式，把所有的重复逻辑和对 context 的依赖放在里面 connect 函数里面，而其他组件保持 Pure（Dumb） 的状态，让 connect 跟 context 打交道，然后通过 props 把参数传给普通的组件。

而每个组件需要的数据和需要触发的 action 都不一样，所以调整 connect，让它可以接受两个参数 mapStateToProps 和 mapDispatchToProps，分别用于告诉 connect 这个组件需要什么数据和需要触发什么 action。

最后为了把所有关于 context 的代码完全从我们业务逻辑里面清除掉，我们构建了一个 Provider 组件。Provider 作为所有组件树的根节点，外界可以通过 props 给它提供 store，它会把 store 放到自己的 context 里面，好让子组件 connect 的时候都能够获取到。

这几节的成果就是 react-redux.js 这个文件里面的两个内容：connect 函数和 Provider 容器组件。这就是 React-redux 的基本内容，当然它是一个残疾版本的 React-redux，很多地方需要完善

