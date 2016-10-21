---
date: 2016-10-19 10:25
status: public
title: Ajax技术
layout: post
---

在Ajax应用开发模式中通过javascript实现在不刷新整个页面的情况下，对部分数据进行更新。
Ajax是XMLHttpRequest对象和JavaScript、XML、CSS、DOM等多种技术的组合。

XMLHttpRequest对象：
Ajax使用的技术中，最核心的技术就是XMLHttpRequest，是具有应用程序接口的JavaScript对象，能够使用超文本传输协议(HTTP)连接一个服务器。

初始化XMLHttpRequest对象：

* IE浏览器
IE浏览器把XMLHttpRequest实例化为一个ActiveX对象

```js
var http_request = new ActiveXObject("Msxml2.XMLHTTP");
```

或者

```js
var http_request = new ActiveXObject("Microsoft.XMLHTTP");
```
        
* 非IE浏览器 

```js
var http_request = new XMLHttpRequest();
```
        
* 创建跨浏览器的XMLHttpRequest对象：

```js
var xmlhttp;
if (window.XMLHttpRequest) {// code for IE7+, Firefox, Chrome, Opera, Safari
    xmlhttp=new XMLHttpRequest();
} else {// code for IE6, IE5
    xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}
```

XMLHttpRequest对象的常用方法：

* open()方法：
用于设置进行异步请求目标的UrL、请求方法以及其他参数信息：

```js
open("method","URL",[请求方式(true/false),userName,password]);
```

* send()方法：
用于向服务器发送请求，如果请求声明为异步，该方法将立即返回，否则将等到接收到响应为止：

```js
send(content(可以是DOM对象的实例、输入流或字符串以及null));

```     
* setRequestHeader()方法：
用于为请求的HTTP头设置值：（必须在调用open()方法之后）

```js
setRequestHeader("header","value");
```

* abort()方法：
用于停止或放弃当前异步请求：

```js
abort();
```

* getResponseHeader()方法：
用于以字符串形式返回指定的HTTP头信息：

```js
getResponseHeader("headerLabel");
```

* getAllResponseHeaders()方法：
用于以字符串形式返回完整的HTTP头信息，其中包括Server、Date、Content-Type和Content-Length

```js
getAllResponseHeaders();
```

XMLHttpRequest对象的属性：

* onreadystatechange属性：
指定状态改变时所触发的事件处理器

* readyState属性：
用于获取请求的状态：

| 值    | 意义     |值     |意义    |
| :---: |  :---:  | :---: | :---: | 
| 0     | 未初始化 |3      |交互中  |
| 1     | 正在加载 |4      |完成    |
| 2     | 已加载   |       |       |

* responseText属性：
用于获取服务器的响应，表示为字符串

* responseXML属性：
用于获取服务器的响应，表示为XML。

* status属性：
用于返回服务器的HTTP状态码：

| 值   | 意义                     | 值  | 意义         |
| :---:| :---:                   |:---:| :---:       |
| 200  | 表示成功                 | 404 | 文件未找到    |
| 202  | 表示请求被接受，但尚未成功  | 500 | 内部服务器错误|
| 400  | 错误的请求               |      |             |

* statusText属性：
用于返回HTTP状态码对应的文本

与服务器通信——发送请求与处理响应
发送请求：（GET/POST）
（1）初始化XMLHttpRequest对象。

```js
var xmlhttp;
if (window.XMLHttpRequest){// code for IE7+, Firefox, Chrome, Opera, Safari
	xmlhttp=new XMLHttpRequest();
} else {// code for IE6, IE5
	xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}
```

（2）为XMLHttpRequest对象指定一个返回结果处理函数（即回调函数），用于对返回结果进行处理：

```js
xmlhttp.onreadystatechange = getResult;
```
（3）创建一个与服务器的连接，在创建时，需要指定发送请求的方式（即GET或POST），以及设置是否采用异步方式发送请求：
 
 ```js
 xmlhttp.open("GET/POST",url,true);
 ```

（4）向服务器发送请求：如果发送的是GET请求，可以将该参数设置为null;如果发送的是POST请求，可以通过该参数指定要发送的请求参数。

* 向服务器发送GET请求的代码：

```js
xmlhttp.send();
```

* 向服务器发送POST请求的代码：
   
```js
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
xmlhttp.send("fname=Bill&lname=Gates");
```
处理服务器响应：
在回调函数中，判断服务器的请求状态，保证请求已完成，然后再根据服务器的HTTP状态码，判断服务器对请求的响应是否成功。

XMLHttpResponse对象提供了两个用来访问服务器响应的属性：

（1）responseText属性，返回字符串响应：

```js
function getResult() {
	if (xmlhttp.readyState == 4) {    //判断请求状态
		if(xmlhttp.status == 200) {   //请求成功，开始处理返回结果
			alert(xmlhttp.responseText);  //显示判断结果
		} else {
			alert("请求页面有错误");
		}
	}
}
```

如果需要将响应结果显示到页面的指定位置，也可以现在页面的合适位置添加一个`<div>`或`<span>`标记，设置该标记的id属性，然后在回调函数中应用一下代码显示响应结果：

```js
function getResult() {
	if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
		document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
	}
}
```
（2）responseXML属性，返回XML响应（用于在服务器端生成复杂的响应）：

* 保存图书信息的XML文档：

```xml
<!-- Copyright w3school.com.cn -->
<!-- W3School.com.cn bookstore example -->
<bookstore>
  <book category="children">
    <title lang="en">Harry Potter</title>
    <author>J K. Rowling</author>
    <year>2005</year>
    <price>29.99</price>
  </book>
  <book category="cooking">
    <title lang="en">Everyday Italian</title>
    <author>Giada De Laurentiis</author>
    <year>2005</year>
    <price>30.00</price>
  </book>
</bookstore>
```

* 在回调函数中遍历保存图书信息的XML文档：

```html
<html>
<head>
<script type="text/javascript">
function loadXMLDoc()
{
    var xmlhttp;
    var txt,x,i,book;
    if (window.XMLHttpRequest) {// code for IE7+, Firefox, Chrome, Opera, Safari
        xmlhttp=new XMLHttpRequest();
    } else {// code for IE6, IE5
        xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
    }
    xmlhttp.onreadystatechange=function() {
        if (xmlhttp.readyState==4 && xmlhttp.status==200) {
            xmlDoc=xmlhttp.responseXML;
            txt="";
            x=xmlDoc.getElementsByTagName("book");
            for (i=0;i<x.length;i++) {
                book=x.item(i);
                txt=txt + book.getElementsByTagName("title")[0].firstChild.data + "<br />";
            }
            document.getElementById("myDiv").innerHTML=txt;
         }
    }
    xmlhttp.open("GET","/example/xmle/books.xml",true);
    xmlhttp.send();
}
</script>
</head>

<body>
<h2>My Book Collection:</h2>
    <div id="myDiv"></div>
        <button type="button" onclick="loadXMLDoc()">获得我的图书收藏列表</button>
</body>
</html>
```