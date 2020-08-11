# 文档对象模型及相关操作
<!-- author:dongliang.yang02@mljr.com -->


## 文档对象模型的定义
> 文档对象模型(DOM:Document-Object-Model)是针对 HTML 和 XML 文档的一个 API（应用程序编程接口）。DOM描绘了一个层次化的节点树，允许开发人员添加、移除和修改页面的某一部分

对于前端来说，浏览器底层实现了这个接口供js调用来实现页面的交互，在服务器端也有相应的实现，比如java Node也有相关的api来操作xml文档等
## Node类型
在DOM规范中定义了一个Node类型，该类型是一个抽象的接口，由具体的节点类型去实现，共有12中节点类型，比如Document类型 Element类型 Text类型等等，也就是说Node类型是最基本的节点类型，其他节点类型都继承于Node类型
## 节点关系
文档中的所有节点构成了一个树形结构，类似于家谱图。与节点相关的属性有` childNodes` `parentNode` `nextSibling` `previousSibling` `firstChild` `lastChild`等(注意这些节点关系都是只读的)，相对应的关系如下图所示
![Snipaste_2019-05-16_12-10-59.png-38.5kB][1]


  [1]: http://static.zybuluo.com/xiaojiang/9x6pjgoa92fdh4fkhksywbn8/Snipaste_2019-05-16_12-10-59.png
  
  ps：用一个demo来演示节点关系
## 操作节点 
  操作节点相关的方法有`appendChild()` `insertBefore()` `replaceChild()` `removeChild()` `cloneNode()` `normalize()`等，这里主要介绍`appendChild()` `insertBefore()` `replaceChild()`方法的用法，demo如下。。。
  
## Document类型
  > JavaScript 通过 Document 类型表示文档。在浏览器中， document 对象是 HTMLDocument （继承
自 Document 类型）的一个实例，表示整个 HTML 页面。而且， document 对象是 window 对象的一个
属性，因此可以将其作为全局对象来访问。

## 查找元素
查找元素的方法主要有` getElementById()` `getElementsByTagName()` ` getElementsByClassName() `
`querySelector()` `querySelectorAll()`

 getElementsByTagName() 方法接受一个参数，即要
取得元素的标签名，而返回的是包含零或多个元素的 NodeList

querySelector() 方法接收一个 CSS 选择符，返回与该模式匹配的第一个元素，如果没有找到匹
配的元素，返回 null 。

querySelectorAll() 方法接收的参数与 querySelector() 方法一样，都是一个 CSS 选择符，但
返回的是所有匹配的元素而不仅仅是一个元素。这个方法返回的是一个 NodeList 的实例

其中`getElementsByTagName()` `getElementsByClassName()`   `querySelector()` `querySelectorAll()`的调用对象可以是document,也可以是一个具体的元素，他们返回的也都是Nodelist实例
  
## Element类型
> 除了 Document 类型之外， Element 类型就要算是 Web 编程中最常用的类型了。 Element 类型用
于表现 XML或 HTML元素，提供了对元素标签名、子节点及特性的访问。

## 创建元素
使用 document.createElement() 方法可以创建一个新元素。这个方法只接受一个参数，即要创建元
素的标签名。这个标签名在 HTML 文档中不区分大小写，而在 XML（包括 XHTML）文档中，则是区
分大小写的。例如，使用下面的代码可以创建一个 `<div>` 元素。
```javascript
var div = document.createElement("div");
```

## 元素的子节点
元素可以有任意数目的子节点和后代节点，因为元素可以是其他元素的子节点。元素的
childNodes 属性中包含了它的所有子节点，这些子节点有可能是元素、文本节点、注释或处理指令。
不同浏览器在看待这些节点方面存在显著的不同，以下面的代码为例。

```html
<ul id="myList">
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
</ul>
```

如果是IE来解析这些代码，那么`<ul>`元素会有3个子节点，分别是3个`<li>`元素。但如果是在其他浏览器中， 
`<ul>`元素都会有7个元素，包括3个`<li>`元素和4个文本节点（表示`<li>`元素之间的空白符）。
如果像下面这样将元素间的空白符删除，那么所有浏览器都会返回相同数目的子节。

```html
<ul id="myList"><li>Item 1</li><li>Item 2</li><li>Item 3</li></ul>
```

对于这段代码， `<ul>`元素在任何浏览器中都会包含3个子节点。如果需要通过 childNodes 属性
遍历子节点，那么一定不要忘记浏览器间的这一差别。这意味着在执行某项操作以前，通常都要先检查
一下 nodeTpye 属性，如下面的例子所示。

```javascript
for (let i=0, len=element.childNodes.length; i < len; i++){
    if (element.childNodes[i].nodeType == 1){
        //执行某些操作
    }
}
```

## 样式操作
任何支持 style 特性的 HTML 元素在 JavaScript 中都有一个对应的 style 属性。在 style 特性中指定的任何 CSS 属性都将表现为这个
style 对象的相应属性。对于使用短划线（分隔不同的词汇，例如 background-image ）的 CSS 属性
名，必须将其转换成驼峰大小写形式，才能通过 JavaScript 来访问。如获取`backgroud-color`属性值的写法为`style.backgroundColor`

只要取得一个有效的 DOM元素的引用，就可以随时使用 JavaScript为其设置样式。以下是几个例子。

```javascript
var myDiv = document.getElementById("myDiv");
//设置背景颜色
myDiv.style.backgroundColor = "red";
//改变大小
myDiv.style.width = "100px";
myDiv.style.height = "200px";
//指定边框
myDiv.style.border = "1px solid black";
```

在以这种方式改变样式时，元素的外观会自动被更新。在日常的开发中，做动画效果会用到这个技术点