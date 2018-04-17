---
layout: post
title: JavaScript高级程序设计笔记2
date: 2018-04-17 16:55:00+00:00
categories: 日志
tags: 博客
---

# 事件

## DOM事件流

”DOM2级事件“事件流包括：

- 事件捕获阶段：最先发生，从document到实际的目标元素的父元素为止

> 捕获阶段单击\<div\>元素以下列顺序触发click事件：
>
> 1. document
> 2. \<html\>
> 3. \<body\>
> 4. \<div\>

- 处于目标阶段：发生在实际的目标元素（如本例中的`<div>` 元素）
- 冒泡阶段：

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Event bubbling example</title>
    </head>
    <body>
        <div id="myDiv">
            Click Me
        </div>
    </body>
</html>
```

> click事件传播顺序：
>
> 1. \<div\>
> 2. \<body\>
> 3. \<html\>
> 4. document

## 事件

> 事件：用户或浏览器自身执行的某种动作，如click、load、mouseover

## 事件处理程序

> 事件处理程序：响应某个事件的函数，又叫事件侦听器。事件处理程序的名字以"on"开头，如`onclick`。

### onclick

> - 事件处理程序中的代码在执行时，有权访问全局作用域中的任何代码。
> - 这样指定事件处理器有独到之处：首先会创建一个封装着元素属性值的函数，这儿函数中有一个局部变量event，即**事件对象**。

```html
<script type='text/javascript'>
    function showMessage() {
        console.log("Hello world!");
    }
</script>
<input type='button' value='Click Me' onclick='showMessage()' />
```

扩展作用域：

```javascript
// 在html中
<input type='button' value='click me' onclick('doSomething()');
// 在函数定义块中
function doSomething() {
    with (document) {
        with (this) {
            // 元素属性值
        }
    }
}
```

### 缺点

> 通过HTML指定事件处理程序的缺点是
>
> 1. HTML与JavaScript代码紧密耦合，一旦改动，HTML代码和JavaScript代码都要改动
> 2. 作用域链在不同浏览器中可能会导致不同结果

## DOM0级事件处理程序

> 使用JavaScript指定事件处理程序，先取得一个要操作的对象的引用。
>
> 1. 每个元素都有自己的事件处理程序属性，这些属性**全部小写** ，如`onclick` 
> 2. 给这种事件处理程序属性设置一个函数，就指定了事件处理程序
>
> 作用域：
>
> 1. DOM0级事件处理程序会在所属元素的作用域内运行

```javascript
var btn = document.getElementById('myBtn');
btn.onclick = function () {
    console.log(this.id);//"myBtn"
};
btn.onclick = null;//删除事件处理程序
```

**注：如果代码在页面中位于按钮后面，很可能在一段时间内无法指定事件处理程序。只有当代码运行之后，才会为这些元素添加事件处理程序。** 



## DOM2级事件处理程序

> **“DOM2级事件”定义了两个方法**
>
> 1. addEventListener()：指定事件处理程序
> 2. removeEventListener()：删除事件处理程序
>
> **都接受3个参数**：
>
> 1. 要处理的事件名
> 2. 作为事件处理程序的函数
> 3. 一个布尔值：如果是true，表示在捕获阶段调用事件处理程序；如果是false，表示在冒泡阶段调用事件处理程序。不写时默认为false。
>
> **好处**
>
> 1. 可以添加多个事件处理程序
> 2. 多个事件处理程序按照添加它们的顺序触发
>
> **注意**
>
> 1. 通过addEventListener()添加的匿名函数将无法移除。
>
> **兼容性**
>
> IE9、Firefox、safari、chrome、opera支持DOM2级事件处理程序。

```javascript
var btn = document.getElementById('myBtn');
btn.addEventListener('click', function () {
    console.log(this.id);
}, false);
```

## IE事件处理程序

> IE实现了与DOM中类似的两个方法：
>
> 1. attachEvent()：添加
> 2. detachEvent()
>
> 接受两个参数：
>
> 1. 事件处理程序名称（如`onclick`）
> 2. 事件处理程序函数（匿名函数无法移除）
>
> 注意：
>
> 1. 只支持事件冒泡阶段（添加的事件处理程序都会被添加到冒泡阶段）
> 2. 可以添加多个事件处理程序，但执行顺序与添加顺序相反（后添加的事件处理程序先执行）
>
> 作用域：
>
> 1. 事件处理程序会在全局作用域中运行，因此this等于window。

```javascript
var btn = document.getElementById('myBtn');
btn.attachEvent('onclick', function () {//注意事件名与DOM2级的区别
    console.log('hello world!');
    console.log(this === window);//true
});
btn.attachEvent('onclick', function () {
    console.log('clicked');
});
// 先看到clicked，再看到hello world!
```

# 事件对象

> 在触发DOM上某个事件时，会产生一个事件对象event，其包含有：
>
> 1. 导致事件的元素
> 2. 事件的类型
> 3. 特定事件相关的信息

## DOM中的事件对象

> 兼容DOM的浏览器都会将一个event对象传入到事件处理程序中

```javascript
var btn = document.getElementById('myBtn');
btn.onclick = function (event) {
    console.log(event.type);//'click'
};
btn.addEventListener('click', function (event) {
    console.log(event.type);//'click'
}, false);
```

所有事件都会有的成员

| 属性/方法                  | 类型         | 读/写 | 说明                                                         |
| -------------------------- | ------------ | ----- | ------------------------------------------------------------ |
| bubbles                    | Boolean      | 只读  | 表明事件是否冒泡                                             |
| cancelable                 | Boolean      | 只读  | 表明是否可以取消事件的默认行为                               |
| currentTarget              | Element      | 只读  | 事件处理程序当前正在处理事件的那个元素                       |
| defaultPrevented           | Boolean      | 只读  | 为true表示已经调用了preventDefault()（DOM3级事件中新增）     |
| detail                     | Integer      | 只读  | 与事件相关的细节信息                                         |
| eventPhase                 | Integer      | 只读  | 调用事件处理程序的阶段：1表示捕获阶段，2表示“处于目标”，3表示冒泡阶段 |
| preventDefault()           | Function     | 只读  | 取消事件的默认行为。如果cancelable是true，则可以使用这个方法 |
| stopImmediatePropagation() | Function     | 只读  | 取消事件的进一步捕获或冒泡，同时阻止任何事件处理程序被调用（DOM3级事件新增） |
| stopPropagation()          | Function     | 只读  | 取消事件的进一步捕获或冒泡。如果bubbles为true，则可以使用这个方法 |
| target                     | Element      | 只读  | 事件的目标                                                   |
| trusted                    | Boolean      | 只读  | 为true表示事件是由浏览器生成的，为false表示事件是开发人员通过JavaScript创建的（DOM3级事件中新增） |
| type                       | String       | 只读  | 被触发的事件的类型                                           |
| view                       | AbstractView | 只读  | 与事件关联的抽象视图。等同于发生事件的window对象。           |

```javascript
var btn = document.getElementById('myBtn');
btn.onclick = function(event) {
    console.log(event.currentTarget === this);//true
    console.log(event.target === this);//true
}
document.body.onclick = function (event) {
    console.log(event.currentTarget === document.body);//true
    console.log(this === document.body);//true
    // 由于click事件真正的目标是按钮，但按钮上没有注册click事件处理程序，所以冒泡到body处才触发处理程序，因此target是button，而currentTarget是body
    console.log(event.target === document.getElementById('myBtn'));//true
}
```

### 处理多个事件

> 需要通过一个函数处理多个事件时，可以使用`type`属性

```javascript
var btn = document.getElementById('myBtn');
var handler = function (event) {
    switch (event.type) {
        case 'click':
            console.log('clicked');
            break;
        case 'mouseover':
            event.target.style.backgroundColor = 'red';
            break;
        case 'mouseout':
            event.target.style.backgroundColor = "";
            break;
    }
};
btn.onclick = handler;
btn.onmouseover = handler;
btn.onmouseout = handler;
```

### 阻止特定事件的默认行为

> preventDefault()：阻止默认行为。只有cancelable属性设置为true的事件，才可以使用preventDefault()来取消其默认行为。

```javascript
var link = document.getElementById("myLink");
link.onclick = function (event) {
    event.preventDefault();// 取消了链接被单击时会导航到其href特性指定的URL这个默认行为
};
```

### 停止事件在DOM层次中的传播

> 即取消进一步的事件捕获或冒泡。

直接添加到一个按钮的事件处理程序可以调用stopPropagation()，从而避免触发注册在document.body上面的事件处理程序。

```javascript
var btn = document.getElementById("myBtn");
btn.onclick = function (event) {
    console.log('Clicked');
    event.stopPropagation();
};
document.body.onclick = function (event) {
    console.log('Body clicked');
};
// 点击按钮时，只会在控制台打印'Clicked'
```

### 确定事件当前处于事件流的阶段

> eventPhase属性：1、2、3其中一个值
>
> 1. 如果是捕获阶段调用的事件处理程序，eventPhase等于1
> 2. 如果事件处理程序处于目标对象上，则eventPhase等于2
> 3. 如果在冒泡阶段调用的事件处理程序，eventPhase等于3

```javascript
var btn = document.getElementById('myBtn');
btn.onclick = function (event) {
    console.log(event.eventPhase);//2
};
document.body.addEventListener('click', function (event) {
    console.log(event.eventPhase);//1
}, true);//true表示捕获阶段
document.body.onclick = function (event) {
    console.log(event.eventPhase);//3
};
```

## IE中的事件对象

```javascript
var btn = document.getElementById('myBtn');
btn.onclick = function () {
    var event = window.event;
    console.log(event.type);//'click'
};
// IE
btn.attachEvent('onclick', function (event) {
    console.log(event.type);//'click'
});
```

| 属性/方法    | 类型    | 读/写 | 说明                                                         |
| ------------ | ------- | ----- | ------------------------------------------------------------ |
| cancelBubble | Boolean | 读/写 | 默认值为false，但将其设置为true就可以取消事件冒泡（同stopPropagation方法） |
| returnValue  | Boolean | 读/写 | 默认值为true，但将其设置为false就可以取消事件的默认行为（同preventDefault方法） |
| srcElement   | Element | 只读  | 事件的目标（与DOM中的target属性相同）                        |
| type         | String  | 只读  | 被触发的事件的类型                                           |

```javascript
var link = document.getElementById('myLink');
link.onclick = function () {
    window.event.returnValue = false;
};
```







## 跨浏览器的事件对象

```javascript
var EventUtil = {
    addHandler : function (element, type, handler) {
        // 省略的代码
    }，
    getEvent : function (event) {
    	return event ? event : window.event;
	},
    getTarget : function (event) {
        return event.target || event.srcElement;
    },
    preventDefault : function (event) {
        if (event.preventDefault) {
            event.preventDefault();
        } else {
            event.returnValue = false;
        }
    },
    removeHandler : function (event, type, handler) {
    	// 省略的代码        
    },
    stopPropagation : function (event) {
        if (event.stopPropagation) {
            event.stopPropagation();
        } else {
            event.cancelBubble = true;
        }
    }
};
```



# 事件类型

## DOM3级事件

> - UI（User Interface，用户界面）事件，当用户与页面上的元素交互时触发
> - 焦点事件，当元素获得或失去交点时触发
> - 鼠标事件，当用户通过鼠标在页面上执行操作时触发
> - 滚轮事件，当使用鼠标滚轮时触发
> - 文本事件，当在文档中输入文本时触发
> - 键盘事件，当用户通过键盘在页面上执行操作时触发
> - 合成事件，当为IME（Input Method Editor，输入法编辑器）输入字符时触发
> - 变动（mutation）事件，当底层DOM结构发生变化时触发
> - 变动名称事件，当底层DOM结构发生变化时触发
> - 变动名称事件，当元素或属性名变动时触发。（已废弃）

### UI事件

> - DOMActivate：表示元素已经被用户操作（通过鼠标或键盘）激活。（已废弃）
> - load：当页面完全加载后在window上面触发，当所有框架都加载完毕时在框架集上面触发，当图像加载完毕时在\<img\>元素上面触发，或者当嵌入的内容加载完毕时在\<object\>元素上面触发
> - unload：当页面完全卸载后再window上面触发，当所有框架都卸载后在框架集上面触发，或者当嵌入的内容卸载完毕后在\<object\>元素上面触发
> - abort：在用户停止下载过程时，如果嵌入的内容没有加载完，则在\<object\>元素上面触发。
> - error：当发生JavaScript错误时在window上面触发，当无法加载图像时在\<img\>元素上面触发，当无法加载嵌入内容时在\<object\>元素上面触发，或者当有一或多个框架无法加载时在框架集上面触发
> - select：当用户选择文本框（\<input\>或\<textarea\>）中的一或多个字符时触发。
> - resize：当窗口或框架的大小变化时在window或框架上面触发。
> - scroll：当用户滚动带滚动条的内容时，在该元素上面触发。\<body\>元素中包含所加载的滚动条。

#### load事件

> - 当页面完全加载后（包括所有图像、JavaScript文件、CSS文件等外部资源），就会触发window上面的load事件。
> - 对于其他元素，则是特定元素加载完毕后会触发其load事件。

```javascript
// load事件
EventUtil.addHandler(window, 'load', function (event) {
    // 加载一个script标签
    var script = document.createElement("script");
    EventUtil.addHandler(script, 'load', function (event) {
        console.log('loaded');
    });
    script.src = 'example.js';
    document.body.appendChild(script);
    // 加载一个link标签
    var link = document.createElement('link');
    link.type = 'text/css';
    link.rel = 'stylesheet';
    EventUtil.addHandler(link, 'load', function (event) {
        console.log('css loaded');
    });
    link.href = 'example.css';
    document.getElementByTagName('head')[0].appendChild(link);
});
```

#### unload事件

> 用户从一个页面切换到另一个页面，就会发生unload事件

```javascript
// unload事件
EventUtil.addHandler(window, 'unload', function (event) {
    console.log('unloaded');
});
```

#### resize事件

> 当浏览器窗口被调整到一个新的高度或宽度时，就会触发resize事件。

#### scroll事件

> scroll事件是在window对象上发生的，但它实际表示的则是页面中相应元素的变化。
>
> - 在混杂模式下，可以通过\<body\>元素的scrollLeft和scrollTop来监控到这一变化
> - 在标准模式下，除safari之外的浏览器都会通过\<html\>来反映这一变化

```javascript
EventUtil.addHandler(window, 'scroll', function (event) {
    if (document.compatMod == 'CSS1Compat') {
        console.log(document.documentElement.scrollTop);
    } else {
        console.log(document.body.scrollTop);
    }
})
```



####  焦点事件

> 焦点事件会在页面获得或失去交点时触发，利用这些事件并与document.hasFocus()方法以及document.activeElement属性配合，可以知晓用户在页面上的行踪。有以下6个焦点事件：
>
> - blur：在元素失去焦点时触发。这个事件不会冒泡。
> - DOMFocusIn：在元素获得焦点时触发。这个事件冒泡。
> - DOMFocusOut：在元素失去焦点时触发
> - focus：在元素获得焦点时触发。这个事件不会冒泡。
> - focusin：在元素获得焦点时触发。这个事件冒泡
> - focusout：在元素失去焦点时触发。

**当焦点从页面中的一个元素移动到另一个元素，会依次触发下列事件：**

1. focusout：在失去焦点的元素上触发
2. focusin：在获得焦点的元素上触发
3. blur：在失去焦点的元素上触发
4. DOMFocusOut：在失去焦点的元素上触发
5. focus：在获得焦点的元素上触发
6. DOMFocusIn：在获得焦点的元素上触发



#### 鼠标与滚轮事件

> DOM3级事件中定义了9个鼠标事件：
>
> - click：用户单击主鼠标按钮或者按下回车键时触发
> - dbclick：在用户双击主鼠标按钮时触发
> - mousedown：在用户按下任意鼠标按钮时触发
> - mouseenter：在鼠标光标从元素外部首次移动到元素范围之内时触发。
> - mouseleave：在位于元素上方的鼠标光标移动到元素范围之外时触发。这个事件不冒泡，而且在光标移动到后代元素上不会触发。
> - mousemove：当鼠标指针在元素内部移动时重复地触发。
> - mouseout：在鼠标指针位于一个元素上方，然后用户将其移入另一个元素时触发。
> - mouseover：在鼠标指针位于一个元素外部，然后用户将其首次移入另一个元素边界之内时触发。不能通过键盘触发这个事件。
> - mouseup：在用户释放鼠标按钮时触发。不能通过键盘触发这个事件。



#### 键盘与文本事件

> 有3个键盘事件：
>
> - keydown：当用户按下键盘上的任意键时触发，按住不放时，会重复触发此事件。
> - keypress：当用户按下键盘上的字符键时触发，按住不放时，会重复触发此事件。按下Esc键也会触发这个事件。
> - keyup：当用户释放键盘上的键时触发。



# JSON

## 语法

> JSON语法可以表示以下三种类型的值
>
> - 简单值：字符串、数值、布尔值和null
> - 对象：有序的键值对，每对键值对中的值可以是简单值或复杂数据类型的值
> - 数组

## 解析与序列化

### JSON对象

> JSON对象有两个方法：stringify()和parse()

#### stringify()方法

```javascript
var book = {
    title : 'professional javascript',
    authors : ["Nicholas C. Zakas"],
    edition : 3,
    year : 2011
};
var jsonText = JSON.stringify(book);
// "{"title":"professional javascript","authors:["Nicholas C. Zakas"],"edition":3,"year":2011}"

```

##### 序列化选项

> JSON.stringify()接受3个参数：
>
> - 要序列化的JavaScript对象
> - 过滤器（一个数组或一个函数）
> - 一个选项（表示是否在JSON字符串中保留缩进）

```javascript
var book = {
    title : 'professional javascript',
    authors : ["Nicholas C. Zakas"],
    edition : 3,
    year : 2011
};

// 过滤器数组
var jsonText = JSON.stringify(book, ["title", "edition"]);//返回结果只包含title和edition

// 过滤器函数
var jsonText = JSON.stringify(book, function (key, value) {
    switch(key) {
        case "authors":
            return value.join(",");
        case "year":
            return 5000;
        case "edition":
            return undefined;
        default:
            return value;
    }
});

// 添加缩进选项(数字)
var jsonText = JSON.stringify(book, null, 4);
/*保存在jsonText中的字符串如下所示：
{
    "title" : 'professional javascript',
    "authors" : [
    	"Nicholas C. Zakas"
   	],
    "edition" : 3,
    "year" : 2011
}
*/

// 添加缩进选项(字符串)
var jsonText = JSON.stringify(book, null, "--");
/*保存在jsonText中的字符串如下所示：
{
--"title" : 'professional javascript',
-"-authors" : [
----"Nicholas C. Zakas"
--],
--"edition" : 3,
--"year" : 2011
}
*/
```



#### parse()方法

> 将JSON字符串直接传递给JSON.parse()可以得到相应的JavaScript值。
>
> 参数：
>
> 1. 序列化后的JSON字符串
> 2. 还原函数（reviver）

```javascript
var bookCopy = JSON.parse(jsonText);
// bookCopy和book具有相同的属性

// 带第二个参数
var bookCopy = JSON.parse(jsonText, function (key, value) {
    if (key == 'releaseDate') {
        return new Date(value);
    } else {
        return value;
    }
});
```

