### 一、什么是Redux的中间件？

所谓中间件，一定是处于两个事物的中间，那么什么是Redux中间件呢？

回顾一下Redux的工作流程:



![img](https://user-gold-cdn.xitu.io/2019/6/19/16b6f71e151db1c4?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

 Redux的中间件，处于Action和Reducer之间，将中间某个过程拦截一下，进行一些处理再继续正常执行，这就是中间件的功能。



那为什么要用到Redux中间件呢？

对于异步请求的代码，我们最好将它们放到Redux中间件里面。

当项目复杂到一定规模的时候，我们希望让各个模块尽可能的实行单一职责，比如React作为一个视图层的框架就只负责渲染，数据的事情统统交给Redux来处理，此时，相对于把异步请求的Ajax代码放到写到组件的componentDidMount生命周期钩子函数，其实交给Redux是一种更好的选择。另外，交给Redux,一方面能够做到不同组件的接口复用，另一方面方便于测试，单纯去测试一些异步代码比测试一个React组件的生命周期函数会更加容易。 然而Redux里面dispatch的过程中只允许传递对象，而不允许传递函数。于是，redux-thunk便应运而生。

### 二、Redux-thunk实现异步更新state

```
npm install redux-thunk --save
复制代码

```

首先在项目中启用redux-thunk

```
//src/store/index.js
import {createStore, applyMiddleware, compose} from 'redux';
import reducer from './reducer';
import thunk from 'redux-thunk'
import TodoSagas from './sagas'

//需要同时激活redux-devtools的chrome插件，下面是激活代码
//兼容代码在redux-devtools的文档下拿过来照着用即可
const composeEnhancers =
  typeof window === 'object' &&
  window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?   
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;

const enhancer = composeEnhancers(applyMiddleware(thunk))
//创建store
const store = createStore(
  reducer,
  enhancer
)

export default store;
复制代码
//src/store/actionCreators.js
//以下为getTodoList部分
export const getTodoList = () => {
  return (dispatch) => {
    axios.get('xxx').then((res) => {
      const data = res.data.list
      //创建action
      const action = initListAction(data)
      //相当于store.dispatch
      //其实redux-thunk给当前返回函数中添加的第一个默认参数就是store的dispatch方法
      dispatch(action)
    })
  }
}
复制代码
  //App.js
  componentDidMount() {
    // axios.get('xxx').then((res) => {
    //   const data = res.data.data
    //   const action = initListAction(data)
    //   store.dispatch(action)
    // })
    //将以上代码放进Redux，效果相同
    const action = getTodoList()
    store.dispatch(action)
  }
复制代码

```

因此，redux-thunk其实是拦截了store的dispatch方法，Redux中store.dispatch原本是不能传一个函数进去的，但是redux-thunk让dispatch拥有的接受函数参数的能力。具体来说，如果dispatch方法中传递的是一个对象，那么直接按照正常的Redux工作流来运行，但如果是一个函数，那么直接执行它，并把store.dispatch这个方法当作第一个参数传进这个函数。

### 三、Redux-saga实现异步更新state

redux-saga也是Redux中处理异步函数的中间件。

```
npm install redux-saga --save
复制代码

```

首先在项目中启动redux-saga:

```
//src/store/index.js
import {createStore, applyMiddleware, compose} from 'redux';
import reducer from './reducer';
import createSagaMiddleware from 'redux-saga'
import TodoSagas from './sagas'
//创建中间件
const sagaMiddleware = createSagaMiddleware()
//需要同时激活redux-devtools的chrome插件，下面是激活代码
const composeEnhancers =
  typeof window === 'object' &&
  window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?   
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;

const enhancer = composeEnhancers(applyMiddleware(sagaMiddleware))
//创建store
const store = createStore(
  reducer,
  enhancer
)
//启动中间件
sagaMiddleware.run(TodoSagas)

export default store;
复制代码
//sagas.js
import { takeEvery, put } from 'redux-saga/effects'
import { GET_INIT_LIST } from './actionTypes';
import { initListAction } from './actionCreators';
import axios from 'axios'
function* getInitList() {
  try {
    const res = yield axios.get('xxx')
    //拿到Ajax数据后，通过initListAction创建action对象
    //注意: 这是另一个action:INIT_LIST_ACTION，直接改变state中的list的值
    const action = initListAction(res.data.list)
    //put相当于dispatch这个新的action
    yield put(action)
  }catch(e) {
    console.log('网络请求失败')
  }
}
//导出的mySaga需要写成一个Generator函数，异步处理函数getInitList也应是Generator函数
function* mySaga() {
  //拦截GET_INIT_LIST这个action
  yield takeEvery(GET_INIT_LIST, getInitList);
}

export default mySaga
复制代码
  //App.js
  componentDidMount() {
    const action = getInitList()
    store.dispatch(action)
  }
复制代码

```

所以你能够看到，在redux-saga当中的处理思路是: 先抛出一个类似于信号灯的action，redux-saga看到了这个信号灯，拦截下来，然后执行响应的异步函数，在这个异步函数拿到数据后，执行真正要更新state的action。在这个过程中，作为信号灯的action在reducer中并没有具体关于state的逻辑编写，而仅仅是给redux-saga发一个信号而已。 

