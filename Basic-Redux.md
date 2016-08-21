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
