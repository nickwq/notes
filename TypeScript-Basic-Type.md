# TypeScript
## 基础类型
### 布尔型
<pre><code>
let isDone: boolean = false;
</code>
</pre>

### 数值型
<pre><code>
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
</code>
</pre>

### 字符串
基础字符串用法
<pre><code>
let color: string = "blue";
color = 'red';
</code>
</pre>
模板字符串
<pre><code>
let fullName: string = `Bob Bobbington`;
let age: number = 37;
let sentence: string = `Hello, my name is ${ fullName }.

I'll be ${ age + 1 } years old next month.`
</code>
</pre>

### 数组
用法一：
<pre><code>
let list: number[] = [1, 2, 3];
</code>
</pre>
用法二：
<pre><code>
let list: Array< number> = [1, 2, 3];
</code>
</pre>

### Tuple
用于声明任意类型数据组成的数组
<pre><code>
// Declare a tuple type
let x: [string, number];
// Initialize it
x = ["hello", 10]; // OK
// Initialize it incorrectly
x = [10, "hello"]; // Error
</code>
</pre>

### 枚举型
声明方式：
<pre><code>
enum Color {Red, Green, Blue};
let c: Color = Color.Green;
</code>
</pre>
可以指定对应的整型值：
<pre><code>
enum Color {Red = 1, Green, Blue};
let c: Color = Color.Green;
</code>
</pre>
或者，
<pre><code>
enum Color {Red = 1, Green = 2, Blue = 4};
let c: Color = Color.Green;
</code>
</pre>
也可以通过index来引用：
<pre><code>
enum Color {Red = 1, Green, Blue};
let colorName: string = Color[2];

alert(colorName);
</code>
</pre>

### 任意类型 any
声明为任意类型， 用于未知类型的声明：
<pre><code>
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
</code>
</pre>

### 无返回类型
类似于any类型的对立， 用于无类型的声明，只能被赋值为null或者undefined
<pre><code>
function warnUser(): void {
    alert("This is my warning message");
}
</code>
</pre>
<pre><code>
let unusable: void = undefined;
</code>
</pre>

### 类型判定
用于类型转化：
第一种
<pre><code>
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;
</code>
</pre>

第二种
<pre><code>
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
</code>
</pre>
