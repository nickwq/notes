# Action
## Action的定义
* Action是把数据从应用传到store的有效载荷。
* 它是store数据的**唯一**来源。
* 一般通过store.dispatch()将action传到store。
* Action本质上是一个普通的JavaScript对象。例如：
<pre><code>
{
  type: ADD_TODO,
  text: 'Build my first Redux app'
}
</code></pre>
* 通常约定action必须含有一个字符串类型的type字段表示将要执行的动作。

## 创建一个Action
传统Flux是将Action的创建和dispacth写在一起。而redux同过action创建函数实现了action创建和分发的分离。
* 创建一个action
<pre><code>
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
</code></pre>
* dispacth一个Action
<pre><code>
dispatch(addTodo(text))
dispatch(completeTodo(index))
</code></pre>
* 或者创建一个**被绑定的action创建函数**自动dispacth
<pre><code>
const boundAddTodo = (text) => dispatch(addTodo(text))
const boundCompleteTodo = (index) => dispatch(completeTodo(index))
</code></pre>

# Reducer
Action用于描述事件的发生，reducer则是指明了如何更新state。
它就是一个纯函数，接受旧的state和action， 返回新的state。
<pre><code>
(previousState, action) => newState
</code></pre>

**永远不要**在reducer中做如下操作：
* 修改传入参数
* 执行有副作用的操作， 如API请求或路由
* 调用非纯函数， 如<pre>Date.now()</pre>
只要传入参数相同，返回计算得到的下一个state就一定相同。没有副作用，没有API请求，没有变量修改，单纯执行计算。

## todo的reducer例子
<pre><code>
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    default:
      return state
  }
}
</code></pre>
1. 不要修改state， 使用Object.assign()新建一个副本
1. 在default的情况下返回旧的state。


##  reducer的组织和层次
<pre><code>
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      })
    case TOGGLE_TODO:
      return Object.assign({}, state, {
        todos: state.todos.map((todo, index) => {
          if(index === action.index) {
            return Object.assign({}, todo, {
              completed: !todo.completed
            })
          }
          return todo
        })
      })
    default:
      return state
  }
}
</code></pre>
上面例子中todos和toggle的状态更新逻辑都写到一起了。因为它们的更新是相互独立的，所以可以拆分单独的reducer中。
* todos数据的reducer
<pre><code>
function todos(state = [], action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case TOGGLE_TODO:
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: !todo.completed
          })
        }
        return todo
      })
    default:
      return state
  }
}
</code></pre>
* visibilityFilter的reducer
<pre><code>
function visibilityFilter(state = SHOW_ALL, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return action.filter
    default:
      return state
  }
}
</code></pre>
* 主reducer
<pre><code>
function todoApp(state = {}, action) {
  return {
    visibilityFilter: visibilityFilter(state.visibilityFilter, action),
    todos: todos(state.todos, action)
  }
}
</code></pre>
**关键点： 每个子reducer负责state中的一部分数据的update逻辑。**

## 写法
Redux提供了**combineReducers（）**来简化写法：
<pre><code>
import { combineReducers } from 'redux';

const todoApp = combineReducers({
  visibilityFilter,
  todos
})

export default todoApp;
</code></pre>
combineReducers() 所做的只是生成一个函数，这个函数来调用你的一系列 reducer，每个 reducer 根据它们的 key 来筛选出 state 中的一部分数据并处理，然后这个生成的函数再将所有 reducer 的结果合并成一个大的对象。
