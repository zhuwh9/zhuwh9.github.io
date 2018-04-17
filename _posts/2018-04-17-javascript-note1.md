---
layout: post
title: JavaScript高级程序设计笔记1
date: 2018-04-17 16:54:00+00:00
categories: 日志
tags: 博客
---

## 变量

```
function test() {
    var message = "hi";//local variable
}
test();
alert(message);//error!
```

var 定义的变量在其作用域内生效，没有用var定义的变量是全局的。

## 数据类型

五种简单数据类型：

- Undefined
- Null
- Boolean
- Number
- String

一种复杂数据类型：

- Object：一组无序的名值对组成。

## typeof

```
typeof('message');//'string'
typeof 'message';//'string'
```

- 'undefined' 如果值是未定义；
- ‘boolean’ 如果这个值是布尔值
- 'string' 如果这个值是字符串
- ‘number’ 如果这个值是数值
- ‘object’ 如果这个值是对象或null；
- ‘function’ 如果这个值是函数

**safari 5及之前的版本、chrome 7及之前版本对正则表达式调用该typeof操作符会返回‘function’，其他浏览器在这种情况会返回‘object’**

## null

null值表示一个空对象指针，因此用typeof null会返回'object'

## Boolean

- 字面值：true、false。**区分大小写**
- 所有类型的值都有与这两个字面值等价的值，通过转型函数Boolean（）进行转换。
- 转换规则
  - string: 任何非空字符串->true、空字符串->false
  - number：任何非零数字值->true，0和NaN->false
  - object：任何对象->true，null->false
  - undefined：n/a（不适用）->true，undefined->false

## Number

- 整数：

  ```
  var intNum = 55;
  ```

- 八进制：

  ```
  var octNum1 = 070;//有效的八进制
  var octNum2 = 079;//无效的八进制，解析为79
  var octNum3 = 08;//无效的八进制，解析为8
  ```

  **八进制字面量在严格模式下是无效的**

- 十六进制

  ```
  var hexNum1 = 0xA;
  var hexNum2 = 0x1f;
  ```

- 浮点数

  以下三种方式都可以（浮点数所需的内存空间是整数的两倍）

  ```
  var floatNum1 = 1.1;
  var floatNum2 = 0.1;
  var floatNum3 = .1;
  ```

  **如果小数点后没有数字，如1.0或1. ，则会呗自动转换为整数存储。**

  - 科学记数法（当小数点后面带有6个零以上的浮点数值转换为e表示法表示）

  ```
  var floatNum = 3.125e7;
  ```

  - 浮点数值的最高精度是17位小数

  ```
  0.1 + 0.2 = 0.30000000000000004
  ```

- 数值范围

  - Number.MIN_VALUE：5e-324
  - Number.MAX_VALUE：1.7976931348623156e+308
  - 如果超过数值范围，则会转换为-Infinity或Infinity
  - 判断是否为有穷的：调用isFinite(number)函数

## NaN

非数值，这个数值用于表示一个本来要返回数值的操作数未返回数值的情况

- 任何数值除以0都会返回NaN
- 任何涉及NaN的操作都会返回NaN（例如NaN/10）
- NaN与任何值都不相等，包括NaN本身。

**可以使用isNaN判断一个参数是否能转换成数值**

```
isNaN(NaN);//true
isNaN(10);//false，10是一个数值
isNaN("10");//false，可以被转换成数值10
isNaN("blue");//true，不能转换成数值
isNaN(true);//false，可以被转换成数值1
```

## 数值转换

### Number()函数的转换规则

- Boolean，true和false将分别被转换1和0。
- 数字值，只是简单的传入和返回。
- null值，返回0。
- undefined，返回NaN。
- 如果是字符串
  - 字符串只包含数字（包含带正负号的情况），则将其转换为十进制数值（前导的零被忽略了）
  - 字符串包含有效的浮点格式，如'1.1'，会被转换为对应的浮点数值（前导的零被忽略了）
  - 字符串包含有效的十六进制格式，如“0xf"，则将其转换为相同大小的十进制整数值
  - 如果字符串是空的，则将其转换为0
  - 其他情况转换为NaN。
- 如果是对象，则调用对象的valueOf()方法，然后依照前面的规则转换返回的值。
- 如果转换的结果是NaN，则调用对象的toString()方法，然后再次依照前面的规则转换返回的字符串值。

```
var num1 = Number("hello world");//NaN
var num2 = Number("");//0
var num3 = Number("000011");//11
var num4 = Number(true);//1
```

### parseInt()函数

- parseInt()函数在转换字符串时，更多的是看其是否符合数值模式。
- 忽略字符串前面的空格，直到找到第一个非空格字符。
- 如果第一个字符不是数字字符或负号，函数返回NaN
- parseInt()转换空字符串会返回NaN
- 如果第一个字符是数字字符，会一直解析到一个非数字字符
  - '1234blue'会被转换为1234
  - '22.5'会被转换为22，因为小数点不是有效的数字字符

```
var num1 = parseInt('1234blue');//1234
var num2 = parseInt('');//NaN
var num3 = parseInt('0xA');//10
var num4 = parseInt(22.5);//22
var num5 = parseInt('070');//56
var num6 = parseInt('70');//70
var num7 = parseInt('0xf');//15
```

- 给parseInt传第二个参数，指定进制

```
var num = parseInt('0xAF',16);//175
var num1 = parseInt('AF',16);//175
var num2 = parseInt('AF');//NaN
var num3 = parseInt('10',2);//2
var num4 = parseInt('10',8);//8
var num5 = parseInt('10',10);//10
var num3 = parseInt('10',16);//16
```

### parseFloat

- 字符串第一个小数点是有效的，第二小数点是无效的。
- 忽略前导的零
- 只解析十进制格式，十六进制全部转换为0
- 没有第二个参数指定进制的用法
- 如果解析出的数是整数，会返回整数

## string

### 字符字面量

```
\n --- 换行
\t --- 制表
\b --- 空格
\r --- 回车
\f --- 进纸
\\ --- 斜杠
\' --- 单引号
\" --- 双引号
\xnn --- 以十六进制代码nn代表的一个字符（n的范围是0~F）
\unnnn --- 以十六进制代码nnnn代表的一个unicode字符（其中n为0~F）
```

### 字符串的特点

**字符串是不可变的，一旦创建，它们的值就不能改变**

- 如果要改变某个变量保存的字符串
  - 销毁原来的字符串
  - 用另一个包含新值的字符串填充这个变量

```
var lang = "Java";
lang = lang + "Script";
```

上述代码实现的过程：

- 首先创建一个容纳10个字符的新字符串
- 在这个字符串中填充”Java“和”Script“
- 销毁原来的字符串”Java“和字符串”Script“

### 转换为字符串

**toString()方法**

- 几乎所有类型都有toString方法（除null和undefined）
- 可以传递一个参数，指定基数

**String()方法**

- 如果值有toString()方法，则调用该方法返回相应结果
- 如果值有null，则返回“null”；
- 如果值是undefined，则返回“undefined"

## Object

### 创建实例

```
var o = new Object();
var o = new Object;//有效，但不推荐
```

### 属性与方法

- Constructor：保存着用于创建当前对象的函数，仅对于new Object()创建的对象实例来说，函数是Object()
- hasOwnProperty(*propertyName*)：用于检查给定的属性在当前对象实例中是否存在。
  - *propertyName*以字符串形式传入，如”name“
- isPrototypeOf(object)：用于检查传入的对象是否是另一对象的原型
- propertyIsEnumerable(*propertyName*)：用于检查给定的属性是否能够用for-in语句来枚举
- toLocaleString()：返回对象的字符串表示，该字符串与执行环境的地区对应。
- toString()：返回对象的字符串表示。
- valueOf()：返回对象的字符串、数值或布尔值表示。


## 一元操作符

### 一元加、减操作符

对非数值类型变量应用一元加、减操作符，该操作符会像Number()转型函数一样对这个值执行转换。

### 二元操作符

#### 位操作符

##### 按位非(NOT)

```
var num1 = 25;
var num2 = ~num1;//-26
```

##### 按位与(AND)

```
var result = 25 & 3;//1
```

##### 按位或(OR)

```
var result = 25 | 3;//27
```

##### 按位异或(XOR)

```
var result = 25 ^ 3;//26
```

##### 左移

```
var oldValue = 2;
var newValue = oldValue << 5;//64
```

##### 有符号的右移

用符号位来填充最高位，舍弃最低位

```
var oldValue = 64;
var newValue = oldValue >> 5;//2
```

##### 无符号的右移

用0来填充最高位，舍弃最低位

```
var oldValue = 64;
var newValue = oldValue >>> 5;//2
```

#### 布尔操作符

#####逻辑非

用叹号（！）表示

- 对象，返回false
- 空字符串，返回true
- 非空字符串，返回false
- 数值0，返回true
- 非零数值（包括Infinity），返回false
- null，返回true
- NaN，返回true
- undefined，返回true

```
!false;//true
!"blue";//false
!0;//true
!NaN;//true
!"";//true
!12345;//false
```

##### 逻辑与

用两个和号（&&）表示

- 只有两个操作数都是true时，结果才是true；其它情况都是false。
- 逻辑与操作是短路操作，如果第一个操作数的求值结果为false，则不会对第二个操作数求值。
- 当两个操作数有一个不是布尔值时：
  - 如果第一个操作数是对象，返回第二个操作数
  - 如果第二个操作数是对象，只有第一个操作数的求值结果为true的情况才会返回该对象
  - 如果两个操作数都是对象，则返回第二个操作数
  - 如果有一个操作数是null，则返回null
  - 如果有一个操作数是NaN，则返回NaN
  - 如果有一个操作数是undefined，则返回undefined。

##### 逻辑或

- 用两个竖线符号（||）表示
  - 如果第一个操作数是对象，则返回第一个操作数
  - 如果第一个操作数的求值结果为false，则返回第二个操作数
  - 如果两个操作数都是对象，则返回第一个操作数
  - 如果两个操作数都是null，则返回null
  - 如果两个操作数都是NaN，则返回NaN
  - 如果两个操作数都是undefined，则返回undefined
- 逻辑或操作符是短路操作符

#### 乘性操作符

##### 乘法

特殊规则

- 如果有一个操作数是NaN，则结果是NaN
- 如果是Infinity与0相乘，则结果是NaN
- 如果是Infinity与非0数值项乘，则结果是NaN
- 如果是Infinity与Infinity相乘，则结果是Infinity
- 如果有一个操作数不是数值，则在后台调用Number()将其转换为数值，再应用上面的规则。

##### 除法

- 如果有一个操作数是NaN，则结果是NaN
- 如果是Infinity被Infinity除，则结果是NaN
- 如果是零被零除，则结果是NaN
- 如果是非零的有限数被零除，则结果是Infinity或-Infinity，取决于有符号操作数的符号；
- 如果是Infinity被任何非零数值除，则结果是Infinity或-Infinity，取决于有符号操作数的符号
- 如果有一个操作数不是数值，则在后台调用Number()将其转换为数值，然后再应用上面的规则

##### 求模

- 如果被除数是无穷大值而除数是有限大的数值，则结果是NaN
- 如果被除数是有限大的数值而除数是零，则结果是NaN
- 如果是Infinity被Infinity除，则结果是NaN
- 如果被除数是有限大的数值而除数是无穷大的数值，则结果是被除数
- 如果被除数是零，则结果是零
- 如果有一个操作数不是数值，则在后台调用Number()将其转换为数值，然后再应用上面的规则。

#### 加性操作符

##### 加法

- 如果有一个操作数是NaN，则结果是NaN
- 如果是Infinity加Infinity，则结果是Infinity
- 如果是-Infinity加-Infinity，则结果是-Infinity
- 如果是Infinity加-Infinity，则结果是NaN
- 如果是+0加+0，则结果是+0
- 如果是-0加-0，则结果是-0
- 如果是+0加-0，则结果是+0
- 如果有一个操作数是字符串，则有特殊规则
  - 如果两个操作数都是字符串，则将第二个操作数与第一个操作数拼接起来
  - 如果只有一个操作数是字符串，则将另一个操作数转换为字符串，然后再将两个字符串拼接起来
- 如果有一个操作数是对象、数值或布尔值，则调用它们的toString()方法取得相应的字符串值，然后再应用前面关于字符串的规则
- 如果是undefined和null，则分别调用String()函数并取得字符串“undefined”和“null”。

##### 减法

- 如果两个操作符都是数值，则执行常规的算数减法操作并返回结果
- 如果有一个操作数是NaN，则结果是NaN
- 如果Infinity减Infinity，则结果是NaN
- 如果-Infinity减-Infinity，则结果是NaN
- 如果Infinity减-Infinity，则结果是Infinity
- 如果-Infinity减Infinity，则结果是-Infinity
- 如果+0减+0，则结果是+0；
- 如果+0减-0，则结果是-0；
- 如果是-0减-0，则结果是+0。
- 如果有一个操作数是字符串、布尔值、null或undefined，则先在后台调用Number()函数将其转换为数值，然后再根据前面的规则执行减法计算。如果转换的结果都是NaN，则减法的结果就是NaN
- 如果有一个操作数是对象，则调用对象的valueOf()方法以取得表示该对象的数值。如果得到的值是NaN，则减法的结果就是NaN。如果对象没有valueOf()方法，则调用其toString()方法并将得到的字符串转换为数值。

#### 关系操作符

小于（<）、大于（>）、小于等于（<=）和大于等于（>=）

- 如果两个操作数都是数值，则执行数值比较
- 如果两个操作数都是字符串，则比较两个字符串对应的字符编码值
- 如果一个操作数是数值，则将另一个操作数转换为一个数值，然后执行数值比较。
- 如果一个操作数是对象，则调用这个对象的valueOf()方法，用得到的结果按照前面的规则执行比较。如果对象没有valueOf()方法，则调用toString()方法，并用得到的结果根据前面的规则执行比较。
- 如果一个操作数是布尔值，则先将其转换为数值，然后再执行比较。
- 任何操作数与NaN进行关系比较，结果都是false

**比较字符串时，是比较两个字符串中对应位置的每个字符的字符编码值，当某一个位置的字符值能比较出大小时，返回相应的结果**

#### 相等操作符

##### 相等和不相等

相等操作符由两个等于号（==）表示

不相等操作符由叹号后跟等于号（！=）表示

**两个操作符会先转换操作数，然后再比较它们的相等性**

- 如果有一个操作数是布尔值，则在比较相等性之前先将其转换为数值，false转换为0，true转换为1
- 如果一个操作数是字符串，另一个操作数是数值，则先将字符串转换为数值
- 如果一个操作数是对象，另一个操作数不是，则调用对象的valueOf()方法，用得到的基本类型值按照前面的规则进行比较；

两个操作符在进行比较时则要遵循下列规则

- null和undefined是相等的
- 要比较相等性之前，不能将null和undefined转换成其他值。
- 如果有一个操作数是NaN，则相等操作符返回false，而不相等操作符返回true。
  - 即使两个操作数是NaN，相等操作符也返回false
- 如果两个操作数都是对象，则比较它们是不是同一个对象，如果两个操作数都指向同一个对象，则相等操作符返回true；否则，返回false。

##### 全等和不全等

全等操作符由3个等于号（===）表示，只在两个操作数未经转换就相等的情况下返回true

不全等操作符由一个叹号后跟两个等于号（！==）表示，在两个操作数未经转换就不相等的情况下返回true。

#### 条件操作符

```
variable = boolean_expression ? true_value : false_value;
```

### 语句

#### if语句

```
if (condition)
	statement1
else
	statement2
```

#### do-while语句

```
do {
    statement
} while (expression)
```

#### while语句

```
while (expression) {
    statement
}
```

#### for语句

```
for (initialization; expression; post-loop-expression) {
    statement
}
```

#### for-in语句

用来枚举对象的属性

```
for (property in expression) {
    statement
}
```

如：

```
var o = {
    a : "cc",
    b : "ddd"
};
for (var propName in o) {
    console.log(o[propName]);//cc ddd
}
for (var propname in o) {
    console.log(propName);//a b
}
```

#### label语句

```
label: statement
```

#### break和continue语句

下例使用标签outermost，并结合break使用

```
var num = 0;
outermost:
for (var i = 1; i < 10; i++) {
    for (var j = 0; j < 10; j++) {
        if (i == 5 && j == 5) {
            break outermost;
        }
        num++;
    }
}
alert(num);//55
```

#### with语句

```
with (expression) statement;
```

如：

```
//没有用with
var qs = location.search.substring(1);
var hostname = location.hostname;
var url = location.href;

//用with
with (location) {
    var qs = search.substring(1);
    var hostname = hostname;
    var url = href;
}
```

**严格模式不允许使用with语句；大量使用with语句会导致性能下降**

#### switch语句

```
switch (expression) {
    case expression:
    	break;
    default:
    	break;
}
```

switch语句在比较值时使用的是全等操作符，因此不会发生类型转换。

### 函数

```
function functionName(arg0, arg1, ...argN) {
    statements
}
```

- 函数在定义时不必指定是否返回值。
- 函数在执行完return语句之后停止并立即退出。因此，位于return语句之后的任何代码都永远不会执行。
- return语句可以不带有任何返回值，在这种情况下，函数在停止执行后将返回undefined值。

### 基本类型

基本类型指的是简单的数据段

### 引用类型

指那些可能由多个值构成的对象。

- 引用类型的值是保存在内存中的对象

  **注意：Javascript不允许直接访问内存中的位置**

- 因此，操作对象时，实际是在操作对象的引用而不是实际的对象，所以引用类型（对象）是按引用访问的。

#### 访问对象属性的方式

- `person['name']`
- `person.name` 
  - 注意：当属性含有空格，不能用点表示法来访问

#### Array

##### 创建数组

```
var colors = new Array();
var colors = new Array(20);
var colors = new Array("red", "blue", "green");
var colors = ['red', 'blue', 'green'];
```

##### 一些版本导致的BUG

在IE8及之前版本中的ECMAScript实现在数组字面量方面存在bug。

下列数组的长度可能为2（IE9+、Firefox、Opera、Safari和Chrome）或3（IE8及之前的版本）

```
var values = [1,2,];
```

##### 访问已创建但未赋值的空间

```
var colors = ['red', 'blue', 'green'];
colors.length = 4;
colors[3];//undefined
```

##### 减少数组长度的后果

```
var colors = ['red', 'blue', 'green'];
colors.length = 2;
colors[2];//undefined
```

##### 对象是不是数组？

这个方法是建立在单一的全局执行环境

```
if (value instanceof Array){
    //do something
}
```

这个方法最有效(ES5后可用)

```
if (Array.isArray(value)) {
    //do somethng
}
```

使用Object.prototype上的原生toString()方法判断

```
var a = {};
var b = [];
var frame = document.createElement("iframe");
document.body.appendchild(frame);
var c = window.frames[0].Array;
var d = new c();
Object.prototype.toString.call(a);//[object Object]
Object.prototype.toString.call(b);//[object Array]
Object.prototype.toString.call(d);//[object Array]
function Person(){
    this.name = name;
}
var n = new Person();
Object.prototype.toString.call(n);//[object Object]
```

##### 栈方法

- push()方法可以接收任意数量的参数，把它们逐个加入到数组末尾，并返回修改后的数组的长度。
- pop()方法则从数组末尾移除最后一项，减少数组的length值，然后返回移除的项。

```javascript
var colors = new Array();
var count = colors.push("red", "green");
alert(count);//2
count = colors.push("black");
alert(count);//3
var item = colors.pop();
alert(item);//"black"
```

##### 队列方法

- push()向数组末端添加项。
- shift()方法移除数组中的第一项并返回该项。

```javascript
var colors = new Array();
var count = colors.push("red", "green");
alert(count);//2
count = colors.push("black");
alert(count);//3
var item = colors.shift();
alert(item);//"red"
alert(colors.length);//2
```

- unshift()方法能在数组前端添加任意个项并返回新数组的长度。

*同时使用unshift和pop方法，可以从相反方向来模拟队列，即在数组的前端添加项，从数组末端移除项*

##### 重排序方法

- reverse()会反转数组项的顺序

```javascript
var values = [1,2,3,4,5];
values.reverse();
alert(values);//5,4,3,2,1
```

- sort()会调用每个数组项的toString()转型方法，然后比较得到的字符串，以确定如何排序，即使数组中的每一项都是数值，sort()方法比较的也是字符串。

```javascript
var values = [0,1,5,10,15];
values.sort();
alert(values);//0,1,10,15,5
```

- sort()方法接收一个比较函数：

  - 比较函数接收两个参数

    - 如果第一个参数应该位于第二个之前则返回一个负数
    - 如果两个参数相等则返回0
    - 如果第一个参数应该位于第二个之后则返回一个正数

    ```javascript
    function compare(value1, value2) {
        if (value1 < value2) return -1;
        else if (value1 > value2) return 1;
        else return 0;
    }
    var values = [0, 1, 5, 10, 15];
    values.sort(compare);
    alert(values);//0,1,5,10,15
    ```

##### 操作方法

- concat()方法可以基于当前数组中的所有项创建一个新数组：
  - 先创建当前数组一个副本
  - 然后将接收到的参数添加到这个副本的末尾
  - 最后返回新构建的数组

```javascript
var colors = ['red', 'green', 'blue'];
var colors2 = colors.concat('yellow', ['black', 'brown']);
alert(colors);//red, green, blue
alert(colors2);//red, green, blue, yellow, black, brown
```

- slide()方法能够基于当前数组的一或多个项创建一个数组
  - slide()方法可以接受一或两个参数，即要返回项的起始和结束位置。
  - 在只有一个参数的情况下，slice()方法返回从该参数指定位置开始到当前数组末尾的所有项。
  - 如果有两个参数，该方法返回起始和结束位置之间的项（但不包含结束位置的项）

```javascript
var colors = ['red', 'green', 'blue', 'yellow', 'purple'];
var colors2 = colors.slice(1);//green,blue,yello,purple
var colors3 = colors.slice(1,4);//green,blue,yellow
```

- splice()方法
  - **删除** ：指定2个参数（要删除的第一项的位置，要删除的项数）
    - splice(0,2)会删除数组中的前2项
  - **插入** ：提供3个参数（起始位置、要删除的项数、要插入的项）
    - splice(2,0,'red','green')会从当前数组的位置2开始插入字符串'red'和'green'
  - **替换** ：提供3个参数（起始位置、要删除的项数、要插入的任意数量的项）
    - splice(2,1,'red','green')会删除当前数组位置2的项，然后从位置2插入字符串'red'和'green'
  - **splice()方法始终都会返回一个数组** ，该数组中包含从原始数组中删除的项（如果没有删除任何项，则会返回一个空数组）

##### 位置方法

- indexOf(number)方法从数组开头（位置0）开始向后查找
- lastIndexOf(number)方法从数组末尾开始向前查找

*没找到的情况下返回-1，比较时使用全等操作符===* 

##### 迭代方法

- every()：对数组中的每一项运行给定函数，如果该函数对每一项都返回true，则返回true

  ```javascript
  var numbers = [1,2,3,4,5,4,3,2,1];
  numbers.every(function(item, index, array) {
      return (item > 2);
  });// false
  ```

- filter()：对数组中的每一项运行给定函数，返回该函数会返回true的项组成的数组。

  ```javascript
  var numbers = [1,2,3,4,5,4,3,2,1];
  numbers.filter(function(item, index, array) {
      return (item > 2);
  });//[3,4,5,4,3]
  ```

- forEach()：对数组中的每一项运行给定函数，不返回值。

  ```javascript
  var numbers = [1,2,3,4,5,4,3,2,1];
  numbers.forEach(function(item, index, array) {
      // do something, no return value
  });
  ```

- map()：对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。

  ```javascript
  var numbers = [1,2,3,4,5,4,3,2,1];
  numbers.map(function(item, index, array) {
      return item * 2;
  });//[2,4,6,8,10,8,6,4,2]
  ```

- some()：对数组中的每一项运行给定函数，如果该函数对任一项返回true，则返回true。

##### 缩小方法

- reduce()接收4个参数：前一个值、当前值、项的索引和数组对象

  - 返回的任何值都会作为第一个参数自动传给下一项，第一次迭代发生在数组的第二项（参数一是数组第一项，参数二是数组第二项）

  ```javascript
  var values = [1,2,3,4,5];
  var sum = values.reduce(function(prev, cur, index, array) {
      return prev + cur;
  });//15
  ```

- reduceRight()从数组末尾向数组前端执行类似操作。

#### Date类型

- Date.UTC()：返回表示日期的毫秒数

```javascript
var y2k = new Date(Date.UTC(2000,0));//2000年1月1日0:00:00
```

- Date.parse()：解析传入的字符串为日期，如果不能解析，返回NaN

```javascript
var y2k = new Date(Date.parse("May 25, 2004"));
```

*直接使用`new Date("May 25, 2004")`也会在后台调用Date.parse()* 

- 月份从0开始到11

```javascript
var date = new Date(2005, 4, 5, 17, 55, 55);//2005年5月5日5:55:55
```

- ES5新方法：Date.now()方法返回调用这个方法时的日期和时间的毫秒数。

```javascript
var start = Date.now();
doSomething();
var stop = Date.now(), result = stop - start;//单位是毫秒
```

#### RegExp类型

##### 正则表达式语法

```
var expression = / pattern / flags;
```

##### 匹配模式

支持下列3个标志

- g：表示全局模式，即模式将被应用于所有字符串，而非在发现第一个匹配项时立即停止；
- i：表示不区分大小写模式，即在确定匹配项时忽略模式与字符串的大小写
- m：表示多行模式，即在到达一行文本末尾时还会继续查找下一行中是否存在与模式匹配的项

##### 元字符

所有元字符必须转义，元字符包括：

```
( [ { \ ^ $ | ) ? * + . ] }
```

如果想要匹配字符串中包含的这些字符，就必须对它们进行转义。

##### 匹配实例

###### 无转义字符

```javascript
var pattern1 = /at/g;//匹配字符串中所有'at'的实例
var pattern2 = /[bc]at/i;//匹配第一个'bat'或'cat'，不区分大小写
var pattern3 = /.at/gi;//匹配所有以'at'结尾的3个字符的组合，不区分大小写
```

###### 含转义字符

```javascript
var pattern4 = /\[bc\]at/i;//匹配第一个'[bc]at'，不区分大小写
```

##### 构造函数

以下`re1`和`re2`等价

```javascript
re1 = /cat/g;
re2 = new RegExp("cat","g");
```

##### 实例属性

- global：布尔值，表示是否设置了g标志
- ignoreCase：布尔值，表示是否设置了i标志
- lastIndex：整数，表示开始搜素下一个匹配项的字符位置，从0算起
- multiline：布尔值，表示是否设置了m标志
- source：正则表达式的字符串表示，按照字面量形式而非传入构造函数中的字符串模式返回（简单地说就是不包含模式符号`i` 、`g` 、`m`和`/` ）

##### 实例方法

- exec()：接受一个参数，即要应用模式的字符串，然后返回包含第一个匹配项信息的数组，没有匹配项的情况下返回null

  - 返回的数组是Array实例，但包含两个额外的属性：index和input。
    - index：表示匹配项在字符串中的位置
    - input表示应用正则表达式的字符串

  ```javascript
  var text = 'mom and dad and baby';
  var pattern = /mom( and dad( and baby)?)?/gi;
  var matches = pattern.exec(text);
  alert(matches.index);//0
  alert(matches.input);//'mom and dad and baby'
  alert(matches[0]);//'mom and dad and baby'
  alert(matches[1]);//' and dad and baby'
  alert(matches[2]);//' and baby'
  ```

  - 对于exec()方法而言，即使在模式中设置了全局标志（g），每次也只会返回一个匹配项
    - 设置了全局标志时，多次调用exec()会在字符串中继续查找新匹配项
    - 没设置全局标签时，多次调用exec()都会返回第一个匹配项

- test()：接受一个字符串参数，模式与参数匹配时返回true，否则返回false

#### Function类型

- 函数实际上是对象，每个函数都是Function类型的实例
- 函数名是指向函数的指针，所以一个函数可能有多个名字（或匿名函数）

##### 定义函数

不推荐的做法

```javascript
var sum = new Function("num1","num2", "return num1 + num2");
```

一般的做法

```javascript
// 函数声明语法
function sum(num1, num2) {
    return num1 + num2;
}
// 引用函数
var sum = function(num1, num2) {
    return num1 + num2;
};
```

##### 函数声明

解析器在向执行环境中加载数据时，对函数声明和函数表达式处理不同

- 解析器会率先读取函数声明，并使其在执行任何代码之前可用（可以访问）

  - 函数声明提升（function declaration hoisting）：在代码开始执行之前，读取并将函数声明添加到执行环境中
  - 对代码求值时，JavaScript引擎在第一遍会声明函数并将它们放到源代码树的顶部

  ```javascript
  // 会被正确提升函数声明的做法
  alert(sum(10,10));
  function sum(num1, num2) {
      return num1 + num2;
  }
  ```

  ```javascript
  // 不会被正确提升函数声明的做法
  alert(sum(10,10));
  var sum = function(num1, num2) {
      return num1 + num2;
  }
  ```

- 而函数表达式则必须等到解析器执行到它所在的代码行，才会真正被解释执行。

##### 函数的返回值是函数

```javascript
function createComparisonFunction(propertyName) {
    return function(object, object2) {
        var value1 = object[propertyName];
        var value2 = object[propertyName];
        if (value1 < value2) {
            return -1;
        } else if (value1 > value2) {
            return 1;
        } else {
            return 0;
        }
    }
}
// 数组调用sort方法，以比较某个属性为准
data.sort(createComparisonFunction('name'));
```

##### 函数内部属性

两个特殊的对象：`arguments`和`this`。

- `arguments` ：含有一个名叫`callee`的属性，`callee`是一个指针，指向拥有这个`arguments`对象的函数

  - 耦合的递归函数：

    ```javascript
    function factorial(num) {
        if (num <= 1) return 1;
        else return num * factorial(num - 1);
    }
    ```

  - 解耦的递归函数

    ```javascript
    function factorial(num) {
        if (num <= 1) return 1;
        else return num * arguments.callee(num - 1);
    }
    ```

- `this` ：引用的是函数数据以执行的环境对象

  ```javascript
  window.color = 'red';
  var o = {color: 'blue'};
  function sayColor() {
      alert(this.color);
  }
  sayColor();//'red'
  o.sayColor = sayColor();
  o.sayColor();//'blue'
  ```

- `caller` ：这个属性保存着调用当前函数的函数的引用，如果是在全局作用域中调用当前函数，它的值为null。

  ```javascript
  function outer() {
      inner();
  }
  function inner() {
      alert(arguments.callee.caller);
  }
  outer();
  ```

- 在严格模式下，访问`arguments.callee` 会导致错误；此外，不能为函数的caller属性赋值，否则会导致错误。

- ES5定义了`arguments.caller`属性，但在严格模式下会导致错误；非严格模式下这个属性始终是undefined

##### 函数属性和方法

- 属性
  - length属性：表示函数希望接受的命名参数的个数。
  - prototype属性：保存所有实例方法；prototype属性不可枚举，使用for-in无法发现。

- 方法

  - apply()方法接收两个参数：一个是在其中运行函数的作用域，另一个是参数数组（如Array的实例，arguments对象）

  ```javascript
  function sum(num1, num2) {
      return num1 + num2;
  }
  function callSum1(num1, num2) {
      return sum.apply(this, arguments);
  }
  function callSum2(num1, num2) {
      return sum.apply(this, [num1, num2]);
  }
  alert(callSum1(10,10));//20
  alert(callSum2(10,10));//20
  ```

  - call()方法：第一个参数是this，其余参数直接传递给函数

  ```javascript
  function sum(num1, num2) {
      return num1 + num2;
  }
  function callSum(num1, num2) {
      return sum.call(this, num1, num2);
  }
  alert(callSum(10,10));//20
  ```

  - apply()和call()能扩充函数赖以运行的作用域

  ```javascript
  window.color = 'red';
  var o = {color:'blue'};
  function sayColor() {
      alert(this.color);//必须使用this才能扩充作用域
  }
  sayColor();
  sayColor.call(this);//'red'
  sayColor.call(window);//'red'
  sayColor.call(o);//'blue'
  ```

- ES5新方法bind()：这个方法创建一个函数的实例，其this值会被绑定到传给bind()函数的值。（apply和call不会再生效）


  ```javascript
  window.color = 'red';
  var o = {color:'blue'};
  function sayColor(){
      alert(this.color);
  }
  var objectSayColor = sayColor.bind(o);
  objectSayColor();//blue
  ```

#### 基本包装类型

>  ECMAScript提供了3种特殊的引用类型：Boolean、Number、String。

- 每当读取一个基本类型值时，后台会创建一个对应的基本包装类型的对象（包含方法），访问基本包装类型时，访问过程处于一种读取模式，从内存中读取这个字符串的值。
  - 创建String类型的一个实例
  - 在实例上调用指定的方法
  - 销毁这个实例

> 引用类型与基本包装类型的主要区别就是对象的生存期。

```javascript
var s1 = 'some text';
//基本包装类型在被调用之后就销毁，不会存在于之后的代码所在的作用域中
s1.color = 'red';
console.log(s1.color);//undefined
```

> 用String、Boolean、Number创建的对象，调用typeof会返回'object'，且生存期跟一般对象一样，添加的属性或函数会一直在内存中

- Number类型方法：

  - toString()：可传递一个参数表示基数，告诉它返回几进制数值的字符串形式
  - toFixed()：可传递一个参数指定小数位数，按照指定的小数位返回数值的字符串表示
  - toExponential()：可传递一个参数，指定以指数表示法时，输出结果中的小数位数。
  - toPrecision()：传递一个参数，表示数值的所有数字的位数，不包含指数部分

- String类型方法：

  - charAt()：接收一个位置参数，返回特定位置的字符

  - charCodeAt()：接收一个位置参数，返回特定位置的字符的编码

  - concat()：接受多个参数，拼接成一个字符串，返回拼接后的字符串，不改变原字符串。

  - slice()、substr()、substring()：

    ```javascript
    var s = "hello world";
    console.log(s.slice(3));//'lo world'
    console.log(s.substring(3));//'lo world'
    console.log(s.substr(3));//'lo world'
    console.log(s.slice(3,7));//'lo w'
    console.log(s.substring(3,7));//'lo w'
    console.log(s.substr(3,7));//'lo worl'
    ```

  - indexOf()、lastIndexOf()：传入一个字符串

  - trim()：删除前置和后缀的所有空格，返回结果，不修改源字符串

  - toUpperCase()、toLowerCase()：全部转换为大、小写

  - match()：接受一个参数，正则表达式或RegExp对象，返回匹配的字符串的数组

  - search()：接受一个参数，正则表达式或RegExp对象，返回匹配的字符串的第一次出现的位置

  - replace()：接受两个参数，正则表达式或RegExp对象、替换的字符串，返回替换后的字符串（不影响源字符串）

    - 正则表达式指定全局标志(g)时，会替换所有匹配的字符串
    - 不指定全局标志时，只替换第一个匹配的子字符串

  - split()：接受一个或两个参数，第一个参数是字符串或RegExp对象，第二个参数是数组的大小（返回结果不会超过这个大小）

  - localeCompare()：比较两个字符串，字符串在字母表中应该排在字符串参数之前，返回负数；相等返回0；排在之后返回正数

#### 单体内置对象

> 内置对象：Object、Array、String
>
> ECMA-262定义的单体内置对象：Global和Math

##### Global

> 实际上没有全局变量或全局函数，所有在全局作用域中定义的属性和方法，都是Global对象的属性
>
> - isNaN、isFinite、parseInt、parseFloat方法都是Global对象的方法

###### URI编码方法

> - encodeURI()：接受一个字符串参数。不会对本身属于URI的特殊字符（如冒号、正斜杠、问号和井字号）进行编码
>   - decodeURI()：对应的解码方法，接受一个由encodeURI生成的字符串参数
> - encodeURIComponent()：会对它发现的所有非标准字符进行编码。
>   - decodeURIComponent()：对应的解码方法，接受一个由encodeURIComponent生成的字符串参数

###### eval方法

> - 传入的参数会被当作实际的ECMAScript语句来解析，然后把执行结果插入到原位置。
> - 严格模式下，在外部访问不到eval()中创建的任何变量或函数，为eval赋值也导致错误。

###### Global对象的属性

> ES 5明确禁止给undefined、NaN和Infinity赋值

###### window对象

> - 在web浏览器都是将Global全局对象作为window对象的一部分加以实现的。
> - 在全局作用域中声明的所有变量和函数，都会成为window对象的属性。

获取Global对象的方法：

```javascript
var global = function(){
    return this;
}();//立即调用的函数表达式
```

##### Math对象

- 属性

  > - Math.E：自然对数的底数，即常量e的值
  > - Math.LN10：10的自然对数
  > - Math.LN2：2的自然对数
  > - Math.LOG2E：以2为底e的对数
  > - Math.LOG10E：以10为底e的对数
  > - Math.PI：π的值
  > - Math.SQRT1_2：1/2的平方根
  > - Math.SQRT2：2的平方根

- 方法：

  > - min()：可传入多个参数，返回最小值
  >
  >   - 如果需要对一个数组进行求最小值，需要使用apply
  >
  >   ```javascript
  >   Math.min.apply(Math, array);
  >   ```
  >
  > - max()：可传入多个参数，返回最大值
  >
  >   ```javascript
  >   Math.max.apply(Math, array);
  >   ```
  >
  > - ceil()：执行向上舍入
  >
  > - floor()：执行向下舍入
  >
  > - round()：四舍五入
  >
  > - random()：返回介于0和1之间的一个随机数
  >
  > ```javascript
  > 值 = Math.floor(Math.random() * 可能值的总数 + 第一个可能的值)
  > ```
  >
  > - abs(num)：返回num的绝对值
  > - exp(num)：返回Math.E的num次幂
  > - log(num)：返回num的自然对数
  > - pow(num, power)：返回num的power次幂
  > - sqrt(num)：返回num的平方根
  > - acos(x)：返回x的反余弦值
  > - asin(x)：返回x的反正弦值
  > - atan(x)：返回x的反正切值
  > - atan2(y,x)：返回y/x的反正切值
  > - cos(x)：返回x的余弦值
  > - sin(x)：返回x的正弦值
  > - tan(x)：返回x的正切值

## 作用域链

### 检测类型

#### typeof

> 返回值包括：
>
> - string
> - number
> - boolean
> - undefined
> - object

#### instanceof操作符

```javascript
console.log(person instanceof Object);
console.log(colors instanceof Array);
console.log(pattern instanceof RegExp);
```

### 执行环境

- 全局执行环境是最外围的一个执行环境
- 每个函数都有自己的执行环境。当执行流进入一个函数时，函数的环境会被推入一个环境栈中；函数执行之后，栈将其环境弹出，把控制权返回给之前的执行环境。

### 作用域链

> 1. 作用域链的用途，是保证对执行环境有权访问的所有变量的有序访问。
> 2. 作用域链的前端，是当前执行代码所在环境的变量对象；如果这个环境是函数，则将其活动对象作为变量对象。
> 3. 活动对象在最开始时只包含一个变量，即arguments对象（不存在于全局环境），下一个变量对象来自包含（外部）环境，下一个变量对象来自下一个包含对象。

```javascript
var color = 'blue';
function changeColor() {
    var anotherColor = 'red';
    
    function swapColors() {
        var tempColor = anotherColor;
        anotherColor = color;
        color = tempColor;
        // 此处可访问color、anotherColor、tempColor
    }
    
    // 此处可访问color、anotherColor，但不可访问tempColor
    swapColors();
}
// 此处只可访问color
changeColor();
```

上述代码的作用域链为（内部环境可以通过作用域链访问所有的外部环境）

> window
>
> |——color
>
> |——changeColor
>
> ​		|——anotherColor
>
> ​		|——swapColors()
>
> ​				|——tempColor

### 延长作用链

- try-catch语句的catch块
- with语句


### 没有块级作用域

> - 类C的语言中，由花括号封闭的代码块都有自己的作用域

```javascript
if (true) {
    var color = "blue";//会将变量添加到全局环境中
}
console.log('blue');
```

> 对于有块级作用域的语言来说，for语句初始化后的变量i仅存在于循环的环境中。

```javascript
for (var i = 0; i < 10; i++) {
    doSomething(i);
}
console.log(i);//10
```

> 如果初始化变量时没有使用var声明，该变量会自动被添加到全局环境

```javascript
function add(num1, num2) {
    var sumA = num1 + num2;
    sumB = num1 + num2;
    return sumA;
}
var result = add(10, 20);//30
console.log(sumA);//error
console.log(sumB);//30
```

## 查询标识符

> 当在某个环境中为了读取或写入而引用一个标识符，必须通过搜索来确定标识符实际代表的变量。
>
> 1. 搜索过程从作用域链的前端开始
> 2. 向上逐级查询与给定名字匹配的标识符
> 3. 如果在局部环境中找到了该标识符，搜索过程停止，变量就绪；如果在局部环境中没有找到该变量名，则继续沿作用域链向上搜索，一直查询到全局环境的变量对象为止。

## 垃圾收集

> 原理：找出那些不再继续使用的变量，然后释放其占用占用的内存。
>
> 具体实现：垃圾收集器会按照固定的时间间隔，周期性地执行这一操作。

### 标记清除

> **进入环境** ：当变量进入环境（如在函数中声明一个变量）时，就将这个变量标记为“进入环境“
>
> **离开环境** ：当变量离开环境时，则将其标记为”离开环境“。

#### 内存清除

> 1. 垃圾收集器在运行时会给存储在内存中的所有变量都加上标记
> 2. 去掉环境中的变量以及被环境中的变量引用的变量的**标记** 
> 3. 在此之后再被加上标记的变量将被视为准备删除的变量
> 4. 最后，垃圾收集器销毁那些带标记的值并回收它们所占用的内存空间

## 引用计数

> 引用计数的含义是跟踪记录每个值被引用的次数。

1. 当声明一个变量并将一个引用类型值赋给该变量时，则这个值的引用次数就是1
2. 如果同一个值又赋给了另一个变量，则该值的引用次数加1；反之，如果包含对这个值引用的变量又取了另一个值，则这个值的引用次数减1。
3. 引用次数为0时，意味着将占用的内存空间回收回来。当垃圾收集器下次再运行时，就会释放那些引用次数为零的值所占用的内存。

### 循环引用

```javascript
// a和b的引用次数都是2，在使用引用计数时会出问题
function problem() {
    var a = new Object();
    var b = new Object();
    a.someOtherObject = b;
    b.anotherObject = a;
}
```

## 性能问题

**IE（7以前）的垃圾收集器** ：256个变量、4096个对象（或数组）字面量和数组元素（slot）或者64KB的字符串。达到上述任何一个临界值，垃圾收集器就会运行。

**IE7（及以后）的垃圾收集器** ：临界值修改为动态修正

> 1. 临界值在初始时与IE6相同
> 2. 当垃圾收集例程回收的内存分配量低于15%，则变量、字面量和（或）数组元素的临界值就会加倍。
> 3. 当例程回收了85%的内存分配量，则将各种临界值重置回默认值。

### 解除引用

> 一旦数据不再有用，最好将其值设为null来释放引用

## 面向对象的编程

### 数据属性

- [[configurable]]

  > 表示能否通过delete删除属性，能否修改属性的特性，或者能否把属性修改为访问器属性。默认值为true

- [[Enumerable]]

  > 表示能否通过for-in循环返回属性。默认值为true。

- [[Writable]]

  > 表示能否修改属性的值。默认值为true。

- [[Value]]

  > 包含这个属性的数据值。读取属性值的时候从这个位置读；写入属性值的时候，把新值保存在这个位置。这个位置默认值为undefined。

####修改属性默认的特性

方法：`Object.defineProperty()`

参数：`属性所在的对象` `属性的名字` `描述符对象` 

>  注：描述符对象的属性必须是`configurable` `enumerable` `writable` `value` 

```javascript
var person = {};
Object.defineProperty(person, 'name', {
    configurable: false,
    writable: false,
    value: "Nicholas"
});
console.log(person.name);//"Nicholas"
//非严格模式下，对不可写属性的赋值操作被忽略，严格模式下导致抛出错误
person.name = "Greg";
console.log(person.name);//"Nicholas"
//非严格模式下，对不可写属性的delete操作被忽略，严格模式下导致抛出错误
delete person.name;
console.log(person.name);//"Nicholas"
```

1. 一旦`configurable`设为`false`，就不可再改回`true`
2. 再调用`Object.defineProperty()`方法修改除`writable`之外的特性，都会导致错误。

### 访问器属性

访问器属性不包含数据值，包含一对getter和setter函数

> 1. 读取访问器属性时，会调用getter函数，它负责返回有效的值
> 2. 写入访问器属性时，会调用setter函数并传入新值，它负责决定如何处理数据。

访问器属性有以下4个特性：

> 1. [[Configurable]]：表示能否通过delete删除属性，能否修改属性的特性，或者能否把属性修改为访问器属性。默认值为true
> 2. [[Enumerable]]：表示能否通过for-in循环返回属性。
> 3. [[Get]]：在读取属性时调用的函数。默认值为undefined。
> 4. [[Set]]：在写入属性时调用的函数。默认值为undefined。

访问器属性不能直接定义，必须使用Object.definedProperty()来定义

```javascript
var book = {
    _year : 2004,
    edition: 1
};
Object.defineProperty(book, "year", {
    get : function() {
        return this._year;
    },
    set : function(newValue) {
        if (newValue > 2004) {
            this._year = newValue;
            this.edition += newValue - 2004;
        }
    }
});
book.year = 2005;
console.log(book.edition);//2
```

只指定getter意味着属性是不能写的

> 1. 在非严格模式下，尝试写入属性会忽略
> 2. 在严格模式下，尝试写入只指定了getter函数的属性会抛出错误

只指定setter时同理，严格模式下抛出错误，非严格模式下返回undefined。

### 定义多个属性

- ES5新方法Object.defineProperties()，可以通过描述符一次定义多个属性。

  - 接受两个对象参数：第一个是要添加和修改其属性的对象；第二个对象的属性与第一个对象中要添加或修改的属性一一对应。

  ```javascript
  var book = {};
  Object.defineProperties(book, {
      _year : {//默认条件下writable和configurable都是false
          value : 2004
      },
      edition : {//默认条件下writable和configurable都是false
          value : 1
      },
      year : {
          get : function() {
              return this._year;
          },
          set : function(newValue) {
              if (newValue > 2004) {
                  this._year = newValue;
                  this.edition += newValue - 2004;
              }
          }
      }
  });
  ```

- ES5方法Object.getOwnPropertyDescriptor()：属性所在的对象、要读取其描述符的属性名称。返回值是一个对象。

  > 访问器属性：configurable、enumerable、get和set
  >
  > 数据属性：configurable、enumerable、writable和value

  ```javascript
  var descriptor = Object.getOwnPropertyDescriptor(book, "_year");//数据属性
  console.log(descriptor.value);//2004
  console.log(descriptor.configurable);//false
  console.log(typeof descriptor.get);//"undefined"
  var descriptor = Object.getOwnPropertyDescriptor(book, "year");//访问器属性
  console.log(descriptor.value);//undefined
  console.log(descriptor.enumerable);//false
  console.log(typeof descriptor.get);//"function"
  ```

## 创建对象

### 工厂模式

用函数来封装以特定接口创建对象的细节

```javascript
function createPerson(name, age, job) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function() {
        console.log(this.name);
    };
    return o;
}
var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
```

### 构造函数模式

```javascript
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function() {
        console.log(this.name);
    };
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
console.log(person1.constructor == Person);//true
console.log(person2.constructor == Person);//true
```

要创建Person的新实例，必须使用new操作符。以这种方式调用构造函数会经历以下4个步骤：

> 1. 构造一个新对象；
> 2. 将构造函数的作用域赋给新对象（因此this就指向了这个新对象）；
> 3. 执行构造函数中的代码（为这个新对象添加属性）；
> 4. 返回新对象。

#### 把构造函数当成函数

```javascript
var person = new Person("Nicholas", 29, "Software Engineer");
person.sayName();//"Nicholas"

// 添加到window
Person("Greg", 27, "Doctor");
window.sayName();//"Greg"
var o = new Object();

//将构造函数中的this变为o
Person.call(o, "Kristen", 25, "Nurse");
o.sayName();//"Kristen"
```

构造函数中的方法在每次构造对象时，都会新建一个函数对象

#### 解决方法

将构造函数方法用外部方法代替

```javascript
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = sayName;
}
function sayName() {
    console.log(this.name);
}
```

缺陷：对象如果要定义很多方法，就要定义很多全局函数。

### 原型模式

#### 原型对象

> 1. 创建一个新函数
> 2. 根据一组特定的规则为该函数创建一个prototype属性，prototype属性指向函数的原型对象
> 3. 原型对象会自动获得一个constructor属性，constructor包含一个指向prototype属性所在函数的指针。

**举例：Person.prototype.constructor指向Person**

> 1. 创建自定义的构造函数后，原型对象（如 Person prototype）只会取得constructor属性
> 2. 其他方法都是从Object继承而来的

实例内部包含一个指针，指向构造函数的原型对象（ECMA-262称为`[[Prototype]]` 。

#### 确定对象之间的原型关系

##### isPrototypeOf()方法

```javascript
Person.prototype.isPrototypeOf(person1);//true
```

#####Object.getPrototypeOf()方法

```javascript
Object.getPrototypeOf(person1) == Person.prototype;//true
```

##### 读取对象的属性的过程

> 1. 搜索对象实例本身，如果没有找到，继续搜索指针指向的原型对象。
> 2. 搜索原型对象本身，如果有这个属性，则返回原型对象上的属性。

##### hasOwnProperty()方法

> 用于判断访问的属性属于实例属性还是原型属性

##### Object.getOwnPropertyDescriptor()方法

> 只能用于实例属性，且必须直接在原型对象上调用

#### 原型与in操作符

##### in操作符

> 会在通过对象能够访问给定属性时返回true，而不论这个属性存在于实例中还是原型中。

###### hasPrototypeProperty()方法（自定义）

```javascript
function hasPrototypeProperty(object, name) {
    //对象没有这个属性且这个属性存在于对象或原型中，所以只存在于原型中
    return !object.hasOwnProperty(name) && (name in object);
}
```

##### for-in循环

> 使用for-in循环时，返回的是所有能够通过对象访问的、可枚举的属性
>
> （如存在于实例中的属性，也包括存在于原型中的属性）

*所有开发人员定义的属性都是可枚举的* 

###### Object.keys()方法

```javascript
function Person() {}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
   	console.log(this.name);
};
var keys = Object.keys(Person.prototype);
console.log(keys);//"name,age,job,sayName"
var p1 = new Person();
p1.name = "Rob";
p1.age = 31;
var p1keys = Object.keys(p1);
console.log(p1keys);//"name,age"
```

##### Object.getOwnPropertyNames()方法

> 获取所有实例属性（不论是否可枚举）

```javascript
var keys = Object.getOwnPropertyNames(Person.prototype);
console.log(keys);//"constructor,name,age,job,sayName"
```

##### 简单的原型语法

```javascript
function Person(){}
Person.prototype = {
    name : "Nicholas",
    age : 29,
    job : "Software Engineer",
    sayName : function () {
        console.log(this.name);
    }
};
```

问题：重写了默认的prototype对象，constructor属性指向Object构造函数而非Person函数。

```javascript
var friend = new Person();
console.log(friend instanceof Object);//true
console.log(friend instanceof Person);//true
console.log(friend.constructor == Person);//false
console.log(friend.constructor == Object);//true
```

解决办法：在字面量中重新指明constructor指向Person

*（同样会造成问题，constructor属性的[[Enumerable]]特性被设置为true）* 

```javascript
function Person() {}
Person.prototype = {
    constructor : Person,
    name : "Nicholas",
    age : 29,
    job : "Software Engineer",
    sayName : function () {
        console.log(this.name);
    }
};
// 解决[[Enumerable]]为true的方法，使用ES5兼容的函数defineProperty
Object.defineProperty(Person.prototype, "constructor", {
    enumerable : false,
    value : Person
});
```

##### 原型的动态性

对原型对象上的方法/属性的修改会立即从实例上反映出来

```javascript
var friend = new Person();
Person.prototype.sayHi = function () {
    console.log("hi");
};
friend.sayHi();//"hi"
```

###### 重写prototype时动态性消失

```javascript
function Person() {}
var friend = new Person();
// 实例中的指针仅指向原型，而不指向构造函数
Person.prototype = {
    //旧的原型对象没有指针指向它了，现在Person指向新的Person.prototype对象
    constructor : Person,
    name : "Nicholas",
    age : 29,
    job : "Software Engineer",
    sayName : function() {
        console.log(this.name);
    }
};
friend.sayName();//error，因为friend指向的原型中不包含以该名字命名的属性
```

#### 原生对象的原型

通过原生引用类型（Object、Array、String）的原型可以找到许多方法

```javascript
var a = [1,3,2];
Array.prototype.sort.apply(a);
console.log(a);
```

还可以通过原生对象的原型定义新方法

```javascript
String.prototype.startsWith = function (text) {
    return this.indexOf(text) == 0;
};
var msg = "Hello world!";
console.log(msg.startsWith("Hello"));//true
```

### 组合使用构造模式和原型模式

> 实例属性都在构造函数中定义，而由所有实例共享的属性constructor和方法则是在原型中定义。

### 动态原型模式

```javascript
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    if (typeof this.sayName != "function") {
        // 千万别用对象字面量重写原型，因为这样做会切断实例和新原型的联系
        Person.prototype.sayName = function(){
            console.log(this.name)''
        };
    }
}
```

### 寄生构造函数模式

> 基本思想：创建一个函数，这个函数的作用是封装创建对象的代码，然后返回新创建的对象

```javascript
function Person(name, age, job) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function () {
        console.log(this.name);
    };
    return o;
}
var friend = new Person("Nicholas", 29, "Software Engineer");
friend.sayName();//"Nicholas"
```

缺点：无法通过`instanceof` 判断对象类型。

### 稳妥构造函数模式

> 稳妥对象：指没有公共属性，且其方法也不引用this的对象。

```javascript
function Person(name, age, job) {
    var o = new Object();
    
    // 可以在这里定义私有变量和函数
    
    // 添加方法
    o.sayName = function () {//只有sayName()方法可访问数据成员
        console.log(this.name);
    };
    return o;
}
```

## 继承

### 原型链

```javascript
function SuperType(){
    this.property = true;
}
SuperType.prototype.getSuperValue = function() {
    return this.property;
}
function SubType(){
    this.subproperty = false;
}
// 修改原型、实现继承
SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function() {
    return this.subproperty;
};
var instance = new SubType();
alert(instance.getSuperValue());
```

#### 原型链的问题

- 在重写超类方法之前，必须要先替换原型。

  ```javascript
  function SuperType() {
      this.property = true;
  }
  // 超类方法
  SuperType.prototype.getSuperValue = function() {
      return this.property;
  };

  function SubType(){
      this.subproperty = false;
  }
  // 先替换原型
  SubType.prototype = new SuperType();
  // 添加新方法
  SubType.prototype.getSubValue = function() {
      return this.subproperty;
  };
  // 重写超类方法
  SubType.prototype.getSuperValue = function() {
      return false;
  };
  ```

  ​

- 通过原型链进行继承时，不能使用对象字面量创建原型方法，因为这样做就会重写原型链。

  ```javascript
  function SuperType() {
      this.property = true;
  }
  SuperType.prototype.getSuperValue = function() {
      return this.property;
  };
  function SubType() {
      this.subproperty = false;
  }
  SubType.prototype = new SuperType();
  // 以下字面量会重写原型链，导致继承无效
  SubType.prototype = {
      getSubValue : function() {...},
      someMethod : function() {...}
  };
  ```

- 包含引用类型值的原型属性会被所有实例共享，通过原型实现继承时，原型实际上会变成另一个类型的实例。

#### 解决方法

##### 借用构造函数（Construction stealing）

通过apply()和call()方法再新创建的对象上执行构造函数。

```javascript
function SuperType(){
	this.colors = {"red","blue","green"};
}
function SubType(){
    SuperType.call(this);
}
var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors);//"red,blue,green,black"
var instance2 = new SubType();
alert(instance2.colors);//"red,blue,green"

```

##### 传递参数

```javascript
function SuperType(name) {
    this.name = name;
}
function SubType(){
    SuperType.call(this, "Nicholas");
	this.age = 29;
}
var instance = new SubType();
alert(instance.name);//"Nicholas"
alert(instance.age);//29
```

#### 组合继承

```javascript
function SuperType(name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}
SuperType.prototype.sayName = function () {
    console.log(this.name);
};
function SubType(name, age) {
    SuperType.call(this, name);
    this.age = age;
}
SubType.prototype = new SuperType();
SubType.prototype.sayAge = function () {
    console.log(this.age);
};
var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
console.log(instance1.colors);//'red,blue,green,black'
instance1.sayName();//'Nicholas'
instance1.sayAge();//29

var instance2 = new SubType("Greg", 27);
console.log(instance2.colors);//'red,blue,green'
instance2.sayName();//'Greg'
instance2.sayAge();//27
```

#### 原型式继承

```javascript
// 本质上，object()对传入对象执行了一次浅复制
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}
```

```javascript
var person = {
    name : 'Nicholas',
    friends : ['Shelby', 'Court', 'Van']
};
var another = object(person);//复制了person对象的属性
another.name = 'Greg';
another.friends.push('Rob');

var yetAnother object(person);
yetAnother.name = 'Linda';
yetAnother.friends.push('Barbie');

console.log(person.friends);//'Shelby,Court,Van,Rob,Barbie'
```

ES5新增方法：Object.create()。

- 接受两个参数：一个用作新对象原型的对象和一个为新对象定义额外属性的对象。

```javascript
var person = {
    name : 'Nicholas',
    friends : ['Shelby', 'Court', 'Van']
};
/**
 *如果只传入第一个参数
 */
var another = Object.create(person);//复制了person对象的属性
another.name = 'Greg';
another.friends.push('Rob');
var yetAnother Object.create(person);
yetAnother.name = 'Linda';
yetAnother.friends.push('Barbie');
console.log(person.friends);//'Shelby,Court,Van,Rob,Barbie'
/**
 *如果传入第二个参数
 */
var anotherPerson = Object.create(person, {
    name : {
        value : "Greg"
    }
});
```

#### 寄生式继承

```javascript
function createAnother(original) {
    var clone = object(original);//以原型式浅复制一个对象
    clone.sayHi = function() {//增强这个复制对象的属性和方法
        console.log("hi");
    };
    return clone;
}
```

#### 寄生组合式继承

```javascript
function SuperType(name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}
SuperType.prototype.sayName = function () {
    console.log(this.name);
};
function SubType(name, age) {
    SuperType.call(this, name);//第二次调用SuperType()
    this.age = age;
}
SubType.prototype = new SuperType();//第一次调用SuperType()
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function () {
    console.log(this.age);
};
```

# 函数表达式

## 函数声明提升

> 执行代码前会先读取函数声明，把函数声明放在调用它的语句后面。

```javascript
sayHi();//不会报错，因为函数声明提升
function sayHi() {
    console.log("Hi");
}
```

## 匿名函数

```javascript
var functionName = function(arg0, arg1, arg2) {
    //函数体
};
```

## 递归

阶乘递归函数

```javascript
function factorial(num) {
    if (num <= 1) return 1;
    else return num * factorial(num - 1);
}
var another = factorial;
factorial = null;
console.log(another(4));//error,因为内部调用的factorial失效了
```

### arguments.callee

> arguments.callee是一个指向正在执行的函数的指针。
>
> （注：仅能在非严格模式下使用）

```javascript
function factorial(num) {
    if (num <= 1) return 1;
    else return num * arguments.callee(num - 1);
}
```

### 使用命名函数表达式实现递归

> 创建一个名为f()的命名函数表达式，即使函数赋给另一个变量，函数的名字f仍然有效。

```javascript
var factorial = (function f(num) {
    if (num <= 1) return 1;
    else return num * f(num - 1);
});
```

## 闭包

> 1. 闭包是指有权访问另一个函数作用域中的变量的函数
> 2. 创建闭包的常见方式，就是在一个函数内部创建另一个函数。
> 3. 内部函数的作用域链中包含外部包含函数的作用域，所以内部函数可以调用外部函数的变量

```javascript
function createFunctions() {
    var result = new Array();
    for (var i = 0; i < 10; i++) {
        result[i] = function () {
            return i;//
        }(i);
    }
    return result;
}
```

### 关于this对象

this对象是在运行时基于函数的执行环境绑定的：

> 1. 在全局函数中，this等于window
> 2. 当函数被作为某个对象的方法调用时，this等于那个对象
> 3. 匿名函数的执行环境具有全局性，此时this通常指向window。

#### 使用that解决this作用域问题

```javascript
var name = "The Window";
var object = {
    name : "My Object",
    getNameFunc : function () {
        return function() {
            return this.name;
        };
    }
};
console.log(object.getNameFunc()());//"The Window"
```

解决办法：通过that来获取this

```javascript
var name = "The Window";
var object = {
    name : "My Object",
    getNameFunc : function () {
        var that = this;
        return function() {
            return that.name;
        };
    }
};
console.log(object.getNameFunc()());//"My Object"
```

### 内存泄漏

> 如果闭包的作用域链中保存着一个HTML元素，那么就意味着该元素无法被销毁。

```javascript
function assignHandler () {
    var element = document.getElementById("someElement");
    var id = element.id;//通过类似that的方法
    element.onclick = function () {
        console.log(id);
    };
    // 解决办法
    element = null;
}
```

### 模仿块级作用域

>  通过匿名函数来模仿块级作用域（私有作用域）

```javascript
(function () {
    // 此处是块级作用域
})();

var someFunction = function () {
    // 这里是块级作用域
}
```

验证块级作用域：

```javascript
function outputNumbers(count) {
    // 通过这种方式创建临时变量
    (function () {
        for (var i = 0; i < count; i++) {
            console.log(i);
        }
    })();
    console.log(i);//error
}
```

### 私有变量

> 函数内部可以访问的变量，而函数外部则不能访问它们

```javascript
function add(num1, num2) {
    // 共3个私有变量：num1、num2和sum
    var sum = num1 + num2;
    return sum;
}
```

#### 特权方法(privileged method)

```javascript
function MyObject() {
    // 私有变量和私有函数
    var privateVariable = 10;
    function privateFunction () {
        return false;
    }
    // 特权方法
    this.publicMethod = function () {
        privateVariable++;
        return privateFunction();
    }
}
```

#### 创建带有私有数据的对象

```javascript
function Person(name) {
    this.getName = function() {
        return name;
    };
    this.setName = function (value) {
        name = value;
    }
}
```

### 静态私有变量

> 特权方法是在原型上定义的，所以所有实例都都是用同一个函数。所以闭包中的变量是静态的，被所有实例共享的。

```javascript
(function () {
    var num = 0;
    function getNum() {
        return num;
    }
    MyObject = function () {

    };//因为没有用var，所以MyObject是全局变量
    MyObject.prototype.publicGetNum = function () {
        return getNum();
    };
    MyObject.prototype.publicSetNum = function (value) {
        num = value;
    };
})();
console.log(MyObject.prototype.publicGetNum());//0
MyObject.prototype.publicSetNum(2);
console.log(MyObject.prototype.publicGetNum());//2
```

### 模块模式

> 1. 模块模式是为单例创建私有变量的特权方法。
> 2. 单例指的是只有一个实例的对象

```javascript
var singleton = function () {
    // 私有变量和私有函数
    var privateVariable = 10;
    function privateFunction () {
        return false;
    }
    // 返回对象字面量只包含可以公开的属性和方法
    return {
        publicProperty : true,
        publicMethod : function () {
            privateVariable++;
            return privateFunction();
        }
    };
}();
```

> 对单例模式进行初始化

```javascript
var application = function () {
    // 私有变量和函数
    var components = new Array();
    // 初始化
    components.push(new BaseComponent());
    // 公共，有权访问components的特权函数
    return {
        getComponentCount : function () {
            return components.length;
        },
        registerComponent : function (component) {
            if (typeof component == "object") {
                components.push(component);
            }
        }
    };
}();
```

#### 增强的模块模式

```javascript
var singleton = function () {
    // 私有变量和函数
    var privateVariable = 10;
    function privateFunction () {
        return false;
    }
    
    // 创建对象
    var object = new CustomType();
    
    // 添加特权/公有属性和方法
    object.publicProperty = true;
    object.publicMethod = function () {
        privateVariable++;
        return privateFunction();
    }
    
    // 返回对象
    return object;
}();
```

# BOM

## 核心——window对象

> 1. window对象既是JavaScript访问浏览器窗口的一个接口，又是ECMAScript规定的Global对象。
> 2. 所有在全局作用域中声明的变量、函数都会变成window对象的属性和方法
> 3. 全局变量不能通过delete操作符删除，而直接在window对象上的定义的属性可以。

### frames

> 1. 如果页面包含框架，则每个框架都拥有自己的window对象，并且保存在frames集合中。
> 2. 在frames集合中，可以通过数值索引（从0开始，从左至右，从上到下）或者框架名称来访问对应的window对象。
> 3. 每个window对象都有一个name属性

```html
<html>
    <head>
        <title>Frameset Example</title>
    </head>
    <frameset row="160,*">
        <frame src="frame.htm" name="topFrame"></frame>
    	<frameset cols="50%,50%">
        	<frame src="anotherframe.htm" name="leftFrame"></frame>
    		<frame src="yetanotherframe.htm" name="rightFrame"></frame>
    	</frameset>
    </frameset>
</html>
```

以上创建一个框架集，一个框架居上，两个框架居下。

- 可以通过`window.frames[0]`或`top.frames[0]` 引用上方的框架
- 可以通过`window.frames["topFrame"]` 或`top.frames["topFrame"]` 引用上方的框架
- 与top相对的另一个`window`对象是`parent`。`parent` 对象始终指向当前框架的直接上层框架。在某些情况下，`parent`可能等于`top`，在没有框架的情况下，`parent`一定等于`top`（此时它们都等于`window`）。

### 窗口位置

- （IE、Safari、Opera、Chrome提供）screenLeft和screenTop属性分别用于表示窗口相对于屏幕左边和上边的位置。
- （FireFox、Safari、Chrome提供）screenX和screenY属性。但Opera中screenX和screenY属性与screenLeft和screenTop属性并不对应，因此不推荐在Opera使用它们。

```javascript
// 最大兼容性
var leftPos = (typeof window.screenLeft == 'number') ? window.screenLeft : window.screenX;
var topPos = (typeof window.screenTop == 'number') ? window.screenTop : window.screenY;
```

#### 移动窗口

> moveTo()：接收新位置x和y坐标值。
>
> moveBy()：接收水平和垂直方向上移动的像素数。
>
> **注：这两个方法都不适用于框架，只能对最外层的window对象使用**

```javascript
// 将窗口移动到屏幕左上角
window.moveTo(0,0);
// 将窗口向下移动100像素
window.moveBy(0,100);
// 将窗口移动到（200，300）
window.moveTo(200,300);
// 将窗口向左移动50像素
window.moveBy(-50,0);
```

### 窗口大小

> IE9+、Firefox、Safari、Opera和Chrome提供4个属性：innerWidth、innerHeight、outerWidth和outerHeight。

> IE9+、Safari和Firefox中，outerWidth和outerHeight返回浏览器窗口本身的尺寸（无论是从最外层的window对象还是从某个框架访问）。

> 在Opera中，outerWidth和outerHeight表示页面视图容器（指的是Opera中单个标签页对应的浏览器窗口）的大小，而innerWidth和innerHeight则表示该容器中页面视图区的大小（减去边框宽度）。

> 在Chrome中，outerWidth、outerHeight与innerWidth、innerHeight返回相同的值，即视口（viewport）大小而非浏览器窗口大小。

- 在IE、Firefox、Safari和Chrome中，`document.documentElement.clientWidth`和`document.documentElement.clientHeight`中保存了页面视口的信息。
  - 在IE6中，这些属性必须在标准模式下才有效；如果是混杂模式，就必须通过`document.body.clientWidth`和`document.body.clientHeight`取得相同信息
  - 对于混杂模式下的Chrome，则无论通过`document.documentElement`还是`document.body`中的`clientWidth`和`clientHeight`属性，都可以取得视口的大小。

#### 调整窗口大小的方法

> window.resizeTo()：接受两个参数，接收浏览器窗口的新款都和新高度
>
> window.resizeBy()：接收新窗口与原窗口的宽度和高度之差。

```javascript
// 调整到100×100
window.resizeTo(100, 100);
// 调整到200×150
window.resizeBy(100, 50);
// 调整到300×300
window.resizeTo(300, 300);
```

### 导航和打开窗口

> window.open()：可以接收4个参数
>
> 1. 要加载的URL
> 2. 窗口目标：可以是任何一个特殊的窗口名称（字符串）：\_self、\_parent、\_top 、\_blank
> 3. 一个特性字符串：用逗号分隔的设置字符串，表示在新窗口中都显示哪些特性
>    - fullscreen：值（yes或no），表示浏览器窗口是否最大化。仅限IE
>    - height：值（数值），表示新窗口的高度。不能小于100
>    - left：值（数值），表示新窗口的左坐标。不能是负值
>    - location：值（yes或no），表示是否在浏览器窗口中设置地址栏。不同的浏览器的默认值不同，如果设置为no，地址栏可能会隐藏，也可能会被禁用（取决于浏览器）
>    - menubar：值（yes或no），表示是否在浏览器窗口中显示菜单栏。默认值为no。
>    - resizable：值（yes或no），表示是否可以通过浏览器窗口的边框改变其大小。默认值为no。
>    - scrollbars：值（yes或no），表示如果内容在视口中显示不下，是否允许滚动。默认值为no
>    - status：值（yes或no），表示是否在浏览器窗口中显示状态栏。默认值为no。
>    - toolbar：值（yes或no），表示是否在浏览器窗口显示工具栏。默认值为no。
>    - top：值（数值），表示新窗口的上坐标，不能是负值。
>    - width：值（数值），表示新窗口的宽度。不能小于100。
> 4. 一个布尔值（表示新页面是否取代浏览器历史记录中当前加载页面）

```javascript
// 等同于<a href="http://www.wrox.com" target="topFrame"></a>
window.open("http://www.wrox.com/", "topFrame", "height=400,width=400,top=10,left=10,resizable=yes");
// 如果有一个名叫"topFrame"的窗口或者框架，就会在该窗口或框架加载这个URL；
// 否则，就会创建一个新窗口并将其命名为"topFrame"
```

#### 关闭打开的窗口

操作`window.open`返回的对象：

- closed属性：窗口的引用还在，但实际窗口已经关闭时返回true
- opener属性：保存着打开它的原始窗口对象。将opener属性设置为null时，会告诉浏览器，新创建的标签页不需要与打开它的标签页通信，可以在独立的进程运行。一旦切断联系，将无法恢复。

```javascript
var wroxWin = window.open("http://www.wrox.com/", "wroxWindow", "height=400,width=400,top=10,left=10,resizable=yes");
wroxWin.resizeTo(500,500);
wroxWin.moveTo(100,100);
wroxWin.close();
wroxWin.opener = null;// 切断标签页之间的联系，没办法恢复
console.log(wroxWin.closed);//true
console.log(wroxWin.opener == window);//true
```

#### 弹出窗口屏蔽程序

> 如果是浏览器内置的屏蔽程序阻止的弹出窗口，则window.open()很可能会返回null。

```javascript
var blocked = false;
try {
    var wroxWin = window.open("http://www.wrox.com", "_blank");
    if (wroxWin == null) {
        blocked = true;
    }
} catch (ex) {
    blocked = true;
}
if (blocked) {
    console.log("The popup was blocked");
}
```

### 间歇调用和超时调用

#### 超时调用(setTimeout方法)

>  使用window对象的setTimeout()方法，接收两个参数：要执行的代码、以毫秒表示的时间（即在执行代码前需要等待多少毫秒）。
>
> - 第一个参数：一个包含JavaScript代码的字符串（和在eval()函数中使用的字符串一样）；或一个函数
> - 第二个参数：一个表示等待多长时间的毫秒数，但经历该时间后指定的代码不一定会执行，而是将这些代码添加到队列中，然后顺序执行队列中的代码（如果队列为空，则等待时间结束立即执行代码）

#### 取消超时调用(clearTimeout方法)

> 调用setTimeout()之后，该方法会返回一个数值ID，表示超时调用，可以通过这个超时调用ID取消超时调用。要取消尚未执行的超时调用计划，可以调用clearTimeout()方法并将相应的超时调用ID作为参数传递给它。

```javascript
// 设置超时调用
var timeoutId = setTimeout(function () {
    console.log("Hello world!");
}, 1000);

// 取消超时调用
clearTimeout(timeoutId);
```

### 间歇调用(setInterval方法)

```javascript
var num = 0, max = 10, intervalId = null;
function incrementNumber () {
    num++;
    // 如果执行次数达到了max设定的值，则取消后续尚未执行的调用
    if (num == max) {
        clearInterval(intervalId);
        console.log('Done');
    }
}
// 变量num每半秒钟传递一次，当递增到最大值时会取消先前设定的间歇调用
var intervalId = setInterval(incrementNumber, 500);
```

### 系统对话框

> - alert
> - confirm
> - prompt

#### confirm对话框

```javascript
if (confirm("Are you sure?")) {//点击OK时
    console.log("I'm so glad you're sure!")
} else {//点击Cancel按钮
    console.log("I'm sorry to hear you're not sure");
}
```

#### prompt对话框

> 接收两个参数：
>
> 1. 要给用户显示的文本提示
> 2. 文本输入域的默认值
>
> 返回结果：
>
> - 文本输入域的值

```javascript
var result = prompt("what's your name?", "Michael");
if (result != null) {
    console.log("Welcome, " + result);
}
```

### 查找、打印

> 查找、打印：这两种对话框都是异步显示的，能够将控制权立即交还给脚本。

```javascript
// 显示“打印”对话框
window.print();

// 显示“查找”对话框
window.find();
```

## location对象

> location是最有用的BOM对象之一

### 属性

| 属性名   | 例子                  | 说明                                                         |
| -------- | --------------------- | ------------------------------------------------------------ |
| hash     | "#contents"           | 返回URL中的hash（#号后跟零或多个字符），如果URL中不包含散列，则返回空字符串。 |
| host     | "www.wrox.com:80"     | 返回服务器名称和端口号（如果有）                             |
| hostname | "www.wrox.com"        | 返回不带端口号的服务器名称                                   |
| href     | "http://www.wrox.com" | 返回当前加载界面的完整URL。而location对象的toString()方法也返回这个值。 |
| pathname | "/wileyCDA/"          | 返回URL中的目录和（或）文件名                                |
| port     | "8080"                | 返回URL中指定的端口号。如果URL中不包含端口号，则这个属性返回空字符串。 |
| protocol | "http:"               | 返回页面使用的协议。通常是http:或https:                      |
| search   | "?q=javascript"       | 返回URL的查询字符串。这个字符串以问好开头                    |

### 查询字符串参数

```javascript
function getQueryStringArgs() {
    // 取得查询字符串并去掉开头的问号
    var qs = (location.search.length > 0 ? location.search.substring(1) : ""),
        args = {},
        items = qs.length ? qs.split("&") : [],
        item = null,
        name = null,
        value = null,
        // 在for循环中使用
        i = 0,
        len = items.length;
    for (i = 0; i < len; i++) {
        item = items[i].split("=");
        name = decodeURIComponent(item[0]);
    	value = decodeURIComponent(item[1]);
        if (name.length) {
            args[name]  = value;
        }
    }
    return args;
}
var args = getQueryStringArgs();
console.log(args["q"]);"javascript"
console.log(args["num"]);//"10"
```

### 位置操作

#### assign方法

```javascript
// 立即打开新URL并在浏览器的历史记录中生成一条记录
location.assign('http://www.wrox.com');
```

以下代码与显式调用assign()方法的效果完全一样。

```javascript
window.location = 'http://www.wrox.com';
location.href = 'http://www.wrox.com';
```

#### 设置属性

```javascript
//将URL修改为"http://www.wrox.com/WileyCDA/#section1"
location.hash = "#section1";
//将URL修改为"http://www.wrox.com/WileyCDA/?q=javascript"
location.search = "?q=javascript";
//将URL修改为"http://www.yahoo.com/WileyCDA/"
location.hostname = "www.yahoo.com";
//将URL修改为"http://www.yahoo.com/mydir"
location.pathname = "mydir";
//将URL修改为"http://www.yahoo.com:8080/WileyCDA/"
location.port = 8080;
```

每次修改location的属性（hash除外），页面都会以新URL重新加载。

#### 只修改地址不留下历史记录

##### replace方法

> replace()：只接受一个参数，即要导航到的URL。

*调用replace()方法之后，用户不回到前一个页面）*

#### 重新加载当前页面

##### reload方法

> reload()：
>
> 1. 不传递任何参数，页面就会以最有效的方式重新加载（从缓存或服务器）。
> 2. 传递参数true，强制从服务器重新加载。

## navigator对象

### 属性

| 属性或方法                | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| appCodeName               | 浏览器的名称。通常是Mozilla，即使在非Mozilla浏览器中也如此。 |
| appMinorVersion           | 次版本信息                                                   |
| appName                   | 完整的浏览器名称                                             |
| appVersion                | 浏览器的版本。一般不与实际的浏览器版本对应                   |
| buildID                   | 浏览器编译版本                                               |
| cookieEnabled             | 表示cookie是否启用                                           |
| cpuClass                  | 表示客户端计算机中使用的CPU类型（x86、68K、Alpha、PPC或Other） |
| javaEnabled               | 表示浏览器中是否启用了Java                                   |
| language                  | 浏览器的主语言                                               |
| mimeTypes                 | 在浏览器中注册的MIME类型数组                                 |
| onLine                    | 表示浏览器是否连接到了因特网                                 |
| opsProfile                | 似乎早就不用了。查不到相关文档                               |
| oscpu                     | 客户端计算机的操作系统或使用的CPU                            |
| Platform                  | 浏览器所在的系统平台                                         |
| plugins                   | 浏览器中安装的插件信息的数组                                 |
| preference()              | 设置用户的首选项                                             |
| product                   | 产品名称（如Gecko）                                          |
| productSub                | 关于产品的次要信息（如Gecko的版本）                          |
| registerContentHandler()  | 针对特定的MIME类型将一个站点注册为处理程序                   |
| registerProtocolHandler() | 针对特定的协议将一个站点注册为处理程序                       |
| securityPolicy            | 已经废弃。安全策略的名称。为了与Netscape Navigatior 4向后兼容而保留了下来 |
| systemLanguage            | 操作系统的语言                                               |
| taintEnabled()            | 已经废弃。表示是否允许变量被修改（taint）。为了与Netscape Navigator 3向后兼容而保留了下来 |
| userAgent                 | 溜啦你的用户代理字符串                                       |
| userLanguage              | 操作系统的默认语言                                           |
| userProfile               | 借以访问用户个人信息的对象                                   |
| vendor                    | 浏览器的品牌                                                 |
| vendorSub                 | 有关供应商的次要信息                                         |

### 检测插件

> 对于非IE浏览器，可以使用plugins数组来达到这个目的：
>
> - name：插件的名字
> - description：插件的描述
> - filename：插件的文件名
> - length：插件所处理的MIME类型数组

```javascript
//检测插件（在IE中无效）
function hasPlugin(name) {
    name = name.toLowerCase();
    for (var i = 0; i < navigator.plugins.length; i++) {
        if (navigator.plugins[i].name.toLowerCase().indexOf(name) > -1) {
            return true;
        }
    }
    return false;
}
// 检测Flash
console.log(hasPlugin("Flash"));
// 检测QuickTime
console.log(hasPlugin("QuickTime"));
```

#### 检测IE中的插件

```javascript
function hasIEPlugin(name) {
    try {
        new ActiveXObject(name);
        return true;
    } catch (ex) {
        return false;
    }
}
// 检测Flash
console.log(hasIEPlugin("ShockwaveFlash.ShockwaveFlash"));
// 检测QuickTime
console.log(hasIEPlugin("QuickTime.QuickTime"));
```

#### 检测所有浏览器中的插件

```javascript
// Flash
function hasFlash() {
    var result = hasPlugin("Flash");
    if (!result) {
        result = hasIEPlugin("ShockwaveFlash.ShockwaveFlash");
    }
    return result;
}
// QuickTime
function hasQuickTime() {
    var result = hasPlugin("QuickTime");
    if (!result) {
        result = hasIEPlugin("QuickTime.QuickTime");
    }
    return result;
}
```

### 注册处理程序

> registerContentHandler：让一个站点指明它可以处理特定类型的信息。
>
> 1. 要处理的MIME类型
> 2. 可以处理该MIME类型的页面的URL
> 3. 应用程序的名称

```javascript
navigator.registerContentHandler("application/rss+xml", "http://www.somereader.com?feed=%s", "Some Reader");
```

> registerProtocolHandler：让一个站点指明它可以处理特定类型的信息。
>
> 1. 要处理的协议（如mailto或ftp）
> 2. 处理该协议的页面的URL
> 3. 应用程序的名称

```javascript
navigator.registerProtocolHandler("mailto", "http://www.somemailclient.com?cmd=%s", "Some Mail Client");
```

## screen对象

> screen对象包含浏览器窗口外部的显示器信息，如像素宽度和高度。

```javascript
window.resizeTo(screen.availWidth, screen.availHeight);
```

## history对象

### go方法

> go()：接收一个参数，表示向后或向前跳转的页面属的一个整数值。
>
> - 负数：表示向后跳转
> - 正数：表示向前跳转

```javascript
// 后退一页
history.go(-1);
// 前进一页
hostory.go(1);
// 前进两页
history.go(2);
```

> go()：接收一个字符串参数，浏览器会跳转到历史记录中包含该字符串的第一个位置，如果历史记录中不包含这个字符串，那么这个方法什么也不做。

```javascript
// 跳转到最近的wrox.com页面
history.go("wrox.com");
// 跳转到最近的nczonline.net页面
history.go("nczonline.net");
```

### back()和forward()

```javascript
// 后退一页
history.back();
// 前进一页
history.forward();
```

### length属性

> length属性保存着历史记录的数量

```javascript
if (history.length == 0) {
    // 这应该是用户打开窗口后的第一个页面
}
```

# DOM

> DOM：文档对象类型

## 文档元素

> 文档节点是每个文档的根节点。

- HTML元素通过元素结点表示
- 特性（attribute）通过特性节点表示
- 文档类型通过文档类型结点表示
- 注释通过注释结点表示。

```javascript
// 共12种结点
Document
|——Element html
	|——Element head
	|	|——Element title
	|		|——Text Sample Page
	|——Element body
		|——Element p
        	|——Text Hello World!
```



## Node类型

> - DOM1级定义了一个Node接口，该接口将由DOM种的所有节点类型实现。
> - JavaScript种所有节点类型都继承自Node类型，所有节点类型都共享着相同的基本类型和方法。

每个节点都有一个NodeType属性，用于表明节点的类型。由以下12个数值常量来表示，任何节点类型必居其一：

- Node.ELEMENT_NODE（值为）
- Node.ATTRIBUTE_NODE（值为2）
- Node.TEXT_NODE（值为3）
- Node.CDATA_SECTION_NODE（值为4）
- Node.ENTITY_REFERENCE_NODE（值为5）
- Node.ENTITY_NODE（值为6）
- Node.PROCESSING_INSTRUCTION_NODE（值为7）
- Node.COMMENT_NODE（值为8）
- Node.DOCUMENT_NODE（值为9）
- Node.DOCUMENT_TYPE_NODE（值为10）
- Node.DOCUMENT_FRAGMENT_NODE（值为11）
- Node.NOTATION_NODE（值为12）



### nodeName和nodeValue属性

> nodeName：元素的标签名
>
> nodeValue：对于元素节点，nodeValue的值始终是null。



### childNodes属性

> 每个节点都有一个childNodes属性，其中保存着一个NodeList对象。
>
> - NodeList是一种类数组对象，用于保存一组有序的节点，可以通过下标来访问这些节点。
> - NodeList基于DOM结构动态执行查询的结果。
> - childNodes列表中每个节点之间都是同胞节点。

```javascript
var firstChild = someNode.childNodes[0];
var secondChild = someNode.childNodes.item(1);
var count = someNode.childNodes.length;
```



#### 将NodeList转为数组

```javascript
// IE8之前无效，其它浏览器都可以运行
var arrayOfNodes = Array.prototype.slice.call(someNode.childNodes, 0);
// 以下代码在所有浏览器中都可以运行
function convertToArray(nodes) {
    var array = null;
    try {
        array = Array.prototype.slice.call(nodes, 0);
    } catch (ex) {
        array = new Array();
        // 手动枚举所有成员
        for (var i = 0, len = nodes.length; i < len; i++) {
            array.push(nodes[i]);
        }
    }
    return array;
}
```



#### previousSibling和nextSibling属性

> 通过使用列表中每个节点的previousSibling和nextSibling属性，可以访问同一列表中的其他节点。
>
> - 列表中第一个节点的previousSibling属性值为null
> - 列表中最后一个节点的nextSibling属性的值同样也为null。

```javascript

```



#### firstChild和lastChild属性

> 父节点的firstChild：指向childNodes列表中第一个节点
>
> 父节点的lastChild：指向childNodes列表中最后一个节点



#### hasChildNodes()方法

> hashChildNodes()：这个方法在节点包含一或多个子节点的情况下返回true



#### ownerDocument属性

> ownerDocument：指向表示整个文档的文档节点。



###parentNode属性

> 指向文档数中的父节点，包含在childNodes列表中的所有节点都有相同的父节点。



### 操作节点

**注：以下所有方法必须在支持子节点的节点上调用** 

#### appendChild()方法

> appendChild()：用于向childNodes列表的末尾添加一个节点

```javascript
var returnedNode = someNode.appendChild(newNode);
console.log(returnedNode == newNode);//true
console.log(someNode.lastChild == newNode);//true
```

> 如果传入到appendChild()中的节点已经是文档的一部分，那结果就是将该节点从原来的位置转移到新位置。

```javascript
// someNode有多个子节点
var returnedNode = someNode.appendChild(someNode.firstChild);
console.log(returnedNode == someNode.firstChild);//false
console.log(returnedNode == someNode.lastChild);//true
```

#### insertBefore()方法

> insertBefore()：将节点放在childNodes列表中某个特定的位置上，而不是放在末尾。
>
> 接收2个参数：
>
> 1. 要插入的节点
> 2. 参照的节点

```javascript
// 插入后成为最后一个子节点
returnedNode = someNode.insertBefore(newNode, null);
console.log(newNode == someNode.lastChild);//true

// 插入后成为第一个子节点
var returnedNode = someNode.insertBefore(newNode, someNode.firstChild);
console.log(returnedNode == newNode);//true
console.log(newNode == someNode.firstChild);//true

// 插入到最后一个子节点前面
returnedNode = someNode.insertBefore(newNode, someNode.lastChild);
console.log(newNode == someNode.childNodes[someNode.childNodes.length-2]);//true
```



#### replaceChild()方法

> replace()：接收两个参数
>
> 1. 要插入的节点
> 2. 要替换的节点
>
> 要替换的节点将由这个方法返回并从文档树中被移除，同时由要插入的节点占据其位置。

```javascript
// 替换第一个子节点
var returnedNode = someNode.replaceChild(newNode, someNode.firstChild);

// 替换最后一个子节点
returnedNode = someNode.replaceChild(newNode, someNode.lastChild);
```



#### removeChild()方法

> removeChild()：接收一个参数（要移除的节点），并返回被移除的节点
>
> *移除的节点仍然为文档所有，但文档中已经没有这个节点了* 

```javascript
// 移除第一个子节点
var formerFirstChild = someNode.removeChild(someNode.firstChild);

// 移除最后一个子节点
var formerLastChild = someNode.removeChild(someNode.lastChild);
```



#### cloneNode()方法

> cloneNode()：接受一个布尔值参数，表示是否执行深复制。
>
> （不会复制添加到DOM节点中的JavaScript属性，例如事件处理程序等。这个方法只复制特性、子节点。但IE会复制事件处理程序）
>
> - true：执行深复制，即复制节点及其整个子节点树
> - false：执行浅复制，即只复制节点本身。复制后的节点副本属于文档所有，但并没有为它指定父节点。

```javascript
<ul>
    <li>item 1</li>
	<li>item 2</li>
	<li>item 3</li>
</ul>
// myList是<ul>元素的引用
var deepList = myList.cloneNode(true);//deepList将<ul>的子节点也复制了
console.log(deepList.childNodes.length);//3 (IE < 9) 或 7

var shallowList = myList.cloneNode(false);//shallowList只复制了<ul>节点本身
console.log(shallowList.childNodes.length);//0
```

### Document类型

> - document对象是HTMLDocument（继承自Document类型）的一个实例，表示整个HTML页面
> - document对象是window对象的一个属性
>
> 特征：
>
> 1. nodeType的值是9
> 2. nodeName的值是"#document";
> 3. nodeValue、parentNode、ownerDocument的值都是null
> 4. 子节点可能是一个DocumentType（最多一个）、Element（最多一个）、ProcessingInstruction或Comment。

#### 文档的子节点

> DOM内置两个访问Document节点的子节点的快捷方式：
>
> 1. documentElement属性（始终指向HTML页面中的\<html\>元素）
> 2. childNodes列表访问文档元素

```javascript
var html = document.documentElement;//<html>的引用
console.log(html === document.childNodes[0]);//true
console.log(html === document.firstChild);//true
```

> document对象还有一个body属性，直接指向\<body\>元素

```javascript
var body = document.body;
```

> Document另一个节点是DocumentType，可通过doctype属性来访问它的信息。

```javascript
var doctype = document.doctype;//取得对<!DOCTYPE>的引用
```

#### 文档信息

> document对象还有一些标准的Document对象所没有的属性
>
> 1. title（可读写）
> 2. URL（只可读）
> 3. domain（可读写）：不能修改为URL中不包含的域，如果URL为`p2p.wrox.com`或`www.wrox.com`，则只能将domain设置为`wrox.com` 
>    - 如果域名一开始是松散的（如`wrox.com`），则不能再将它设置为紧绷的（如`p2p.wrox.com`
>    - 如果域名一开始是紧绷的（如`p2p.wrox.com`），可以将它设置为松散的（如`wrox.com`）
> 4. referrer（只可读）：链接到当前网页的来源网页的URL（可能为空字符串）

```javascript
var originalTitle = document.title;
document.title = "New page tilte";

var url = document.URL;//	"http://www.wrox.com/WilleyCDA/"
var domain = document.domain;//		"www.wrox.com"
var referrer = document.referrer;
```

#### 查找元素

##### getElementById()方法

> getElementById()：接收一个参数（要取得的元素的ID），如果不存在则返回null

```html
<div id = "myDiv"></div>
```

```javascript
var div = document.getElementById("myDiv");//取得<div>元素的引用
```

##### IE8及更低的BUG

> IE8及较低版本不区分ID的大小写

##### IE7及较低版本的怪癖

> name特性与给定ID匹配的表单元素（\<input\>、\<textarea\>、\<button\> 及\<select\> ），如果该表单元素在文档中带有该ID的元素之前，会先被getElementById返回表单元素。

#### getElementByTagName()方法

> getElementByTagName()：接受一个参数，即要取得元素的标签名，返回的是包含零或多个元素的NodeList。在HTML文档中，这个方法会返回一个HTMLCollection对象。

```javascript
var images = document.getElementsByTagName("img");
console.log(images.length);
console.log(images[0].src);
console.log(images.item(0).src);
```

##### nameItem()方法

> HTMLCollection对象还有一个namedItem()方法，可以通过元素的name特性取得集合中的项。

```html
<img src="myimage.gif" name="myImage">
```

```javascript
var images = document.getElementsByTagName("img");
var myImage = images.namedItem("myImage");
var myImage = images["myImage"];
// 取得所有元素
var allElements = document.getElementsByTagName("*");
```

#### getElementsByName()方法

> getElementsByName()：只有HTMLDocument类型才有的方法，这个方法返回带有给定name特性的所有元素
>
> **最常用于取得单选按钮，因为单选按钮必须具有相同的name特性**

```html
<ul>
    <li><input type="radio" value="red" name="color" id="colorRed"></li>
    <li><input type="radio" value="green" name="color" id="colorGreen"></li>
    <li><input type="radio" value="blue" name="color" id="colorBlue"></li>
</ul>
```

```javascript
var radios = document.getElementsByName("color");
```

#### 特殊集合

> 以下集合都是HTMLCollection对象
>
> - document.anchors：包含文档中所有带name特性的\<a\>元素
> - document.applets：包含文档中所有\<applet\>元素（不建议使用）
> - document.forms：包含文档中所有\<form\>元素，与document.getElementsByTagName("form")得到的结果相同
> - document.images：包含文档中所有的\<img\>元素
> - document.links：包含文档中所有带href特性的\<a\>元素。

#### DOM一致性检测

> document.implementation：为检测浏览器实现了DOM的哪些部分提供了相应信息和功能的对象

##### hasFeature()方法

> hasFeature()：接受两个参数
>
> 1. 要检测的DOM功能的名称
> 2. 版本号

| 功能  | 版本号        | 说明                                            |
| ----- | ------------- | ----------------------------------------------- |
| Core  | 1.0、2.0、3.0 | 基本的DOM，用于描述表现文档的节点树             |
| XML   | 1.0、2.0、3.0 | Core的XML扩展，添加了对CDATA、处理指令及实体    |
| HTML  | 1.0、2.0      | XML的HTML扩展，添加了对HTML特有元素及实体的支持 |
| Views | 2.0           |                                                 |
|       |               |                                                 |
|       |               |                                                 |
|       |               |                                                 |
|       |               |                                                 |
|       |               |                                                 |




