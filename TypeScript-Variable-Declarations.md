
# TypeScript
## 变量声明
### 使用var声明
闭包的例子
<pre><code>
function f() {
    var a = 10;
    return function g() {
        var b = a + 1;
        return b;
    }
}

var g = f();
g(); // returns '11'
</code></pre>

g捕获了函数f中定义的变量a， 任何时候g被调用，a在f中的值都会被使用，即使g在f调用完成以
后调用，它仍然可以获取和修改a的值。

### 使用let声明变量
let提供了block-scoping，两个{}定义了一个scope， 例如：
<pre><code>
function f(input: boolean) {
    let a = 100;

    if (input) {
        // Still okay to reference 'a'
        let b = a + 1;
        return b;
    }

    // Error: 'b' doesn't exist here
    return b;
}
</code></pre>
在catch语句中定义的变量也有作用域
<pre><code>
try {
    throw "oh no!";
}
catch (e) {
    console.log("Oh well.");
}

// Error: 'e' doesn't exist here
console.log(e);
</code></pre>
block作用域声明的变量没有变量提升，因为在声明以前不能对其读写。
<pre><code>
a++; // illegal to use 'a' before it's declared;
let a;
</code></pre>
你仍然可以在函数中捕获外部变量，但是函数调用必须在变量声明之后， 例如下面就会报错：
<pre><code>
function foo() {
    // okay to capture 'a'
    return a;
}

// illegal call 'foo' before 'a' is declared
// runtimes should throw an error here
foo();

let a;
</code></pre>

### 重复声明和shadowing
使用var可以任意重复定义变量，它们都指向同一个变量。
而使用let在同一作用域内是不能重复定义的， 例如：
<pre><code>
let x = 10;
let x = 20; // error: can't re-declare 'x' in the same scope
</code></pre>
有一些不太明显的重复定义：
<pre><code>
function f(x) {
    let x = 100; // error: interferes with parameter declaration
}

function g() {
    let x = 100;
    var x = 100; // error: can't have both declarations of 'x'
}
</code></pre>

如果需要重复定义必须显示给一个新的scope， 例如：
<pre><code>
function f(condition, x) {
    if (condition) {
        let x = 100;
        return x;
    }

    return x;
}

f(false, 0); // returns '0'
f(true, 0);  // returns '100'
</code></pre>

在内部作用域重复定义一个变量叫做shadowing， 它是双刃剑：
<pre><code>
function sumMatrix(matrix: number[][]) {
    let sum = 0;
    for (let i = 0; i < matrix.length; i++) {
        var currentRow = matrix[i];
        for (let i = 0; i < currentRow.length; i++) {
            sum += currentRow[i];
        }
    }

    return sum;
}
</code></pre>
本例中， 内部定义的ishadowing外部的i，于是可以正确运行。

### block作用域的变量捕获
本例中getCity捕获了变量city， 我们仍然可以读取它即使if block执行完毕。
<pre><code>
function theCityThatAlwaysSleeps() {
    let getCity;

    if (true) {
        let city = "Seattle";
        getCity = function() {
            return city;
        }
    }

    return getCity();
}
</code></pre>
当在for循环中使用let声明， 每个循环中都会创建一个新的变量， 例如
<pre><code>
for (let i = 0; i < 10 ; i++) {
    setTimeout(function() {console.log(i); }, 100 * i);
}
</code></pre>
会输出如下结果：
<pre><code>
0
1
2
3
4
5
6
7
8
9
</code></pre>

### const变量声明
const变量一旦声明就不能再被赋值。但是其内部状态依然可以被改变， 这点与immutable不同。
<pre><code>
const numLivesForCat = 9;
const kitty = {
    name: "Aurora",
    numLives: numLivesForCat,
}

// Error
kitty = {
    name: "Danielle",
    numLives: numLivesForCat
};

// all "okay"
kitty.name = "Rory";
kitty.name = "Kitty";
kitty.name = "Cat";
kitty.numLives--;
</code></pre>

## 解构
### 数组解构
解构数组元素：

<pre><code>
let input = [1, 2];
let [first, second] = input;
console.log(first); // outputs 1
console.log(second); // outputs 2
</code></pre>

交换数组元素：

<pre><code>
// swap variables
[first, second] = [second, first];
</code></pre>

作为函数参数的数组解构：
<pre><code>
function f([first, second]: [number, number]) {
    console.log(first);
    console.log(second);
}
f(input);
</code></pre>

rest blablabla
<pre><code>
let [first, ...rest] = [1, 2, 3, 4];
console.log(first); // outputs 1
console.log(rest); // outputs [ 2, 3, 4 ]
</code></pre>

### Object解构
从object生成新的变量：
<pre><code>
let o = {
    a: "foo",
    b: 12,
    c: "bar"
}
let {a, b} = o;
</code></pre>

变量重命名：
<pre><code>
let {a: newName1, b: newName2} = o;
</code></pre>
等同于写成：
<pre><code>
let newName1 = o.a;
let newName2 = o.b;
</code></pre>
此处有陷阱， 冒号后面不是类型，如果你想声明类型，正确的姿势是：
<pre><code>
let {a, b}: {a: string, b: number} = o;
</code></pre>
缺省参数， keepWholeObject有一个wholeObject，它有属性a，b
<pre><code>
function keepWholeObject(wholeObject: {a: string, b?: number}) {
    let {a, b = 1001} = wholeObject;
}
</code></pre>

### 函数声明的解构
最简单的case
<pre><code>
type C = {a: string, b?: number}
function f({a, b}: C): void {
    // ...
}
</code></pre>
加入缺省参数：
<pre><code>
function f({a, b} = {a: "", b: 0}): void {
    // ...
}
f(); // ok, default to {a: "", b: 0}
</code></pre>
在解构参数中使用缺省参数：
<pre><code>
function f({a, b = 0} = {a: ""}): void {
    // ...
}
f({a: "yes"}) // ok, default b = 0
f() // ok, default to {a: ""}, which then defaults b = 0
f({}) // error, 'a' is required if you supply an argument
</code></pre>
