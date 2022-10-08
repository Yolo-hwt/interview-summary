## css规范书写顺序

- 位置属性(position, top, right, z-index, display, float等)
- 大小(width, height, padding, margin)
- 文本(font, line-height, letter-spacing, color- text-align等)
- 背景(background, border等)
- 其他(animation, transition等)

##  标准盒模型和IE盒模型

> 我们目前所学习的知识中，以标准盒子模型为准。

标准盒子模型：

![img](面试总结_CSS.assets/2015-10-03-css-27.jpg)

IE盒子模型：

![img](面试总结_CSS.assets/2015-10-03-css-30.jpg)

上图显示：

在 CSS 盒子模型 (Box Model) 规定了元素处理元素的几种方式：

- width和height：**内容**的宽度、高度（不是盒子的宽度、高度）。
- padding：内边距。
- border：边框。
- margin：外边距。

CSS盒模型和IE盒模型的区别：

- 在 **标准盒子模型**中，**width 和 height 指的是内容区域**的宽度和高度。增加内边距、边框和外边距不会影响内容区域的尺寸，但是会增加元素框的总尺寸。
- **IE盒子模型**中，**width 和 height 指的是内容区域+border+padding**的宽度和高度。

注：Android中也有margin和padding的概念，意思是差不多的，如果你会一点Android，应该比较好理解吧。区别在于，Android中没有border这个东西，而且在Android中，margin并不是控件的一部分，我觉得这样做更合理一些，呵呵。

## 清除浮动

示例代码

```html
<div class="box">
        <div class="left"></div>
        <div class="right"></div>
        <!-- <div class="clearfix"></div> -->
    </div>
```

```css
.box {}
.left {
    ...
    float: left;
}
.right {
    ...
    float: right;
}
```

如果只有子级元素浮动，父元素内没有其他元素支撑便会塌陷

![image-20220901152053028](面试总结_CSS.assets/image-20220901152053028.png)

**1.一浮多浮**

```
.box{float: left}
```

那么就将父级元素也跟着浮动，都脱离标准文档流

**缺点：**父级设置成浮动了，那爷爷级肯定又会受影响，又得解决爷爷级的高度坍塌，这不是无限套娃吗？？

**2.父级设置绝对定位（absoluate）**

```
.box{ position: absolute}
```

`position:absolute脱离文档流`达到父级浮动目的

缺点：`position:absolute`也会`脱离文档流`啊，影响了整体布局，这不是给自己找罪受吗？

**3.父级设置overflow:hidden**

```
.box{overflow: hidden;}
```

缺点：当文本过长，且包含过长英文时，会出现英文文本被隐藏的情况

**4.父级定宽高**

```
.box{width: 450px;
        height: 50px;}
```

缺点：父级宽高受子级元素限制，无法处理子级动态宽高带来的适配问题

//若子元素超过50px

![image-20220901151953935](面试总结_CSS.assets/image-20220901151953935.png)

**5.末尾添加空元素设置clear**

关于`clear`：

![image.png](面试总结_CSS.assets/aa44b80414c8463092abd1c0103e64b6tplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp) `bottomDiv`设置成`clear:both`，代表了它左右都不能有浮动元素，这迫使了他往下移动，进而撑开了父级盒子的高度。

```js
<div class="box">
        <div class="left"></div>
        <div class="right"></div>
        <div class="bottomDiv"></div>
</div>

.bottomDiv {
            clear: both;
        }
```

![image.png](面试总结_CSS.assets/5c6167b814b64c5286ed7cdfcfb0225dtplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp)

**缺点：**增加了一个多余的`div`标签，增加了页面的`渲染负担`

**6.父级添加伪元素+clear**

```
.box::after {
            content: '.';
            height: 0;
            display: block;
            clear: both;
        }
```

**伪元素不存在文档树结构中**，优化了上面的添加clearfix元素问题

文档定位方式

## 三种文档定位方式

在讲BFC的原理之前先看看html文档的三种定位方式

**普通流**

> 元素按照其在HTML中的先后位置自上而下布局
>
> **行内元素水平排列，直到当行被占满然后换行**
>
> **块级元素则会被渲染为完整的一个新行**，除非另外指定，否则所有元素默认都是普通流定位
>
> 也就是说**普通流中元素的位置由该元素在HTML文档中的位置决定。**

**浮动**

> 在浮动定位中，元素**首先按照普通流的位置出现，然后根据浮动的方向尽可能的向左边或右边偏移**

**绝对定位**

> 在绝对定位中，元素会**整体脱离普通流**，因此绝对定位元素不会对其兄弟元素造成影响，而元素**具体的位置由绝对定位的坐标决定。**

## BFC

**什么是BFC**

**块格式化上下文（Block Formatting Context，BFC）** 

**其实BFC是上面三种布局方式中的普通流**

BFC会产生一个独立的容器，该容器内部的元素**不会在布局上影响到外部的元素**，在外部的普通流看来它和其他普通流元素无差别，文档最终会按照上面说的普通流计算布局。



**BFC的注意事项**

- 浮动定位和清除浮动时只会**应用于同一个BFC内的元素**。 
- **浮动不会影响其它BFC中元素的布局，而清除浮动只能清除同一BFC中在它前面的元素的浮动。** 
- 外边距折叠也只会发生在属于同一BFC的块级元素之间。




**浏览器对BFC的限制**

1.在BFC包含块的盒子**一个一个不重叠地垂直排列**

2.属于**同一个BFC的两个相邻Box的margin会发生折叠**，**不同BFC不会发生折叠**。

3.**每个元素的左外边距与包含块的左边界相接触**（从左向右），即使浮动元素也是如此。

4.**BFC的区域不会与float的元素区域重叠**

5.计算BFC的高度时，**浮动子元素也参与计算**



**BFC的用途**

1.防止外边距重叠

2.清除浮动



**触发条件**

- 根元素，即`HTML`标签

- 浮动元素：`float`值为`left/right`

- overflow:hidden/auto/scroll
- display`值为` inline-block、table-cell、table-caption、table、inline-table、flex、inline-flex、- - grid、inline-grid

- 定位元素：`position`值为 `absolute/fixed`



**常见使用**

**为什么overflow:hidden可以清除浮动？**

overflow: hidden使得外层元素产生了一个BFC，**BFC的高度计算包含其内部的浮动元素**，从而达到清除浮动效果



**内联元素中使用块级元素会产生什么效果？**

> 内联元素中插入块级元素会在插入的块级元素前后各产生一个匿名块与插入的块按照普通流进行定位

![img](面试总结_CSS.assets/170a9f8c2ffd95f4tplv-t2oaga2asx-zoom-in-crop-mark3024000.webp)



**🍓内联元素中使用插入浮动元素会产生什么效果？**

> 内联元素使用了display: inline-block会产生一个IFC，其内部的浮动会在内部进行浮动定位，然后整个IFC看成一个块级元素与外部进行文档流定位



## 伪类伪元素

- **伪类**：以冒号(:)开头，用于**选择处于特定状态的元素。**
- **伪元素**：以双冒号(::)开头，用于在文档中**插入虚构的元素。**

两者用法区别

- 伪类用于**向某些已经存在的选择器添加特殊效果（当状态改变时）**
- 伪元素用于**将特殊效果添加到不存在的虚拟元素中（浏览器自动创建）**



也就是说**伪类的本质还是类（class）**，作用于标签本身，只不过限定了状态条件；

而**伪元素的本质是元素（element）**，作用于该虚拟元素的内容本身。



下面的表格详细记录了各种伪类及其描述：

- [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Aactive) 代表主流浏览器都支持（至少 95% 以上）
- [❌](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Anth-child) 代表大部分主浏览器都不支持（仅 20% 以下浏览器实现该特性）
- ⚠️ 代表部分浏览器支持（可能需要加前缀，例如 `:webkit-`或 `:-moz-`等）

| **伪类**                      | **描述**                            | **兼容性**                                                   |
| ----------------------------- | ----------------------------------- | ------------------------------------------------------------ |
| `:active`                     | 元素处于活动状态时                  | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Aactive) |
| `:focus`                      | 元素已获取焦点时                    | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Afocus) |
| `:hover`                      | 元素处于悬浮状态时                  | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Ahover) |
| `:link`                       | 链接未访问时                        | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Alink) |
| `:visited`                    | 链接已访问时                        | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Avisited) |
| `:first-child`                | 元素是首个子元素时                  | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Afirst-child) |
| `:last-child`                 | 元素是最后一个子元素时              | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Alast-child) |
| `:nth-child()`                | 元素是第 n 个子元素时               | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Anth-child) |
| `:nth-last-child()`           | 元素是倒数第 n 个子元素时           | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Anth-last-child) |
| `:only-child`                 | 元素是唯一子元素时                  | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Aonly-child) |
| `:first-of-type`              | 元素是首个特定类型的子元素时        | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Afirst-of-type) |
| `:last-of-type`               | 元素是最后一个特定类型的子元素时    | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Alast-of-type) |
| `:nth-of-type()`              | 元素是第 n 个特定类型的子元素时     | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Anth-of-type) |
| `:nth-last-of-type()`         | 元素是倒数第 n 个特定类型的子元素时 | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Anth-last-of-type) |
| `:only-of-type`               | 元素是唯一的特定类型的子元素时      | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Aonly-of-type) |
| `:not`                        | 不满足指定条件时                    | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Anot) |
| `:target`                     | 元素 id 匹配到哈希值时              | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Atarget) |
| `:root`                       | 元素是文档树的根元素时              | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Aroot) |
| `:lang()`                     | 匹配到指定语言时                    | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Alang) |
| `:empty`                      | 元素处于没有子元素状态时            | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Aempty) |
| `:invalid` 和 `:valid`        | 表单项是否有效                      | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Avalid) |
| `:required` 和 `:optional`    | 表单项是否必填                      | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Arequired) |
| `:in-range`和 `:out-of-range` | 表单项是否超出范围                  | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Ain-range) |
| `:read-only`和 `:read-write`  | 表单项是否只读                      | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Aread-only) |
| `:enabled`和 `:disabled`      | 表单项是否禁用                      | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Aenabled) |
| `:fullscreen`                 | 当前处于全屏显示模式时              | [⚠️](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Afullscreen) |
| `:blank`                      | 输入框处于输入为空状态时            | ❌                                                            |
| `:dir()`                      | 匹配到特定文字书写方向时            | [❌](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3Adir) |

**伪元素有哪些？**

| **伪元素**               | **选中或创建出来的元素**               | **兼容性**                                                   |
| ------------------------ | -------------------------------------- | ------------------------------------------------------------ |
| `::first-letter`         | 选中块状元素中的首字母                 | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3A%3Afirst-letter) |
| `::first-line`           | 选中首行                               | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3A%3Afirst-line) |
| `::before`               | 在之前创建一个不在文档树中的元素       | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3A%3Abefore) |
| `::after`                | 在之后创建一个不在文档树中的元素       | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3A%3Aafter) |
| `::placeholder`          | 选中表单元素的占位文本                 | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3A%3Aplaceholder) |
| `::file-selector-button` | 选中类型为 file 的 input 里面的 button | [✅](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3A%3Afile-selector-button) |
| `::selection`            | 选中被用户高亮的部分                   | [⚠️](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3A%3Aselection) |
| `::backdrop`             | 选中视觉聚焦元素后面的背景元素         | [⚠️](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3A%3Abackdrop) |
| `::marker`               | 选中 list 的 marker                    | [⚠️](https://link.juejin.cn?target=https%3A%2F%2Fcaniuse.com%2F%3Fsearch%3D%3A%3Amarker) |

**经典使用**

1.错误提示

```
<div className="error-message">系统异常，请稍后再试</div>

//css
.error-message {
  position: relative;
  color: #666666;
  padding: 12px 30px;
  background-color: #FFECE4;
  border-radius: 5px;
}

.error-message::before {
  content: '';
  background-image: url('/public/icon-error.svg');
  background-size: 15px;
  position: absolute;
  left: 10px;
  display: inline-block;
  width: 15px;
  height: 15px;
}
```

![image-20220902104006044](面试总结_CSS.assets/image-20220902104006044.png)

注意：创建 `::before`和 `::after`的元素时，必须要设置 content 属性，否则就不存在了。另外宿主元素的 position 别忘记设置成 relative 或 absolute 了，否则布局可能会乱掉。

## css动态样式修改

核心在于利用js来操作CSSOM以实现动态样式修改

- **js动态设置className**

```
//获取指定元素class属性
let cName = element.classList;
//使用api操作classList
```

**备注：** 使用名称`className`而不是`class`作为属性名，是因为"class" 在 JavaScript 中是个保留字。

**扩展：Element.classList**

**`Element.classList`** 是一个只读属性，返回一个元素 `class` 属性的动态 [`DOMTokenList`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMTokenList) 集合。这可以用于操作 class 集合。

相比将 [`element.className`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/className) 作为以空格分隔的字符串来使用，`classList` 是一种更方便的访问元素的类列表的方法。

**api**

```js
// 使用 classList API 移除、添加类值
div.classList.remove("foo");
div.classList.add("anotherclass");

// 添加或移除多个类值
div.classList.add("foo", "bar", "baz");
div.classList.remove("foo", "bar", "baz");

// 如果 visible 类值已存在，则移除它，否则添加它
div.classList.toggle("visible");

// 将类值 "foo" 替换成 "bar"
div.classList.replace("foo", "bar");
```



- **修改CSSStyleSheet**

**`CSSStyleSheet`** 接口代表一个 [CSS](https://developer.mozilla.org/zh-CN/docs/Web/CSS) 样式表，并允许检查和编辑样式表中的规则列表。它从父类型 [`StyleSheet`](https://developer.mozilla.org/zh-CN/docs/Web/API/StyleSheet) 继承属性和方法。

第一步：获取样式

```js
//获取文档中的styleSheets
let styleSheets=document.styleSheets
//获取需要修改的样式所在页数
let styleSheet=styleSheets[index]
//index根据目标样式所在style标签或者外链样式表在文档中的声明位置决定，从0开始计算
```

第二步：操作cssRule

1.cssRule返回一个实时的CSSRuleList

```js
//直接设置样式表中某个样式的某条属性
styleSheet.cssRules[index].style.propertyName=...
//index由该样式在一条css规则内部的声明顺序决定）
```

2.styleSheet的api（操作styleSheet的规则）

这里是插入一条css规则也就是一对大括号内容，如#someid{...}

```js
stylesheet.insertRule(rule [, index])//默认头部插入相当于stylesheet.insertRule('.test{width:100px;}',0)
stylesheet.deleteRule(index)
```

3.cssRule.styleMap

使用styleMap相当于style，但是对其使用做了封装

```
styleSheet.cssRules[index].styleMap.set(propertyName, value)
```



- **Element.style修改行内样式**

```
//对于行内样式可以使用Element.style进行修改
element.style.background=red
```



- **Element.setAttribute**

获得元素的引用，然后使用它的 [`setAttribute`](https://developer.mozilla.org/en-US/DOM/element.setAttribute) 方法，指定 CSS 属性和值，来改变该元素的样式。

```
element.setAttribute('style', cssrule-item)
//示例
 color_list.setAttribute('style', 'cursor:default;')
```

## CSS中的@规则

@media

@supports

@document*(推迟至 CSS Level 4 规范)*

## 颜色

![image-20220904094551434](面试总结_CSS.assets/image-20220904094551434.png)

注意：

**颜色名**

大多数的浏览器都支持颜色名集合。

**提示：**仅仅有 16 种颜色名被 W3C 的 HTML4.0 标准所支持。

它们是：aqua, black, blue, fuchsia, gray, green, lime, maroon, navy, olive, purple, red, silver, teal, white, yellow。

如果需要使用其它的颜色，需要使用十六进制的颜色值。

https://www.w3school.com.cn/html/html_colornames.asp

## CSS解析顺序

CSS匹配不是从左到右进行查找，而是**从右到左进行查找**。

如果从左到右的顺序，那么每条选择器都需要**遍历整个DOM树，性能很受影响**。

所谓**高效的CSS就是让浏览器在查找style匹配的元素的时候尽量进行少的查找,** 

所以选择器最好写的简洁一点。

## link和import引入区别



## CSS选择器

```ini
	 div 标签选择器
     .box 类名选择器
     #box　id选择器
     div p 后代选择器
     div.box 交集选择器
     div,p,span 并集选择器
     div>p 子代选择器
     * : 通配符
     div+p: 选中div后面相邻的第一个p
     div~p: 选中的div后面所有的p
```

**一些来自c3的选择器**

**1.属性选择器`[]`**

```
c3增加
^：开头  $：结尾  *：包含
```

格式：

- `E[title]` 选中页面的E元素，并且E存在 title 属性即可。

- `E[title="abc"]`选中页面的E元素，并且E需要带有title属性，且属性值**完全等于**abc。

- `E[attr~=val]` 选择具有 attr 属性且属性值为是**以空格分隔的字词列表，其中一个等于 val 的E元素**。

  - ```ini
    <li tag="lilast litest">111222</li>
    /*选择tag字词列表中包含litest的元素*/
    li[tag~="litest"] {
            color: red;
        }
    ```

- `E[attr|=val]` 选择具有attr属性，且属性值**以val开头或后续以 '-' 连接的E元素**

- ```ini
  <li tag="lilast-litest">111222</li>
  li[tag|="lilast"] {
          color: red;
      }
  ```

- `E[title^="abc"]` 选中页面的E元素，并且E需要带有 title 属性,属性值以 abc **开头**。

- `E[title$="abc"]` 选中页面的E元素，并且E需要带有 title 属性,属性值以 abc **结尾**。

- `E[title*="abc"]` 选中页面的E元素，并且E需要带有 title 属性,属性值**任意位置包含**abc。



**2.结构伪类选择器**

**第一部分**

- `E:first-child` 匹配父元素的第一个子元素E。
- `E:last-child` 匹配父元素的最后一个子元素E。
- `E:nth-child(n)` 匹配父元素的第n个子元素E。**注意**，盒子的编号是从`1`开始算起，不是从`0`开始算起。
- `E:nth-child(odd)` 匹配奇数//等价为`E:nth-child(2n)`
- `E:nth-child(even)` 匹配偶数//等价为`E:nth-child(2n+1)`
- `E:nth-last-child(n)` 匹配父元素的倒数第n个子元素E。

！！！E是子元素

```ini
<ul>
	<li>
	...
<ul>
//li:first- child
li:nth-child(-n+5)，则表示前5个 li。
li:nth-last-child(-n+5)，则表示最后5个 li。
li:nth-child(7n)，则表示选中7的倍数。。

只要记住： n 表示 0,1,2,3,4,5,6,7,8.....就很容易明白了。当然n不能等于0
```

**第二部分**

- `E:first-of-type` 匹配同类型中的第一个同级兄弟元素E。
- `E:last-of-type` 匹配同类型中的最后一个同级兄弟元素E。
- `E:nth-of-type(n)` 匹配同类型中的第n个同级兄弟元素E。
- `E:nth-last-of-type(n)` 匹配同类型中的倒数第n个同级兄弟元素E。

既然上面这几个选择器带有`type`，我们可以这样理解：先**在同级里找到所有的E类型，然后根据 n 进行匹配。**



**3.伪元素选择器**

![img](面试总结_CSS.assets/20180207_1503.png)

## CSS选择器权重

即根据css规则判定的选择器优先级，权重大的会覆盖掉权重小的

**优先级顺序**

| 类型         | 权重       |
| ------------ | ---------- |
| *            | 0，0，0，0 |
| 元素/伪元素  | 0，0，0，1 |
| 类/伪类/属性 | 0，0，1，0 |
| ID           | 0，1，0，0 |
| 行内式       | 1，0，0，0 |
| !import      | 无穷大     |

 **!important>行内样式>ID选择器 > 类选择器 | 属性选择器 | 伪类选择器 > 元素选择器**

斗帝》斗圣》斗尊》斗宗...一个斗帝打无数个斗圣，一个斗圣打无数个斗尊，权重不能越级挑战，一个id选择器，秒杀所有类选择器

**注意点：**

- 如果两个权重不同的选择器作用在同一元素上，**权重值高的css规则生效**
- 如果两个相同权重的选择器作用在同一元素上：**以后面出现的选择器为最后规则.**
- 权重相同时，与元素距离近的选择器生效（如外链css规则和html中定义的规则）

## CSS中属性的继承性

**tips：记一下默认继承的剩下就是默认不继承的**

**默认继承的 ("Inherited: Yes") 的属性：**

- 所有元素默认继承：**visibility、cursor**
- **文本属性默认继承**：letter-spacing、word-spacing、white-space、line-height、color、font、 font-family、font-size、font-style、font-variant、font-weight、text-indent、text-align、text-shadow、text-transform、direction
- **列表元素默认继承**：list-style、list-style-type、list-style-position、list-style-image
- **表格元素默认继承**：border-collapse

**默认不继承的("Inherited: No") 的属性：**

- 所有元素默认不继承：all、display、overflow、contain
- 文本属性默认不继承：vertical-align、text-decoration、text-overflow
- 盒子属性默认不继承：width、height、padding、margin、border、min-width、min-height、max-width、max-height
- 背景属性默认不继承：background、background-color、background-image、background-repeat、background-position、background-attachment
- 定位属性默认不继承：float、clear、position、top、right、bottom、left、z-index
- 内容属性默认不继承：content、counter-reset、counter-increment
- 轮廓属性默认不继承：outline-style、outline-width、outline-color、outline
- 页面属性默认不继承：size、page-break-before、page-break-after
- 声音属性默认不继承：pause-before、pause-after、pause、cue-before、cue-after、cue、play-during

## visibility

CSS 属性 `visibility` 显示或隐藏元素而不更改文档的布局。该属性还可以隐藏 表格table 中的行或列

```
/* Keyword values */
visibility: visible;
visibility: hidden;
visibility: collapse;

/* Global values */
visibility: inherit;
visibility: initial;
visibility: unset;
```

- **visible**

元素正常显示。

- **hidden**

隐藏元素，但是其他元素的布局不改变，相当于此元素变成透明。要注意若将其子元素设为 `visibility: visible`，则该子元素依然可见。

子元素上定义的事件无法触发

- **collapse**

`collapse` 关键字对于不同的元素有不同的效果：

- **用于表格行、列、列组和行组，隐藏表格的行或列**，并取消隐藏部分的空间占用（与将 `display: none` 用于表格的行/列上的效果相当）。不影响其他行列但是，就好像折叠的行或列中的单元格一样。**此值允许从表中快速删除行或列，而不强制重新计算整个表的宽度和高度。**
- 折叠的弹性项目被隐藏，他们将占用的空间被删除。
- 对于 [XUL](https://developer.mozilla.org/zh-CN/docs/Mozilla/Tech/XUL) 元素，元素的计算大小始终为零，而且通常会忽略影响大小的其他样式，尽管边距仍然有效。
- 对于其他元素，折叠处理与隐藏相同。

## filter:alpha()和opacity

filter:alpha()与opacity都是用来设置透明度的，区别就在于兼容性的问题，**opacity支持高版本的浏览器，IE8以上不包含IE8.**

- **opacity**
  1.opacity的取值范围在0到1之间，1代表完全不透明。
- **filter:alpha()**
  1.filter:alpha(opacity=20）表示设置透明度为20，其中透明度范围为0-100，100为不透明。

**filter:alpha()语法**

```css
filter：alpha（opacity，finishopacity，style，startX，startY，finishX，finishY）

//finishopacity：设置渐变的透明效果时，用来指定结束时的透明度，范围也是0 到 100。
//style：设置渐变透明的样式，值为0代表统一形状、1代表线形、2代表放射状、3代表长方形。
//startX和startY：渐变透明效果的开始X和Y坐标。
//finishX和finishY：渐变透明效果结束X和Y 的坐标。
```

## 超链接的link、vlink、alink

超链接文字的状态可以通过伪类选择符＋样式规则来控制。 

一组专门的预定义的类称为伪类，主要用来处理超链接的状态。超链接文字的状态可以通过伪类选择符＋样式规则来控制。伪类选择符包括：

① **a:link**：未访问链接 ,如 .mycls a:link {color:blue}
② **a:visited**：已访问链接 ,
③ **a:active**：激活时（链接获得焦点时）链接的颜色 ,
④ **a:hover**：鼠标移到链接上时 ,

一般a:hover和a:visited链接的状态（颜色、下划线等）应该是相同的。

**前三者分别对应body元素的link、vlink、alink这三个属性。**

四个“状态”的先后过程是：a:link ->a:hover ->a:active ->a:visited。另外，a:active不能设置有无下划线（总是有的）。

## img元素底部为何有空白

CSS对于 display: inline 元素的 **vertical-align 默认值是 baseline**，这是一个西文排版才有的概念

![img](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/f0f1e7625a10b204bc32c7203835740d_r.jpg)可以看到，出现在[baseline](https://www.zhihu.com/search?q=baseline&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A18615056})下面的是 p 啊，q 啊,  g 啊这些字母下面的那个尾巴。

对比一下 vertical-align 的另外两个常见值，top 和 bottom:

![img](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/fa1bef7a27a3c235a2e9bd8de5ba5448_720w.jpg)![img](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/fa1bef7a27a3c235a2e9bd8de5ba5448_r.jpg)

**inline 的图片下面那一道空白正是 baseline 和 bottom 之间的这段距离**。

即使只有图片没有文字，只要是 [inline](https://www.zhihu.com/search?q=inline&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A18615056}) 的图片这段空白都会存在。

解决

1. line-height=0
2. font-size=0

## 隐藏元素的几种方式及区别

**一些常见的方式**

display:none，visibility:hiden，opacity:0 这三种；

设置 fixed 并设置足够大负距离的 left top 使其“隐藏”；

用层叠关系 z-index 把元素叠在最底下使其“隐藏”；

用 text-indent:-9999px 使其文字隐藏。

**最常用几种及区别**

**1.display: none**

- **DOM 结构**：浏览器不会渲染 display 属性为 none 的元素，会让元素**完全从渲染树中消失**，渲染的时候不占据任何空间；
- **事件监听**：**无法进行 DOM 事件监听**，不能点击；
- **性能**：修改元素会造成**文档回流**（reflow 与 repaint）,读屏器不会读取display: none元素内容，性能消耗较大；
- **继承**：是**非继承属性**，由于元素从渲染树消失，造成子孙节点消失，即使修改子孙节点属性子孙节点也无法显示，毕竟子类也不会被渲染；
- **场景**：显示出原来这里不存在的结构；
- **transition**：transition 不支持 display。



**2.visibility: hidden**

- **DOM 结构**：不会让元素从渲染树消失，渲染元素**继续占据空间，只是内容不可见**；
- **事件监听**：**无法进行 DOM 事件监听**，不能点击；
- **性能**：修改元素只会造成本**元素的重绘**（repaint），是重回操作，比回流操作性能高一些，性能消耗较少；读屏器读取visibility: hidden元素内容；
- **继承**：是**继承属性**，子孙节点消失是由于继承了visibility: hidden，子元素可以通过设置 visibility: visible 来取消隐藏；
- **场景**：显示不会导致页面结构发生变动，不会撑开；
- **transition**：transition 支持 visibility，visibility 会立即显示，隐藏时会延时。



**3.opacity: 0**

- **DOM 结构**：透明度为 100%，不会让元素从渲染树消失，渲染元素**继续占据空间**，只是内容不可见；
- **事件监听**：**可以进行 DOM 事件监听**，可以点击；
- **性能**：提升为合成层，是重建图层，**不和动画属性一起则不会产生repaint**（不脱离文档流，不会触发重绘），性能消耗较少；
- **继承**：**会被子元素继承，且子元素并不能通过 opacity: 1 来取消隐藏**；
- **场景**：可以跟transition搭配；
- **transition**：transition 支持 opacity，opacity 可以延时显示和隐藏。

## client、offset、scroll

这几个属性开头的height,width日常混淆，以height,top为例做说明

- **clientHeight（内容高度+padding）**
- **Element.clientHeight**

只读属性

元素内部的高度 (单位像素)，包含内边距，但**不包括水平滚动条、边框和外边距**。

**注意：**

！！！对于**没有定义 CSS 或者内联布局盒子（inline）的元素为 0**

**返回一个整数**(浮点数四舍五入得到)。

![img](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/2fedf514554040e482f0f03d2375385ctplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp)



- **offsetHeight（内容+内边距+边框+滚动条）**
- **HTMLElement.offestHeight**

 一个只读属性

返回该元素高度，**包含该元素的内容、内边距、边框和滚动条（如果有的话）**，不包含:before 或:after 等伪类元素的高度。

**注意：**

！！！如果**元素被隐藏**（例如 元素或者元素的祖先之一的元素的 **style.display 被设置为 none**），**则返回 0**

**返回一个整数**(浮点数四舍五入得到)。

![img](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/ba3750b28f464205a24e1f8f186a4d4atplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp)

- **scorllHeight**

当本元素的子**元素比本元素高且overflow=scroll**时，本元素会scroll

 **scrollHeight**: 包括当前不可见部分的元素的高度。

而可见部分的高度其实就是clientHeight，也就是**scrollHeight>=clientHeight恒成立**。

在**没有滚动条时scrollHeight==clientHeight恒成立**。单位px，只读元素。

![img](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/1368cfc0c44a4cc9b8e62b02eefbb0f7tplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp)

- **scrollTop**:

代表在有滚动条时，**滚动条向下滚动的距离**也就是元素顶部被遮住部分的高度。在**没有滚动条时scrollTop==0恒成立**。

单位px，可读可设置。

![img](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/957d454b02864754a0001afb5487d25ctplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp)

- **offsetTop**（与滚动条无关）

**当前元素顶部距离最近父元素顶部的距离**,和有没有滚动条没有关系。

单位px，只读元素。

![img](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/5482d8c06b5243ef976d908158ae4136tplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp)

## getBoundingClientRect()

方法返回一个 [`DOMRect`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMRect) 对象，是包含整个元素的最小矩形（包括 `padding` 和 `border-width`），其提供了元素的大小及其相对于[视口](https://developer.mozilla.org/zh-CN/docs/Glossary/Viewport)的位置。

该对象使用 `left`、`top`、`right`、`bottom`、`x`、`y`、`width` 和 `height` 这几个**以像素为单位的只读属性**描述整个矩形的位置和大小。

除了 `width` 和 `height` 以外的属性是相对于视图窗口的左上角来计算的。

<img src="%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/element-box-diagram.png" alt="img" style="zoom:50%;" />

**注意：**

**width/height**

- 在标准盒子模型中，这两个属性值分别与元素的 `width`/`height` + `padding` + `border-width` 相等。
- 而如果是 [`box-sizing: border-box`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-sizing)，两个属性则直接与元素的 `width` 或 `height` 相等。

## CSS定位

position

relative

absolute

fixed

使用 `fixed` 进行布局的元素，在一般情况下会相对于屏幕视窗来进行定位。但是如果父元素的 **`transform`, `perspective` 或 `filter` 属性不为 `none`** 时，position为`fixed` 的元素就会相对于父元素来进行定位。

static

sticky

## margin/padding

**margin的几个方向**

- `margin-top` 元素自身会向上移动，同时会影响下方的元素会向上移动；
- `margin-botom` 元素自身不会位移，但是会减少自身供css读取的高度，从而影响下方的元素会向上移动。
- `margin-left` 元素自身会向左移动，同时会影响其它元素；
- `margin-right` 元素自身不会位移，但是会减少自身供css读取的宽度，从而影响右侧的元素会向左移动；

总结：

margin-top与left会使得元素自身移动，同时影响其他元素

margin-bottom和right不会使得自身移动，但是会通过减少自身供给css读取的相应值，以此来影响其他元素

## margin塌陷与合并

- **塌陷**

问题引入：设置`marigin`让父元素相对左边及顶部各距离`100px`,子元素相对于父元素左边和顶部各`50px`

```css
div.father{
    width: 200px;
    height: 200px;
    background-color: rgb(219, 68, 101);
    margin-left: 100px;
    margin-top: 100px;
}
div.father div.son{
    width: 100px;
    height: 100px;
    background-color: rgb(56, 248, 207);
    margin-left: 50px;
    margin-top: 50px;
}
```

得到结果为

![image-20221006215320205](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/image-20221006215320205.png)

显而易见垂直方向margin出现了塌陷

这里可以得知：**父子嵌套的元素垂直方向的`margin`取最大值。**

解决：BFC

触发块级上下文后`CSS`将`HTML`的每一个元素都当成一个盒子，而且它进一步的认为每一个盒子里面都有一套正常的语法规则或者叫渲染规则

为上代码父级class添加：overflow:hidden

![image-20221006220310181](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/image-20221006220310181.png)

- **合并**

**！！！注意：**只有普通文档流中块框的垂直外边距才会发生外边距合并。行内框、浮动框或绝对定位之间的外边距不会合并。

问题引入：两个兄弟块元素，一个设置下外边距100px，一个设置上外边距100px，让两个元素相距200px

```css
.one{
    background-color: pink;
    height: 20px;
    margin-bottom: 10px;
}
.two{
    background-color: purple;
    height: 20px;
    margin-top: 100px;
}
```

![image-20221006220703934](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/image-20221006220703934.png)

我们希望得到110px的间距，却只有100px，则出现了外边距合并

- 外边距合并显示较大值

解决：

这实际上是浏览器的一个bug，那么开发过程中遇到这种情况

1.提前计算好需要的边距大小，只设置一边的边距（如只设置margin-bottom，不设置margin-top）

## CSS布局

### 水平垂直居中

- **行内元素**

水平：text-aligin:center

垂直：line-height=height（适合纯文字类型）



- **块级元素**

**元素定宽高**

**1.子绝父相，top+left+margin**

```css
 .father {
      position: relative;
   }
 .son {
     position: absolute;
     top: 50%;
     left: 50%;
     margin-left: -子级width的一半
     margin-top: -子级height的一半
 }
```

**2.子绝父相+margin auto**

```css
 .father {
     position: relative;
 }
 .son {
     position: absolute; 
     margin: auto; 
     /*水平居中*/
     left: 0;
     right: 0;
     /*垂直居中*/
     top: 0;
     bottom: 0;
 }
```

**auto定位原理**

利用元素的**流行特征**——当一个绝对定位元素，其**四个定位方向同时设置具体数值**时，流行特征就会发生。

流行特征好处在于，**元素可自动填充父级元素的可用尺寸**。

我们通过给元素设置`left、right、top、bottom`的值，然后将水平/垂直方向的` margin` 均设为 `auto`，这样一来，`auto `就会自动平分父元素的剩余空间了



**元素宽高不定**

**1.子绝父相+transfrom**

```css
 .father {
      position: relative;
   }
 .son {
     position: absolute;
     top: 50%;
     left: 50%;
     transform: translate(-50%, -50%);
 }
```

**2.flex**

- 水平居中：父级：display:flex+justify-content:center
- 垂直居中：父级：display:flex+align-items:center

```
.father {
   display: flex;
   /*水平居中*/
   justify-content: center;
   /*垂直居中*/
   align-items: center;
}
```

**3.table**

- **水平居中**

```css
.father{
    text-align: center;
}
.son {
    display: inline-block;
}
```

- **垂直居中**

```css
.father{
    display: table-cell;
    vertical-align: middle;
}
```

### 单列布局

1. 普通布局

头部、内容、底部

<img src="%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/b0c141a9004041b78304a4c5f0636c45tplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp" alt="普通布局.png" style="zoom: 50%;" />

```html
<head>
<meta charset="utf-8">
<title>单列布局-普通布局</title>
<style>
    .header{
       margin:0 auto; 
       max-width: 960px;
       height:100px;
       background-color:pink;
    }
    .container{
       margin: 0 auto;
       max-width: 960px;
       height: 500px;
       background-color: aquamarine;
    }
    .footer{
       margin: 0 auto;
       max-width: 960px;
       height: 100px;
       background-color:skyblue;
    }
</style>
</head>
<body>
    <div class="header"></div>
    <div class="content"></div>
    <div class="footer"></div>
</body>
```

内容区域（版心）为960px，采用margin:0 auto实现水平居中

<img src="%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/452692d0c63c4c11a5ffd5750da9cf17tplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp" alt="内容居中.png" style="zoom: 50%;" />

```html
<head>
<meta charset="utf-8">
<title>普通布局-内容居中</title>
<style>
    .header{
        margin:0 auto;
        height:100px;
        background-color:pink;
    }
    .content{
        margin: 0 auto;
        height: 500px;
        width:960px;
        background-color: aquamarine;
    }
    .footer{
        margin: 0 auto;
        height: 100px;
        background-color: skyblue;
    }
</style>
</head>
<body>   
    <div class="header"></div>
    <div class="center">
         <div class="content"></div>
    </div>
    <div class="footer"></div>
</body>
```

### 两栏布局

所谓两栏布局是指：左侧定宽，右侧自适应。

实现思路：
 普通流体BFC后（float:left）和浮动元素不会产生交集，顺着浮动元素形成自己的封闭上下文。

**1.float定宽**

![image.png](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/1d2d59e150d5460ca998c1bee1f60a1dtplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp)

```html
<head>
<meta charset="utf-8">
<title>两栏布局-float</title>
<style>
    .left {
        width: 300px;
        background-color: pink;
        float: left;
        height:500px;
    }
    .right {
        width:100%;
        background-color: aquamarine;
        height:500px;  
    }
</style>
</head>
<body>      
     <div class="warp">
         <div class="left">定宽</div>
         <div class="right">自适应</div>
    </div>
</body>
复制代码
```



**2.flex**

实现思路：
 父元素开启弹性空间，左盒子设置固定宽度，右盒子flex：1

![image.png](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/a99b85db5eba4769beb537ccab4878cbtplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp)

```html
<head>
<meta charset="utf-8">
<title>两栏布局-flex</title>
<style>
   .warp{
       display:flex;
  }
  .left {
       width: 300px;
       background-color: pink;
       height:500px;
  }
 .right {
       background-color: aquamarine;
       height:500px;  
       flex:1
}
</style>
</head>
<body>      
     <div class="warp">
         <div class="left">定宽</div>
         <div class="right">自适应</div>
    </div>
</body>
```



### 三栏布局

- 左右固定，中间自适应
- **中间栏放在文档流前面，保证优先加载**。

![image.png](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/246a28ec48fb4701b4ad470a910e8c6atplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp)

实现方案有三种：

**flex布局、圣杯布局、双飞翼布局**

！！！圣杯起源于a list part的一篇文章，双飞翼起源于淘宝UED，灵感来源于页面渲染。



**1.flex实现**

- 将中间盒子放置html最开始，保证优先加载
- 使用flex order属性决定三个盒子顺序，左，中，右
- 左盒子和右盒子固定宽度，中间盒子flex:1

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>flex布局</title>
<style>
    *{
        margin: 0;
        padding: 0;
    }
    .box{
        min-width: 800px;
        height: 600px;
        background: gray;
        display: flex;
    }
    .left{
        width:200px;
        height: 600px;
        background: pink;
        order:-1
    }
    .center{
        height: 600px;
        background: aquamarine;
        width:100%;
        flex:1
        order:1
    }
    .right{
        width:200px;
        height: 600px;
        background: skyblue;
        order:2
    }
</style>
</head>
<body>
<div class="box">
    <div class="center">中哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈中</div>
    <div class="left">左</div>
    <div class="right">右</div>	
</div>
</body>
</html>
```

**`order`** 属性

规定了弹性容器中的可伸缩项目在**布局时的顺序**。

- 元素按照 `order` 属性的值的**增序进行布局**。
- 拥有相同 `order` 属性值的元素按照它们在源代码中出现的顺序进行布局。



**2.圣杯布局实现**（margin-left+relative）

```html
<!DOCTYPE html>
<html lang="en">
<style>
    * {
        margin: 0;
        padding: 0;
    }

    header,
    footer {
        height: 100px;
        background-color: bisque;
    }

    .wrap {
        padding: 0 200px;
        overflow: hidden;
    }

    .wrap>div {
        height: 500px;
        float: left;
    }

    .content {
        width: 100%;
        background-color: aquamarine;
    }

    .left {
        position: relative;
        width: 200px;
        left: -200px;
        margin-left: -100%;
        background-color: palevioletred;
    }

    .right {
        position: relative;
        width: 200px;
        left: 200px;
        margin-left: -200px;
        background-color: skyblue;
    }
</style>

<body>
    <header>header</header>
    <div class="wrap">
        <div class="content">content</div>
        <div class="left">left</div>
        <div class="right">right</div>
    </div>
    <footer>footer</footer>
</body>

</html>
```

- header和footer定高，warp设置padding预留两边布局位置
- content设置宽度100%，left,content,right设置float:left

![image-20221007173630506](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/image-20221007173630506.png)

然后目的就是将left和right移动到上面padding预留出来的空白区域

使用`margin-left`

**分析：**

content设置了宽度100%，那么left和right就被挤到下一行了

使用margin-left将他们移动道上一行

然后再利用relative定位，基于当前位置，利用left定位微调

**margin-left值百分比，准确的应该是指**：

（子盒子的左边框相对于左侧兄弟盒子的右边框的距离）/左边最近兄弟元素宽度

- left

```css
//移到上一行
margin-left: -100%;
//相对当前位置向左调整
position: relative;
left: -200px;    
```

- right

```css
//移到上一行
margin-left: -200px;
//相对当前位置向左调整
position: relative;
left: 200px;    
```

![image-20221007180300513](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/image-20221007180300513.png)



**3.双飞翼布局实现**（margin-left+margin-right）

基础部分类似圣杯布局，区别在于中间部分遮罩的处理

- 圣杯：左右设置relative定位+left位移
- 双飞翼：中间部分为两个嵌套的div，内容部分放在内层，并使用margin-left/right压缩自身，将两侧被遮挡部分完整显示

具体步骤：

1.三个盒子设置浮动

2.左盒子走负margin-left:100%，右盒子走负自身的宽度

3.调整中间盒子margin

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>双飞翼</title>
</head>
<style>
    * {
        margin: 0;
        padding: 0;
    }

    .box {
        min-width: 800px;
        height: 600px;
    }

    .left {
        float: left;
        margin-left: -100%;
        width: 200px;
        height: 600px;
        background: pink;
    }

    .right {
        float: left;
        margin-left: -200px;
        width: 200px;
        height: 600px;
        background-color: aquamarine;
    }

    .center {
        float: left;
        width: 100%;
        height: 600px;
        background: skyblue;
    }

    .content {
        margin-left: 200px;
        margin-right: 200px;
        background-color: yellowgreen;
    }
</style>

<body>
    <div class="box">
        <div class="center">
            <div class="content">
                中哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈中
            </div>
        </div>
        <div class="left">左</div>
        <div class="right">右</div>
    </div>
</body>

</html>
```

### 等高布局

等高布局是指**子元素**在**父元素中高度相等**的布局方式。

**1.正值内边距+负值外边距**

padding设置一个超大值，将内容撑开，然后由margin-bottom设置一个负的超大值从末尾将其往上推，padding和margin相互抵消，达到两元素等高

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>等高布局-正值内边距+负值外边距</title>
<style type="text/css">
      *{
            margin:0;
            padding:0;
     }
     .left,
     .right {
            width:50%;
            float:left;
            text-align:center;
            background-color:aquamarine;
            /* 设置正值内边距会把背景颜色拉伸 */
            padding-bottom:9999px;
            /* 设置负值外边距把边框往里推 */
            margin-bottom:-9999px;
     }
     .right{
             background-color: pink;	 
     }
     .container {
            width:1200px;
            margin:0 auto;
            /* 开启BFC限制内容 */
            overflow:hidden;
     }
</style>
</head>
<body>
 <div class="container">
     <div class="left">111111111111</div>
     <div class="right">
        333333333333333333333333333333333333333333333333
        333333333333333333333333333333333333333333333333
        333333333333333333333333333333333333333333333333
     </div>
 </div>
</body>
</html>
```



**2.table**

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>等高布局-table布局</title>
<style type="text/css">   
 *{
    margin:0;
    padding:0;
 }
 .center,
 .left,
 .right {
    width:33.3%;
    text-align:center;
    display: table-cell;
    background-color:aquamarine;
 }
.container {
    display:table;
    width:1200px;
    margin:0 auto;
 }    
</style>
</head>
<body>
 <div class="container">
      <div class="left">111111111111</div>
      <div class="center">22222222222222222222222222</div>
      <div class="right">
        333333333333333333333333333333333333333333333333
        333333333333333333333333333333333333333333333333
        333333333333333333333333333333333333333333333333
     </div>
  </div>
</body>
</html>
```



**3.flex**

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>等高布局-flex布局</title>
<style type="text/css">   
 *{
    margin:0;
    padding:0;
 }
 .center,
 .left,
 .right {
    text-align:center;
    background-color:aquamarine;
    flex:1
 }
 .container {
    display:flex;
    /* flex-direction: row; */
    width:1200px;
    margin:0 auto;
 }
</style>
</head>
<body>
 <div class="container">
    <div class="left">111111111111</div>
    <div class="center">22222222222222222222222222</div>
    <div class="right">
	 333333333333333333333333333333333333333333333333
	 333333333333333333333333333333333333333333333333
	 333333333333333333333333333333333333333333333333
 </div>
</div>
</body>
</html>
```

如果对center主版有优先加载需求可以添加order顺序标识，来实现

```css
.left {
    order: 1
}

.center {
    order: 2;
}

.right {
    order: 3;
}
```



# **4.grid布局（待学习）**

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>grid布局</title>
<style>   
 *{
    margin:0;
    padding:0;
 }
 .center,
 .left,
 .right {
    text-align:center;
    background-color:aquamarine;
    flex:1
}
 .container {
    display:grid;
    grid-auto-flow: column;
    width:1200px;
    margin:0 auto;
}
</style>
</head>
<body>
<div class="container">
    <div class="left">111111111111</div>
    <div class="center">22222222222222222222222222</div>
    <div class="right">
	 333333333333333333333333333333333333333333333333
	 333333333333333333333333333333333333333333333333
	 333333333333333333333333333333333333333333333333
 </div>
</div>
</body>
</html>
```



### 粘带布局

当main的高度足够长的时候，footer会跟在其后面。 当main元素比较短的时候(比如小于屏幕宽度)，footer元素能够粘带在屏幕底部。

![20210429160149788.jpg](%E9%9D%A2%E8%AF%95%E6%80%BB%E7%BB%93_CSS.assets/78a4d9d7fc0c426c99bc84046281fdc8tplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp)

**1.负margin-bottom/top**

通过观察结构可以发现，warp包裹着main，然后footer紧随其后

warp设置min-height:100%是灵魂，结合margin-bottom:负的footer高度（元素自身不变，改变的是css读取值）可以让footer粘在底部

由于main部分内容在warp内部，在其内容高度小于100%时候也就不会影响到footer的展示

main内容高度大于100%后，warp设置的是min-height（非常灵魂），warp就被撑开，footer也跟着被挤下去

```html
<html>
<head>
<meta charset="UTF-8">
<title>粘带布局-负margin-bottom</title>
 <style>
/* 用一个元素将footer以外的内容包起来,设置负的margin-bottom,让他正好等于footer的高度 */
html, body {
     margin: 0;
     padding:0;
     text-align:center;
}
#wrap{
      min-height:100%;
      background-color: pink;	
      margin-bottom: -30px;
}
#footer,#main{
      height: 30px;
} 
#footer{
     background-color: aquamarine;
}
</style>
</head>
<body>
    <div id="wrap">
        <div id="main">main</div>
    </div>
    <div id="footer">footer</div>
</body>
</html>
```

同理可以在footer上使用mrgin-top达到上述相同效果

```css
#footer{
        width: 100%;
        height: 30px;
        background-color: aquamarine;
        margin-top: -30px;
    } 
```



**2.flex**

flex太强大了

这里使用warp包裹container和footer（container位于footer上方）,container内部嵌套main

warp设置flex布局，并调整方向为column

footer定高，container设置flex:1

**！！！细节注意**

上面的margin方法实际上存在一个问题底部的footer覆盖了与之相同高度的warp，而flex是对于剩余空间的分配，解决了这个问题

```html
<html>

<head>
    <meta charset="UTF-8">
    <title>粘带布局-flex</title>
    <style>
        html,
        body {
            margin: 0;
            padding: 0;
            text-align: center;
        }

        #wrap {
            height: 100%;
            display: flex;
            flex-direction: column;
        }

        .container {
            flex: 1;
        }

        .main {
            height: 300px;
            background-color: pink;
        }

        #footer {
            background-color: aquamarine;
            height: 30px;
        }
    </style>
</head>

<body>
    <div id="wrap">
        <div class="container">
            <div class="main">
                main
            </div>
        </div>
        <div id="footer">footer</div>
    </div>
</body>

</html>
```



## CSS3新特性

