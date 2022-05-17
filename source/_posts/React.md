---
title: React
date: 2021-01-31 21:09:39
---

#### 组件生命周期

一个组件类由extends Component 创建，并且提供一个render方法以及其他可选的生命周期函数、组件相关事件定义

<pre>getInitialState</pre>
初始化 this.state的值，只在组件装载之前调用一次。
Es6： 构造函数中 (construcor)进行初始化

<pre>getDefaultProps</pre>
组件创建时调用一次并缓存返回的对象（在初始化之前就调用）
Es6：直接定义defaultProps这个类属性来替代，
         Counter.defaultProps = { initialCount: 0 };

<pre>Render</pre>
组装生成这个组件的HTML结构，也可以返回null或false



#### 生命周期函数

##### 装载组件触发

<pre>componentWillMount </pre>
在装载之前调用一次，在render之前调用，可以再这里调用setState改变状态，并且不会导致额外调用一次 render

<pre>componentDidMount</pre>
在装载完成以后调用一次，在render之后调用，通过ReactDOM.findDOMNode(this)获取组件DOM节点

##### 更新组件触发

这些不会在首次render调用
<pre>componentWillReceiveProps
shouldComponentUpdate
componentWillUpdate
componentDidUpdate</pre>

##### 卸载组件触发
<pre>componentWillUnmount</pre>

#### 事件处理

#####  绑定事件
<pre>this.handleClick.bind(this)</pre>
要显式调用bind(this)将事件函数上下文绑定在组件实例上，尽量使用显式容易理解的js代码

##### 合成事件 
消除了IE与W3C标准之前的兼容问题
合成事件已时间委托方式绑定在组件最上层 ，并且在组件卸载时候自动销毁绑定事件。

##### 原生事件
比如在componentDidMount方法里面通过addEventListener绑定的事件就是浏览器原生事件

##### 参数传递
<pre>this.handleClick.bind>this.handleClick.bind ( this, 'extra param')</pre>


##### DOM操作
ReactDOM.render组件返回组件实例（无状态的返回null）

<pre>findDOMNode()
import { findDOMNode } from 'react-dom';
// Inside Component class
componentDidMound() {
  const el = findDOMNode(this);
}
//findDOMNode() 不能用在无状态组件上。</pre>

##### Refs
通过引用DOM元素上面设置一个ref属性指定一个名称，然后通过this.refs.name来访问对应的Dom元素

如果ref设置在原生HTML元素上，拿到的就是DOM元素，如果设置在自定义组件上，拿到就是组件实例，通过findDOMNode来拿到组件的DOM元素。

##### 小结Ref：
1.  可以使用ref到的组件定义的任何公共方法，比如 this.refs.myTypehead.reset()
2.  Refs是访问到组件内部DOM节点唯一可靠的方法
3.  Refs会自动销毁对子组件的引用（当子组件删除时）
4.  不要在render或者render之前访问refs
5.  不要滥用 refs 


##### This.props.children
组件标签里面包含的子元素会通过props.children传递进来
一般可以直接将这个属性作为父组件的子元素render
<pre>Const Parent=(props)=><div>{props.children}</div></pre>

props.children通常是一个组件对象的数组，但是当只有一个元素的时候，props.children将是这个唯一的子元素，而不是数组了。

##### 组件间通信
父子组件间通信

通过props属性传递，在父组件给子组件设置props，然后子组件通过props访问到父组件的数据/方法，搭建了父子组件间通信的桥梁。

##### Flux

**React：MVC中V
Flux：M和C
Flux(四部分)           Action -> Dispatcher -> Store -> View**


1.  The dispatcher 处理动作分发，维护store之间的依赖关系
2.  The stores 数据和逻辑部分
3.  The views React组件，这一层可以看做controller-views,作为视图同时响应用户交互
4.  The actions 提供给dispatcher传递数据给store

##### 单项数据流


流程如下：
首先要有 action，通过定义一些 action creator 方法根据需要创建 Action 提供给 dispatcher
View 层通过用户交互（比如 onClick）会触发 Action
Dispatcher 会分发触发的 Action 给所有注册的 Store 的回调函数
Store 回调函数根据接收的 Action 更新自身数据之后会触发一个 change 事件通知 View 数据更改了
View 会监听这个 change 事件，拿到对应的新数据并调用 setState 更新组件 UI
所有的状态都由 Store 来维护，通过 Action 传递数据，构成了如上所述的单向数据流循环。


# Redux

## 三个基本原则
1.  整个应用只有唯一一个可信数据源，也就是只有一个 Store
2.  State 只能通过触发 Action 来更改
3.  State 的更改必须写成纯函数，也就是每次更改总是返回一个新的 State，在 Redux 里这种函数称为 Reducer

### Actions

actions包含{type,payload}的对象，type是一个常量用来标识动作类型，payload是这个动作携带的数据。Action需要通过store.dispatch()方法来发送。

一般来说，会使用函数（Action Creators）来生成 action，这样会有更大的灵活性，Action Creators 是一个 pure function，它最后会返回一个 action 对象：

function addTodo(text) {
  return {
    type: 'ADD_TODO',
    text
  }
}
所以现在要触发一个动作只要调用 dispatch: dispatch(addTodo(text))

### Reducers 

Reducers用来处理Action触发的对状态树的更改。

一个reducers函数会接受oldState和action两个参数，返回一个新的state:(oldState,action)=>newState。

Redux提供一个工具函数combineReducers简化reducer合并

import { combineReducers } from 'redux';

const someApp = combineReducers({
  a: reducerA,
  b: reducerB
});
如果reducer函数名字和字段名字相同，利用es6的destructuring可以简化成 combineReducers({a,b})

### Store
Store负责连接Action和Reducer。
作用：
	1. Hold 住整个应用的 State 状态树
	2. 提供一个 getState() 方法获取 State
	3. 提供一个 dispatch() 方法发送 action 更改 State
	4. 提供一个 subscribe() 方法注册回调函数监听 State 的更改

创建一个 Store 很容易，将 root reducer 函数传递给 createStore 方法即可：

import { createStore } from 'redux';
import someApp from './reducers';
let store = createStore(someApp);

// 你也可以额外指定一个初始 State（initialState），这对于服务端渲染很有用
// let store = createStore(someApp, window.STATE_FROM_SERVER);


拿到了store.dispatch,用来分发action
store.dispatch({ type: 'CHANGE_A' });
store.dispatch({ type: 'CHANGE_B', payload: 'Modified b' });

### 在 React 应用中使用 Redux

和 Flux 类似，Redux 也是需要注册一个回调函数 store.subscribe(listener) 来获取 State 的更新，然后我们要在 listener 里面调用 setState() 来更新 React 组件。

<Provider>
<Provider> 作为一个容器组件，用来接受 Store，并且让 Store 对子组件可用，用法如下：

import { render } from 'react-dom';
import { Provider } from 'react-redux';
import App from './app';

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
这时候 <Provider> 里面的子组件 <App /> 才可以使用 connect 方法关联 store。

### Connect

connect() 这个方法略微复杂一点，主要是因为它的用法非常灵活：connect([mapStateToProps], mapDispatchToProps], [mergeProps], [options])，它最多接受4个参数，都是可选的，并且这个方法调用会返回另一个函数，这个返回的函数来接受一个组件类作为参数，最后才返回一个和 Redux store 关联起来的新组件，类似这样：

class App extends Component { ... }
export default connect()(App);
这样就可以在 App 这个组件里面通过 props 拿到 Store 的 dispatch 方法，但是注意现在的 App 没有监听 Store 的状态更改，如果要监听 Store 的状态更改，必须要指定 mapStateToProps 参数。

参数 
	• [mapStateToProps(state, [ownProps]): stateProps]: 
	
	第一个可选参数是一个函数，只有指定了这个参数，这个关联（connected）组件才会监听 Redux Store 的更新，每次更新都会调用 mapStateToProps 这个函数，返回一个字面量对象将会合并到组件的 props 属性。 ownProps 是可选的第二个参数，它是传递给组件的 props，当组件获取到新的 props 时，ownProps 都会拿到这个值并且执行 mapStateToProps 这个函数。
	
	• [mapDispatchProps(dispatch, [ownProps]): dispatchProps]:
	
	 这个函数用来指定如何传递 dispatch 给组件，在这个函数里面直接 dispatch action creator，返回一个字面量对象将会合并到组件的 props 属性，这样关联组件可以直接通过 props 调用到 action， Redux 提供了一个 bindActionCreators() 辅助函数来简化这种写法。 如果省略这个参数，默认直接把 dispatch 作为 props 传入。ownProps 作用同上。
	
	
	
表单

表单元素有这么几种属于状态的属性：

	· value，对应 <input> 和 <textarea> 所有
	· checked，对应类型为 checkbox 和 radio 的 <input> 所有
	· selected，对应 <option> 所有
	
受控组件
	
用onchange事件改变状态属性

非受控组件

如果你想要给“状态属性”设置默认值，就要用 React 提供的特殊属性 defaultValue，对于 checked 会有 defaultChecked，<option> 也是使用 defaultValue。


<select>

在 HTML 中 <select> 标签指定选中项都是通过对应 <option> 的 selected 属性来做的，但是在 React 修改成统一使用 value。


### 总结

#### 关于 Store：

1. 整个应用只有一个唯一的 Store
2.  Store 对应的状态树（State），由调用一个 reducer 函数（root reducer）生成
3.  状态树上的每个字段都可以进一步由不同的 reducer 函数生成
4.  Store 包含了几个方法比如 dispatch, getState 来处理数据流
5.  Store 的状态树只能由 dispatch(action) 来触发更改

#### Redux 的数据流：

1. action 是一个包含 { type, payload } 的对象
2.  reducer 函数通过 store.dispatch(action) 触发
3.  reducer 函数接受 (state, action) 两个参数，返回一个新的 state
4. reducer 函数判断 action.type 然后处理对应的 action.payload 数据来更新状态树
5.  所以对于整个应用来说，一个 Store 就对应一个 UI 快照，服务器端渲染就简化成了在服务器端初始化 Store，将 Store 传入应用的根组件，针对根组件调用 renderToString 就将整个应用输出成包含了初始化数据的 HTML。 

#### React-router

react-router 通过一种声明式的方式匹配不同路由决定在页面上展示不同的组件，并且通过 props 将路由信息传递给组件使用，所以只要路由变更，props 就会变化，触发组件 re-render。


#### Reducer

Store 是由 reducer 产生的，所以 reducer 实际上反映了 Store 的状态树结构。

./reducers/index.js

<pre>import listReducer from './list';
import itemReducer from './item';

export default function rootReducer(state = {}, action) {
  return {
    list: listReducer(state.list, action),
    item: itemReducer(state.item, action)
  };
  }</pre>
rootReducer 的 state 参数就是整个 Store 的状态树，状态树下的每个字段对应也可以有自己的 reducer，所以这里引入了 listReducer 和 itemReducer，可以看到这两个 reducer 的 state 参数就只是整个状态树上对应的 list 和 item 字段。

具体到 ./reducers/list.js

<pre>const initialState = [];

export default function listReducer(state = initialState, action) {
  switch(action.type) {
  case 'FETCH_LIST_SUCCESS': return [...action.payload];
  default: return state;
  }
  }</pre>
list 就是一个包含 items 的简单数组，可能类似这种结构：[{ id: 0, name: 'first item'}, {id: 1, name: 'second item'}]，从 'FETCH_LIST_SUCCESS' 的 action.payload 获得。

然后是 ./reducers/item.js，处理获取到的 item 数据

<pre>const initialState = {};

export default function listReducer(state = initialState, action) {
  switch(action.type) {
  case 'FETCH_ITEM_SUCCESS': return [...action.payload];
  default: return state;
  }
  }</pre>


#### Action
对应的应该要有两个 action 来获取 list 和 item，触发 reducer 更改 Store，这里我们定义 fetchList 和 fetchItem 两个 action。

./actions/index.js

<pre>import fetch from 'isomorphic-fetch';

export function fetchList() {
  return (dispatch) => {
    return fetch('/api/list')
        .then(res => res.json())
        .then(json => dispatch({ type: 'FETCH_LIST_SUCCESS', payload: json }));
  }
}

export function fetchItem(id) {
  return (dispatch) => {
    if (!id) return Promise.resolve();
    return fetch(`/api/item/${id}`)
        .then(res => res.json())
        .then(json => dispatch({ type: 'FETCH_ITEM_SUCCESS', payload: json }));
  }
  }</pre>
isomorphic-fetch 是一个前后端通用的 Ajax 实现，前后端要共享代码这点很重要。

另外因为涉及到异步请求，这里的 action 用到了 thunk，也就是函数，redux 通过 thunk-middleware 来处理这类 action，把函数当作普通的 action dispatch 就好了，比如 dispatch(fetchList())


#### Store
我们用一个独立的 ./store.js，配置（比如 Apply Middleware）生成 Store

<pre>import { createStore } from 'redux';
import rootReducer from './reducers';

// Apply middleware here
// ...

export default function configureStore(initialState) {
  const store = createStore(rootReducer, initialState);
  return store;
  }</pre>



#### react-redux
实现 List ，Item 组件，然后把 Redux 和 React 组件关联起来
./app.js

<pre>
import React from 'react';
import { render } from 'react-dom';
import { Router } from 'react-router';
import createBrowserHistory from 'history/lib/createBrowserHistory';
import { Provider } from 'react-redux';
import routes from './routes';
import configureStore from './store';

// __INITIAL_STATE__来自服务器端渲染
</pre>
<code>
const initialState = window.__INITIAL_STATE__;
const store = configureStore(initialState);
const Root = (props) => {
  return (
 <pre>     <Provider store={store}></pre>
   <pre>     <Router history={createBrowserHistory()}></pre>
   <pre>       {routes}</pre>
   <pre>     </Router></pre>
   <pre>   </Provider></pre>
  );
}

render(<Root />, document.getElementById('root'));</code>

##### 到此 客户端部分结束


#### Server Rendering
获取数据调用action,routes在服务端处理参考 react-router server rendering ，在服务器端用一个match方法将拿到的request url 匹配到我们之前定义的routes，解析成和客户端一致的props对象传递给组件。

服务器端渲染部分可以直接通过共用客户端 store.dispatch(action) 来统一获取 Store 数据。另外注意 renderFullPage 生成的页面 HTML 在 React 组件 mount 的部分(<div id="root">)，前后端的 HTML 结构应该是一致的。然后要把 store 的状态树写入一个全局变量（__INITIAL_STATE__），这样客户端初始化 render 的时候能够校验服务器生成的 HTML 结构，并且同步到初始化状态，然后整个页面被客户端接管。


页面内链接跳转处理
react-router 提供了一个 <Link> 组件用来替代 <a> 标签，它负责管理浏览器 history，从而不是每次点击链接都去请求服务器，然后可以通过绑定 onClick 事件来作其他处理。

比如在 /list 页面，对于每一个 item 都会用 <Link> 绑定一个 route url：/item/:id，并且绑定 onClick 去触发 dispatch(fetchItem(id)) 获取数据，显示详情页内容。
