## **Web 技术发展时间线**

- 1991 HTML
- 1994 HTML2
- 1996 CSS1 + JavaScript
- 1997 HTML4
- 1998 CSS2
- 2000 XHTML1（严格的html）
- 2002 Tableless Web Design（表格布局）
- 2005 AJAX
- **2009 HTML5**
- **2014 HTML5 Finalized**

2002年的表格布局逐渐被淘汰，是因为：**表格是用来承载数据的，并不是用来划分网页结构的**。

2009年就已经**推出了HTML5的草案，但直到2014年才有定稿，是因为有移动端的推动。**

## 什么是HTML5

- HTML5并不仅仅做为HTML标记语言的一个最新版本，它**制定了Web应用开发的一系列标准**，代表浏览器端技术的一个发展阶段

- 这个阶段，浏览器的呈现技术得到了飞跃发展和广泛支持，它**包括：HTML5、CSS3、Javascript API在内的一套技术组合。**

- 第一个**将Web做为应用开发平台的HTML语言**。

**总结：**

`HTML5`是新一代开发 **Web 富客户端**应用程序整体**解决方案**。

包括：HTML5，CSS3，Javascript API在内的一套**技术组合**。

**ps:**

**富客户端**：具有很强的**交互性**和体验的客户端程序。比如说，浏览博客，是比较简单的客户端；一个在线听歌的网站、即时聊天网站就是富客户端。

## HTML5新特性

HTML5 的一些最有趣的新特性：

- 新的语义元素，比如 <header>, <footer>, <article>, and <section>。

  - HTML5定义了八个新的语义html，所有都是块级元素

  - ```
    header, section, footer, aside, nav, main, article, figure
    ```

- 新的表单控件，比如数字、日期、时间、日历和滑块。

- 强大的图像支持（借由 <canvas> 和 <svg>）

- 强大的多媒体支持（借由 <video> 和 <audio>）

- 强大的新 API，比如用本地存储取代 cookie。

  - ```
    地理	拖放	应用缓存	web workers SSE
    ```

    

所有现代浏览器都支持 HTML5。

此外，所有浏览器，不论新旧，都会**自动把未识别元素当做行内元素来处理。**

**注意：**Internet Explorer 8 以及更早的版本，不允许对未知元素添加样式。

Sjoerd Visscher 创造了 "HTML5 Enabling JavaScript", *"the shiv"*：

通过引入此脚本使之兼容

```js
<!--[if lt IE 9]>
  <script src="http://html5shiv.googlecode.com/svn/trunk/html5.js"></script>
<![endif]-->
```

## HTML5离线缓存（manifest方法）

**原文地址：**

https://juejin.cn/post/6844903490519564295

html5之前的网页，都是无连接，必须联网才能访问，h5引入cache manifest文件实现离线缓存

**什么是mainfest？**

manifest是一个后缀名为manifest的文件，在文件中**定义那些需要缓存的文件**，支持manifest的浏览器，会将**按照manifest文件的规则，将文件保存在本地**，从而在没有网络链接的情况下，也能访问页面。

**Manifest机制**

- 正确配置app cache
- 再次访问web应用，浏览器检查manifest文件是否改变
  - 变化则缓存更新到app cache
  - 无变化直接返回app cache

**Manifest特点**

- 离线浏览: 用户可以在离线状态下浏览网站内容。
- 更快的速度: 因为数据被存储在本地，所以速度会更快.
- 减轻服务器的负载: 浏览器只会下载在服务器上发生改变的资源。

**使用**

创建index.manifest文件，在html标签引入

```
<html lang="en" manifest="index.manifest">   
```

**Manifest文件构成**

基本格式为三段： CACHE， NETWORK，与 FALLBACK，其中NETWORK和FALLBACK为可选项。

```
CACHE MANIFEST
#version 1.3
#固定格式，必须写在前面。

CACHE:
    test.css

NETWORK:
  *
```

- 以#号开头的是注释，一般会在第二行写个版本号，用来在缓存的文件更新时，更改manifest的作用，可以是版本号，时间戳或者md5码等等。

- **CACHE**（必须）

  - 标识出哪些文件需要缓存，可以是相对路径也可以是绝对路径。

  - ```
    a.css
    http://yanhaijing.com/a.css
    ```

- **NETWORK**（可选）

  - 这一部分是要绕过缓存直接读取的文件，可以使用通配符＊

  ```makefile
  #下面的代码 “login.asp” 永远不会被缓存，且离线时是不可用的：
  NETWORK:
  login.asp
  
  #可以使用星号来指示所有其他资源/文件都需要因特网连接：
  NETWORK:
  *
  ```

- **FALLBACK**（可选）

  指定了一个后备页面，当资源无法访问时，浏览器会使用该页面。

  该段落的**每条记录都列出两个 URI**：第一个表示资源，第二个表示后备页面。

  两个 URI **都必须使用相对路径并且与清单文件同源**。可以使用通配符。

  ```javascript
  #如无法使用网络则使用404.html替代/html5路径下的页面
  FALLBACK:
  /html5/ /404.html
  
  #用 “404.html” 替代所有文件。
  FALLBACK:
  *.html /404.html
  ```

**一个完整的Cache Manifest文件示例**

```ini
CACHE MANIFEST
# 2012-02-21 v1.0.0
/theme.css
/logo.gif
/main.js

NETWORK:
login.asp

FALLBACK:
/html/ /offline.html
```

**如何更新缓存**

如下三种方式，可以更新缓存：

- 更新manifest文件，手动给mainfest添加或删除文件，如：**更新注释行中的日期和版本号**

- 通过javascript操作

  - h5引入了js离线操作缓存的方法已废弃已废弃已废弃已废弃已废弃

  - ```
    window.applicationCache//已废弃已废弃已废弃已废弃
    ```

- 清除浏览器缓存

## HTML5离线缓存（service worker）



## Canvas和Svg

- **svg**

**可缩放矢量图形**（**Scalable Vector Graphics，SVG**），是一种用于描述二维的[矢量图形](https://link.juejin.cn?target=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2F%E7%9F%A2%E9%87%8F%E5%9B%BE%E5%BD%A2)，基于 [XML](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FXML%2FXML_Introduction) 的标记语言, 这意味着可以使用任何文本编辑器(如记事本)创建和编辑`SVG`图像。

与[JPEG](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FGlossary%2Fjpeg)和[PNG](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FGlossary%2FPNG)这种传统的点阵图像模式不同，SVG格式提供的是矢量图，这意味着它的图像能够被**无限放大而不失真或降低质量**，并且可以方便地修改内容。

- **canvas**

`<canvas>`本身只是相当于一块画布，不具有绘图能力，必须**通过脚本(通常是JavaScript)动态地绘制图形，脚本充当画笔的角色**。

**元素只是图形的容器, 必须使用脚本来实际绘制图形**。Canvas有几种绘制路径、框、圆、文本和添加图像的方法

**比较**

**Canvas优势**

- 绘制出来的图形是位图具有很好的渲染性能
- **适合数据量比较大（>1000）**
- **大量图形高频率交互**
- **适合游戏**
- 可以导出jpg/png图片

**Svg优势**

- **矢量图放大不失真**
- **支持事件处理器**
- 文字独立、可编辑可搜索

**区别**

1.Canvas 主要是用笔刷来绘制 2D 图形的。SVG 主要是用标签来绘制不规则矢量图的。

2.Canvas 画的是位图，SVG 画的是矢量图。

3.**SVG 节点过多时渲染慢，Canvas 性能更好一点，但写起来更复杂。**

4.**SVG 支持分层和事件，Canvas 不支持，但是可以用库实现。**

## HTML5 SSE

Server-Sent 事件指的是网页自动从服务器获得更新

接收 Server-Sent 事件通知使用EventSource

EventSource 对象用于接收服务器发送事件通知

```js
var source = new EventSource("demo_sse.php");
source.onmessage = function(event) {
    document.getElementById("result").innerHTML += event.data + "<br>";
};
```

**例子解释：**

- 创建一个新的 EventSource 对象，然后**规定发送更新的页面的 URL（本例中是 "demo_sse.php"）**
- 每当接收到一次更新，就会触发 onmessage 事件
- 当 onmessage 事件发生时，**把已接收的数据推入 id 为 "result" 的元素中**

## 行内元素&块级元素&行内块元素

- **块级元素（block）**

- ```
  p|tr|td|th|dd|dl|li|h5标签|div|h|from|ul|ol|table
  ```
  
  - 每个块级元素独占一行
  - 宽高，内外边距可控（宽度默认100%）
  - 多个块元素从上到下排列

```css
 <address>  // 定义地址 
 <caption>  // 定义表格标题 
 <dd>      // 定义列表中定义条目 
 <div>     // 定义文档中的分区或节 
 <dl>    // 定义列表 
 <dt>     // 定义列表中的项目 
 <fieldset>  // 定义一个框架集 
 <form>  // 创建 HTML 表单 
 <h1>    // 定义最大的标题
 <h2>    // 定义副标题
 <h3>     // 定义标题
 <h4>     // 定义标题
 <h5>     // 定义标题
 <h6>     // 定义最小的标题
 <hr>     // 创建一条水平线
 <legend>    // 元素为 fieldset 元素定义标题
 <li>     // 标签定义列表项目
 <noframes>    // 为那些不支持框架的浏览器显示文本，于 frameset 元素内部
 <noscript>    // 定义在脚本未被执行时的替代内容
 <ol>     // 定义有序列表
 <ul>    // 定义无序列表
 <p>     // 标签定义段落
 <pre>     // 定义预格式化的文本
 <table>     // 标签定义 HTML 表格
 <tbody>     // 标签表格主体（正文）
 <td>    // 表格中的标准单元格
 <tfoot>     // 定义表格的页脚（脚注或表注）
 <th>    // 定义表头单元格
 <thead>    // 标签定义表格的表头
 <tr>     // 定义表格中的行
```



- **行内元素（inline）**

- ```
  span|a|一些修饰文字的标签
  ```
  
  - **无法设置宽高**，**内外边距仅在左右方向有效**，上下方向不影响内容位置，但是会扩大元素范围
  - 宽度由内容长度决定，高度由元素内部字体大小控制，也可以设置行高
  - **行内元素中不能放块级元素，a 链接里面不能再放链接**
  - 相邻的行内元素会排列在同一行里，直到一行排不下才会自动换行

```js
 <a>     // 标签可定义锚 
 <abbr>     // 表示一个缩写形式 
 <acronym>     // 定义只取首字母缩写 
 <b>     // 字体加粗 
 <bdo>     // 可覆盖默认的文本方向 
 <big>     // 大号字体加粗 
 <br>     // 换行 
 <cite>     // 引用进行定义 
 <code>    // 定义计算机代码文本
 <dfn>     // 定义一个定义项目
 <em>     // 定义为强调的内容
 <i>     // 斜体文本效果
 <kbd>     // 定义键盘文本
 <label>     // 标签为 input 元素定义标注（标记）
 <q>     // 定义短的引用
 <samp>     // 定义样本文本
 <select> // 创建单选或多选菜单
 <small>     // 呈现小号字体效果
 <span>     // 组合文档中的行内元素
 <strong> // 加粗
 <sub>     // 定义下标文本
 <sup>     // 定义上标文本
 <tt>     // 打字机或者等宽的文本效果
 <var>    // 定义变量
```



- **行内块元素（inline-block）**

- ```
  img|textarea
  ```
  
  - 宽高、内外边距可控
  - 默认宽度就是它本身内容的宽度，**不独占一行**，但是之间会有空白缝隙，设置它上一级的 font-size 为 0，才会消除间隙
  - 可以在一行中放置多个行内块级元素

```html
<button> 
<input>   
<textarea> 
<select> 
<img>
```

## 标签嵌套

- 块级元素 一般用来搭建网站架构、布局、承载内容
- 内嵌元素 一般用在网站内容之中的某些细节或部位，用以“强调、区分样式、上标、下标、锚点”等等

规则

1. 块元素可以包含内联元素或某些块元素，但**内联元素却不能包含块元素**，它只能包含其它的内联元素

   ！！！注意 `li` 也是一个块级标签，li内部可以容纳div甚至ul或ol

   ！！！但是 `li `标签无法和自身进行父子级嵌套

   ```html
   //这是允许的
   <li>
       <ul>
           <li>
           	<div>
                   111
               </div>
           </li>
       </ul>
   </li>
   //这是出错的，//会被浏览器解析为两个并列的li标签
   <li><li>111<li/></li>
   
   ```

   

2. 块级元素不能放在p里面

   ```html
   <p>
   <div>111</div>
   </p>
   会被浏览器解析为
   <p></p>
   <div>111</div>
   <p></p>
   ```

   

3. 有几个特殊的块级元素**只能包含内嵌元素，不能再包含块级元素**，这几个特殊的标签是：

   h系列和dt标签嵌套块级标签不会报错，但是不符合w3c规范

   ```ini
   弱禁止：h系列、dt
   强禁止：p
   ```

4. 行内标签可以相互嵌套，但是**a标签不能嵌套自身**

## web语义化好处

Web语义化是指使用恰当语义的html标签、class类名等内容，让页面具有良好的结构与含义，从而让人和机器都能快速理解网页内容。

总结起来就是：

- 正确的标签做正确的事情
- 页面内容结构化
- 无CSS样子时也容易阅读，便于阅读维护和理解
- 便于浏览器、搜索引擎解析。 利于爬虫标记、利于SEO

## meta设置html自动跳转

```
<meta http-equiv="Refresh" content="1000" url="https://baidu.com/">
```

## base文档根 URL 元素

**HTML <base> 元素** 指定用于一个文档中包含的所有相对 URL 的根 URL。

！！！一份中只能有一个 <base> 元素。如果指定了多个 `<base>` 元素，只会使用第一个 `href` 和 `target` 值，其余都会被忽略。

一个文档的基本 URL，可以通过使用 [`document.baseURI` (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/Node/baseURI) 的 JS 脚本查询。

- document.baseURI为只读属性

- 如果文档不包含 `<base>` 元素，`baseURI` 默认为 `document.location.href`。

如果base标签指定了`href`或者`target`属性，则此base元素必须在其他具备url属性的元素之前

```html
<base href="http://www.example.com/" target="_blank">
<link href="main.css" rel="stylesheet">

//解析出来的连接为
http://www.example.com/main.css
```

