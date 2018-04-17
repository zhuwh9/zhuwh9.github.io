---
layout: post
title: JavaScript高级程序设计笔记3
date: 2018-04-17 16:56:00+00:00
categories: 日志
tags: 博客
---

# AJAX

> Ajax技术的核心是XMLHttpRequest对象（简称XHR）

## XHR用法

### open()

> 接受3个参数：
>
> 1. 要发送的请求的类型（"get"、"post"等）
> 2. 请求的URL（相对于执行代码的当前网页或绝对路径）
> 3. 布尔值（表示是否异步发送请求）
>
> 注意：
>
> 1. 调用open()方法并不会真正发送请求，而只是启动一个请求以备发送。

```javascript
xhr.open("get","example.php",false);
```

### send()

> 接受一个参数：作为请求主体发送的数据，如果不通过请求主体发送数据，则必须传入null，调用send()之后，请求会被分派到服务器。

```javascript
xhr.open("get","example.php",false);
xhr.send(null);
```

### 收到相应

> 收到响应后，相应的数据会自动填充XHR对象的属性，如下所示
>
> - responseText：作为相应主体被返回的文本
> - responseXML：如果响应的内容类型是"text/xml"或"application/xml"，这个属性中保存包含着响应数据的XML DOM文档。
> - status：响应的HTTP状态
> - statusText：HTTP状态的说明
>
> 收到响应后的步骤：
>
> 1. 检查status属性（200为成功的标志，304表示请求的资源没有修改，可以直接使用浏览器缓存的版本）
> 2. 响应主体的内容会保存到responseText属性
> 3. 非XML数据，responseXML属性的值为null。

```javascript
xhr.open("get","example.txt",false);
xhr.send(null);

if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
    console.log(xhr.responseText);
} else {
    console.log("Request was unsuccessful: " + xhr.status);
}
```

### readyState

> XHR对象的readyStatus属性表示请求/响应过程的当前活动阶段。
>
> - 0：未初始化。尚未调用open()方法
> - 1：启动。已经调用open()方法，但尚未调用send()方法
> - 2：发送。已经调用send()方法，但尚未接收到响应。
> - 3：接收。已经接收到部分响应数据。
> - 4：完成。已经接收到全部响应数据，而且已经可以在客户端使用了。

### readystatechange事件

> 只要readyState属性的值由一个值变成另一个值，都会触发一次readystatechange事件。
>
> **注意：必须在调用open()之前指定onreadystatechange事件处理程序才能确保跨浏览器兼容性。**

```javascript
var xhr = createXHR();
xhr.onreadystatechange = function () {
    if (xhr.readyState == 4) {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
    		console.log(xhr.responseText);
		} else {
    		console.log("Request was unsuccessful: " + xhr.status);
		}
    }
}
xhr.open("get", "example.txt", true);//异步
xhr.send(null);
```

### abort()

> 在接收到响应之前可以调用abort()方法来取消异步请求。

```javascript
xhr.abort();
```

## HTTP头部信息

> 在发送XHR请求的同时，还会发送下列头部信息
>
> - Accept：浏览器能够处理的内容类型
> - Accept-Charset：浏览器能够显示的字符集
> - Accept-Encoding：浏览器能够处理的压缩编码
> - Accept-Language：浏览器当前设置的语言
> - Connection：浏览器与服务器之间连接的类型
> - Cookie：当前页面设置的任何Cookie
> - Host：发送请求的页面所在的域
> - Referer：发送请求的页面的URL
> - User-Agent：浏览器的用户代理字符串。

### setRequestHeader()

> 使用setRequestHeader()可以设置自定义的请求头部信息，接收两个参数：
>
> - 头部字段的名称
> - 头部字段的值
>
> **注意：必须在调用open()方法之后且调用send()方法之前调用setRequestHeader()；且最好不要修改默认的头部信息，用自定义的头部字段名称**

```javascript
var xhr = createXHR();
xhr.onreadystatechange = function () {
    if (xhr.readyState == 4) {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
    		console.log(xhr.responseText);
		} else {
    		console.log("Request was unsuccessful: " + xhr.status);
		}
    }
}
xhr.open("get", "example.php", true);//异步
xhr.setRequestHeader("MyHeader", "MyValue");
xhr.send(null);
```

### getResponseHeader()

> 参数：头部字段名称
>
> 返回值：响应头部信息

```javascript
var myHeader = xhr.getResponseHeader("MyHeader");
```

### getAllResponseHeaders()

> 获取一个包含所有头部信息的长字符串。

```javascript
var allHeaders = xhr.getAllResponseHeaders();
```

## GET请求

> 注意：
>
> - 查询字符串中每个参数的名称和值都必须使用encodeURIComponent()进行编码，然后放到URL的末尾
> - 所有名-值对都必须由和号（&）分隔

```javascript
xhr.open("get", "example.php?name1=value1&name2=value2", true);
```

```javascript
// 辅助向现有URL添加查询字符串参数
function addURLParam(url, name, value) {
    url += (url.indexOf('?') == -1 ? "?" : "&");
    url += encodeURIComponent(name) + "=" + encodeURIComponent(value);//必须编码
    return url;
}

var url = "example.php";

url = addURLParam(url, "name", "Nicholas");
url = addURLParam(url, "book", "Professional JavaScript");

xhr.open("get", url, false);
```

## POST请求

> 发送POST请求的步骤：
>
> 1. open()方法传入"post"
>
> 2. send()方法传入某些（序列化）数据。
>
>    - 模仿表单提交：将Content-Type头部设为application/x-www-form-urlencoded；并以适当的方式创建一个字符串
>
>
>    - POST数据的格式与查询字符串格式相同

```javascript
function submitData() {
    var xhr = createXHR();
    xhr.onreadystatechange = function () {
        if (xhr.readyState == 4) {
        	if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
    			console.log(xhr.responseText);
			} else {
    			console.log("Request was unsuccessful: " + xhr.status);
			}
    	}
    };
    xhr.open("post", "postexample.php", true);
    xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
    var form = document.getElementById("user-info");
    xhr.send(serialize(form));
}
```

## 超时

```javascript
xhr.timeout = 1000;//超时设置为1秒钟
xhr.ontimeout = function () {
    console.log("timeout");
};
xhr.send(null);
```

# 进度事件

> 6个进度事件：
>
> - loadstart：在接收到响应数据的第一个字节时触发
> - progress：在接收响应期间不断触发
> - error：在请求发生错误时触发
> - abort：因为调用abort()方法时触发
> - load：在接收到完整的响应数据时触发
> - loadend：在通信完成或者触发error、abort或load事件后触发

```javascript
xhr.onload = function (event) {
    // do something
};
xhr.onprogress = function (event) {
    // do something
};
xhr.open("get", "altevents.php", true);
xhr.send(null)
```

# 跨域资源共享（CORS）

> 跨域安全策略：XHR对象只能访问与包含它页面位于同一个域中的资源
>
> CORS：定义了在必须访问跨域资源时，浏览器与服务器该如何沟通。	
>
> -  核心思想：使用自定义的HTTP头部让浏览器与服务器进行沟通，从而决定请求或响应是否应该成功还是失败
> - 例子：GET或POST请求，没有自定义的头部，主体信息是text/plain。在发送请求时，需要给它附加一个额外的Origin头部，其中包含请求页面的源信息（协议、域名、端口）
>   - `Origin: http://www.nczonline.net`
>   - 如果服务器认为这个请求可以接收，在`Access-Control-Allow-Origin`头部中回发相同的源信息（公共信息可以回发`"*"`。如
>     - `Access-Control-Allow-Origin: http://www.nczonline.net`

## IE对CORS的实现

> 

```javascript
var xdr = new XDomainRequest();
xdr.onload = function () {
    console.log(xdr.responseText);
};
xdr.open("get", "http://www.baidu.com/page/");
xdr.send(null);
```

# Cookie

> HTTP Cookie，通常直接叫做cookie，最初是在客户端用于存储会话信息的。

- 服务器响应的头：

```html
HTTP/1.1 200 OK
Content-type: text/html
Set-Cookie: name=value
Other-header: other-header-value
```

这个HTTP响应设置以name为名称、以value为值的一个cookie

- HTTP请求的头

```html
GET /index.html HTTP/1.1
Cookie: name=value
Other-header: other-header-value
```

## 构成

> cookie由浏览器保存的以下几块信息构成
>
> - 名称：一个唯一确定cookie的名称（不区分大小写）
> - 值：存储在cookie中的字符串值。值必须被URL编码
> - 域：cookie对于哪个域是有效的
> - 路径：对于指定域中的那个路径，应该向服务器发送cookie。
> - 失效时间：表示cookie何时应该被删除的时间戳
> - 安全标志：指定后，cookie只有在使用SSL连接的时候才发送到服务器（只能发送给https而不能发送http（在Set-Cookie中指定secure）

```html
Set-Cookie: name=value; expires=Mon, 22-Jan-07 07:10:24 GMT; domain=.wrox.com; path=/; secure
```

## 设置cookie

> 基本的cookie操作有三种：读取、写入和删除



```javascript
var CookieUtil = {
    get : function (name) {
        var cookieName = encodeURIComponent(name) + "="；
        var cookieStart = document.cookie.indexOf(cookieName);
        var cookieValue = null;
        if (cookieStart > -1) {
            var cookieEnd = document.cookie.indexOf(";", cookieStart);
            if (cookieEnd == -1) {
                cookieEnd == document.cookie.length;
            }
            cookieValue = decodeURIComponent(document.cookie.substring(cookieStart + cookieName.length, cookieEnd));
        }
        return cookieValue;
    },
    set : function (name, value, expires, path, domain, secure) {
        var cookieText = encodeURIComponent(name) + "=" + encodeURIComponent(value);
        if (expires instanceof Date) {
            cookieText += "; expires=" + expires.toGMTString();
        }
        if (path) {
            cookieText += "; path=" + path;
        }
        if (domain) {
            cookieText += "; domain=" + domain;
        }
        if (secure) {
            cookieText += "; secure";
        }
        document.cookie = cookieText;
    },
    unset : function (name, path, domain, secure) {
        this.set(name, "", new Date(0), path, domain, secure);
    }
};

// 设置cookie
CookieUtil.set("name", "Nicholas");

// 读取cookie的值
console.log(CookieUtil.get("name"));//"Nicholas"

// 删除cookie
CookieUtil.unset("name");
```

## 子cookie



