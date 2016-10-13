# 重头实现一个Promise
## 我们需要的Promise的用法如下
<pre><code>
doSomething().then(function(value) {
  console.log('Got a value:' value);
});
</code></pre>
## 定义Promise类型
<pre><code>
function Promise(fn) {
  var callback = null;
  this.then = function(cb) {
    callback = cb;
  };

  function resolve(value) {
    callback(value);
  }

  fn(resolve);
}
</code></pre>
有了这个Promise之后，我们的doSomething就可以这么写：
<pre><code>
function doSomething() {
  return new Promise(function(resolve) {
    var value = 42;
    resolve(value);
  });
}
</pre></code>

但是这么写有一个问题，new Promise的时候，resolve就会被执行，此时并未执行then。
我们暂且用setTimeout修复这个问题。
<pre><code>
function Promise(fn) {
  var callback = null;
  this.then = function(cb) {
    callback = cb;
  };

  function resolve(value) {
    // force callback to be called in the next
    // iteration of the event loop, giving
    // callback a chance to be set by then()
    setTimeout(function() {
      callback(value);
    }, 1);
  }

  fn(resolve);
}
</pre></code>
这么写代码是可以勉强工作了，但是上面的代码还是很容易出错。

## Promise是有状态的
* Promise可以处于pending状态， 或者resolved一个值。
* 一旦一个Promise resolve一个值，它将会一直保持在那个值而永远不会再次resolve成别的值。

**其实promise可以是rejected状态，后面将会提到**

我们来为Promise加上状态。
<pre><code>
function Promise(fn) {
  var state = 'pending';
  var value;
  var deferred;

  function resolve(newValue) {
    value = newValue;
    state = 'resolved';

    if(deferred) {
      handle(deferred);
    }
  }

  function handle(onResolved) {
    if(state === 'pending') {
      deferred = onResolved;
      return;
    }

    onResolved(value);
  }

  this.then = function(onResolved) {
    handle(onResolved);
  };

  fn(resolve);
}
</pre></code>
有了状态，我们就可以去掉setTimeout，我们来看一下此时的执行顺序。

1. new Promise并传给它一个function， 注意此处只是传入而不是调用function，此时promise处于pending状态
1. Promise内部调用传入的function，并把内部的resolve作为参数传给它
1. 传入的function内部会调用Promise内部的resolve函数将Promise状态设为resolved
1. doSomething.then被调用， then传入异步结果处理函数onResolved
1. Promise内部的handle函数会检查Promise的状态，如果是resolved，就直接使用resolve的值调用回调函数。如果是pending，就用deferred记录下回调函数，
当resolve被调用的时候，用resolve出的值调用deferred函数。

以上实现保证了，无论then在resolve之前还是之后被调用，其执行顺序都会是不变的，即先执行resolve然后执行then中的响应处理函数。