# TypeScript
## Interface
### 基本的interface
interface描述了一个object的类型信息，例如：
<pre><code>
interface LabelledValue {
    label: string;
}

function printLabel(labelledObj: LabelledValue) {
    console.log(labelledObj.label);
}

</code></pre>

interface支持定义可选参数：
<pre><code>
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
    let newSquare = {color: "white", area: 100};
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}

let mySquare = createSquare({color: "black"});

</code></pre>

### 可扩展属性的interface
同时interface也可以定义成已知属性和未知属性的集合：
<pre><code>
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
</code></pre>

### 函数的interface描述
函数作为一种特殊的object，也可以用interface来描述。
方式是给出参数表和返回类型， 形参的名字不必安全匹配：
<pre><code>
interface SearchFunc {
    (source: string, subString: string): boolean;
}
</code></pre>

### 可索引类型的interface
<pre><code>
interface StringArray {
    [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
</code></pre>

### Class类型
class可以实现一个interface，interface描述class中的public部分
<pre><code>
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
</code></pre>

class有两面，static的一面和instance的一面， class不能实现一个定义static类型的interface

### 扩展interface

interface可以互相扩展，将一个interface的成员拷贝到另外一个：
<pre><code>
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
</code></pre>

interface甚至可以扩展多个interface
<pre><code>
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
</code></pre>

### 混合类型
interface可以定义非常丰富的数据类型，例如
<pre><code>
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
</code></pre>
