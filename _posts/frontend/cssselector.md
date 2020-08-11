# css选择器
<!-- author:kepeng.chu01@mljr.com -->
>HTML,CSS,JS是前端开发的核心。css选择器最重要的目的就是为了实现匹配，找到对应的DOM节点，填充样式或者添加事件。JS中的`document.querySelector(css selector)`和`document.querySelectorAll(css selector)`方法的参数就是css选择器。
## 目录
1. 浏览器渲染机制
2. 文档声明
3. css的引用方式
4. 灵活运用Chrome Devtools
5. 常用选择器
6. 扩展的一些选择器
7. css选择器的匹配规则

## 浏览器渲染机制
![](https://user-gold-cdn.xitu.io/2016/11/29/684a6bbe55267fdc310e8b2fd283bc25.png)
1. HTML解析出DOM Tree
![Alt text](https://image-static.segmentfault.com/135/587/1355879024-573db51949951_articlex "Optional title")
2. CSS解析出Style Rules
CSS解析器将CSS解析成Style Rules，Style Rules也叫CSSOM（CSS Object Model）。StyleRules也是一个树形结构，根据CSS文件整理出来的类似DOM Tree的树形结构：
![Alt text](https://image-static.segmentfault.com/376/057/3760570497-573dbf4cd0b79_articlex "Optional title")
3. 将二者关联生成Render Tree
Render Tree的构建其实就是DOM Tree和CSSOM的过程。
渲染树是和 DOM 元素相对应的，但并非一一对应。Render Tree实际上就是一个计算好样式，与HTML对应的（包括哪些显示，那些不显示）的Tree。
4. 根据Render Tree计算每个节点的信息
这个过程也叫布局。就是通过渲染树中渲染对象的信息，计算出每一个渲染对象的位置和尺寸，将其安置在浏览器窗口的正确位置。以后接触到的重排和重绘也都是在这个阶段处理的。
5. 根据计算好的信息绘制整个页面显示在屏幕上面，也就是用户看到的最终界面。

## 文档声明
> 如果在开发中有遇到`有效的CSS没有正确的渲染`的问题，排查一下是否是文档声明的问题。
```html
1. <!DOCTYPE html> /* 这一行是 HTML5 的 doctype 声明，使用该声明会使现代浏览器使用
                   HTML5 解析器处理页面，这是推荐的 doctype 声明。*/

2. <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">

3. <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
"http://www.w3.org/TR/html4/strict.dtd">

4. <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

5. <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
```
## CSS的引用方式
1. 行内样式
2. 内联样式
3. 外联样式表
4. 浏览器默认的基本规则

## 灵活运用Chrome Devtools

## 重点要掌握的选择器

### 1.*
```css
* {
  margin: 0;
  padding: 0;
}
```
在我们看比较高级的选择器之前，应该认识下这个众所周知的清空选择器，也叫通配符选择器。星号呢会将页面上所有每一个元素都选到。许多开发者都用它来清空`margin`和`padding`。当然你在练习的时候使用这个没问题，但是我不建议在生产环境中使用它。
`*`也可以用来选择某元素的所有子元素。
```css
#container * {
  border: 1px solid black;
}
```
它会选中`#container`下的所有元素。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/star.html)
### 2.#X
```css
#container {
   width: 960px;
   margin: auto;
}
```
在选择器中使用`#`可以用id来定位某个元素。大家通常都会这么使用，然后使用的时候大家还是得相当小心的。
需要问自己一下：我是不是必须要给这个元素来赋值个id来定位它呢？

`id`选择器是很严格的并且你没办法去复用它。如果可能的话，首先试试用标签名字，HTML5中的新元素，或者是伪类。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/id.html)
### 3.X
```css
.error {
  color: red;
}
```
这是个`class`选择器。它跟`id`选择器不同的是，它可以定位多个元素。当你想对多个元素进行样式修饰的时候就可以使用`class`。当你要对某个特定的元素进行修饰那就是用`id`来定位它。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/class.html)

### 4. X
```css
a { color: red; }
ul { margin-left: 0; }
```
如果你想定位页面上所有的某标签，不是通过`id`或者是&#8217;class&#8217;，这简单，直接使用类型选择器。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/tagName.html)

### 5.X Y
```css
li a {
  text-decoration: none;
}
```
下一个常用的就是后代选择器。如果你想更加具体的去定位元素，你可以使用它。例如，假如，你不需要定位所有的`a`元素，而只需要定位`li`标签下的`a`标签？这时候你就需要使用后代选择器了。

如果你的选择器像`X Y Z A B.error`这样，那你就错了。时刻都提醒自己，是否真的需要对那么多元素修饰。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/descend.html)
### 6.X:visited 和 X:link
```css
a:link {color:red;}
a:visited {color: purple;}
```
我们使用`:link`这个伪类来定位所有还没有被访问过的链接。
另外，我们也使用`:visited`来定位所有已经被访问过的链接。
[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/links.html)
### 7. X+Y
```css
ul + p {
   color: red;
}
```
这个叫相邻选择器。它指挥选中指定元素的直接后继元素。上面那个例子就是选中了所有`ul`标签后面的第一段，并将它们的颜色都设置为红色。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/adjacent.html)
### 8.X>Y
```css
div#container > ul {
  border: 1px solid black;
}
```
`X Y`和`X > Y`的差别就是后面这个只会选择它的直接子元素。看下面的例子：
```html
<div id="container">
   <ul>
      <li> List Item
        <ul>
           <li> Child </li>
        </ul>
      </li>
      <li> List Item </li>
      <li> List Item </li>
      <li> List Item </li>
   </ul>
</div>
```
`#container > ul`只会选中`id`为`container`的`div`下的所有直接`ul`元素。它不会定位到如第一个`li`下的`ul`元素。
由于某些原因，使用子节点组合选择器会在性能上有许多的优势。事实上，当在javascript中使用`css`选择器时候是强烈建议这么做的。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/childcombinator.html)
### 9. X ~ Y
```css
ul ~ p {
  color: red;
}
```
兄弟节点组合选择器跟`X+Y`很相似，然后它又不是那么的严格。`ul + p`选择器只会选择紧挨跟着指定元素的那些元素。而这个选择器，会选择跟在目标元素后面的所有匹配的元素。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/generalcombinator.html)

> 其实掌握了上面的选择器就已经满足一般的开发需求了，但当某些场景无法满足时，就需要掌握更多的选择器，值得欣慰的是，移动端对最新的选择器支持十分友好，所以可以尽情使用

## 扩展选择器
>需要根据特定的场景去有限度的使用。但不可滥用。

### 10. X[title]
```
a[title] {
  color: green;
}
```
这个叫属性选择器，上面的这个例子中，只会选择有title属性的元素。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/attributes.html)

### 11. X[href="foo"]
```css
a[href="http://strongme.cn"] {
  color: #1f6053; /* nettuts green */
}
```
上面这片代码将会把`href`属性值为`http://strongme.cn`的锚点标签设置为绿色，而其他标签则不受影响。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/attributes2.html)

### 12. X[href*="strongme"]
```css
a[href*="strongme"] {
  color: #1f6053;
}
```
Tada,正是我们需要的，这样，就指定了`strongme`这个值必须出现在锚点标签的`href`属性中，不管是`strongme.cn`还是`strongme.com`还是`www.strongme.cn`都可以被选中。
但是记得这是个很宽泛的表达方式。如果锚点标签指向的不是`strongme`相关的站点，如果要更加具体的限制的话，那就使用`^`和`$`，分别表示字符串的开始和结束。

[DEMO](https://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/attributes3.html)
### 13. X[href^="href"]
```css
a[href^="http"] {
   background: url(path/to/external/icon.png) no-repeat;
   padding-left: 10px;
}
```
大家肯定好奇过，有些站点的锚点标签旁边会有一个外链图标，我也相信大家肯定见过这种情况。这样的设计会很明确的告诉你会跳转到别的网站。
用克拉符号就可以轻易做到。它通常使用在正则表达式中标识开头。如果我们想定位锚点属性`href`中以`http`开头的标签，那我们就可以用与上面相似的代码。

注意我们没有搜索http://，那是没必要的，因为它都不包含https://。

那如果我们想找到所有指向一张图片的锚点标签呢？那我们来使用下`$`字符。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/attributes4.html)
### 14. X[href$=".jpg"]
```css
a[href$=".jpg"] {
  color: red;
}
```
这次我们又使用了正则表达式`$`，表示字符串的结尾处。这段代码的意思就是去搜索所有的图片链接，或者其它链接是以`.jpg`结尾的。但是记住这种写法是不会对`JPG`、`gif`和`png`起作用的。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/attributes5.html)

### 15. X[data-*="foo"]
```css
a[data-filetype="image"] {
   color: red;
}
```
我们如何把所有的图片类型都选中呢`png`,`jpeg`,`jpg`,`gif`？我们可以使用多选择器。看下面：
```css
a[href$=".jpg"],
a[href$=".jpeg"],
a[href$=".png"],
a[href$=".gif"] {
   color: red;
}
```
但是这样效率会很低。另外一个办法就是使用自定义属性。我们可以给每个锚点加个属性`data-filetype`指定这个链接指向的图片类型。
```css
a[data-filetype="image"] {
   color: red;
}
```
[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/attributes6.html)

### 16. X[foo~="bar"]
```css
a[data-info~="external"] {
   color: red;
}

a[data-info~="image"] {
   border: 1px solid black;
}
```
这个`~`符号可以定位那些某属性值是空格分隔多值的标签。
继续使用第15条那个例子，我们可以设置一个`data-info`属性，它可以用来设置任何我们需要的空格分隔的值。这个例子我们将指示它们为外部连接和图片链接。
```html
<a href="path/to/image.jpg" data-info="external image"> Click Me, Fool </a>
```
给这些元素设置了这个标志之后，我们就可以使用`~`来定位这些标签了。
```css
/* Target data-info attr that contains the value "external" */
a[data-info~="external"] {
   color: red;
}

/* And which contain the value "image" */
a[data-info~="image"] {
  border: 1px solid black;
}
```
[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/attributes7.html)
### 17. X:checked
```css
input[type=radio]:checked {
   border: 1px solid black;
}
```
上面这个伪类写法可以定位那些被选中的单选框，就是这么简单。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/checked.html)

### 18. X:after
`before`和`after`这俩伪类。好像每天大家都能找到使用它们的创造性方法。它们会在被选中的标签周围生成一些内容。
当使用`.clear-fix`技巧时许多属性都是第一次被使用到里面的。
```css
.clearfix:after {
    content: "";
    display: block;
    clear: both;
    visibility: hidden;
    font-size: 0;
    height: 0;
  }

.clearfix { 
   *display: inline-block; 
   _height: 1%;
}
```
上面这段代码会在目标标签后面补上一段空白，然后将它清除。
### 19. X::hover
```css
div:hover {
  background: #e3e3e3;
}
```
如果想在用户鼠标飘过的地方涂点儿彩，那这个伪类写法可以办到。
注意旧版本的IE只会对加在锚点`a`标签上的`:hover`伪类起作用。

### 20. X:not(selector)
```css
div:not(#container) {
   color: blue;
}
```
`取反`伪类是相当有用的，假设我们要把除`id`为`container`之外的所有`div`标签都选中。那上面那么代码就可以做到。

或者说我想选中所有出段落标签之外的所有标签。
```css
*:not(p) {
  color: green;
}
```
[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/not.html)

### 21. X::selection
```css
::selection {
  background-color: cyan;
}
```
::selection CSS伪元素应用于文档中被用户高亮的部分（比如使用鼠标或其他选择设备选中的部分）。
### 22. X:nth-child(n)
```css
li:nth-child(3) {
   color: red;
}
```

请注意`nth-child`接受一个整型参数，然后它不是从0开始的。如果你想获取第二个元素那么你传的值就是`li:nth-child(2)`.

我们甚至可以获取到由变量名定义的个数个子标签。例如我们可以用`li:nth-child(4n)`去每隔3个元素获取一次标签。
`li:nth-child(n+3)`去获取除了前两个之后的所有li。`li:nth-child(even)`去获取偶数，`li:nth-child(odd)`去获取奇数。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/nth.html)
### 23.X:nth-last-child(n)
```css
li:nth-last-child(2) {
   color: red;
}
```
假设你在一个`ul`标签中有N多的元素，而你只想获取最后三个元素，甚至是这样`li:nth-child(397)`，你可以用`nth-last-child`伪类去代替它。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/nthLast.html)
### 24. X:nth-of-type(n)
```css
ul:nth-of-type(3) {
   border: 1px solid black;
}
```

想象一下有5个`ul`标签。如果你只想对其中的第三个进行修饰，而且你也不想使用`id`属性，那你就可以使用`nth-of-type(n)`伪类来实现了，上面的那个代码，只有同级的第三个`ul`标签会被设置边框。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/nthOfType.html)
### 25. X:nth-last-of-type(n)
```css
ul:nth-last-of-type(3) {
   border: 1px solid black;
}
```
同样，也可以类似的使用`nth-last-of-type`来倒序的获取标签。
### 26. X:first-child
```css
ul li:first-child {
   border-top: none;
}
```
这个结构性的伪类可以选择到第一个子标签，你会经常使用它来取出第一个和最后一个的边框。

假设有个列表，没个标签都有上下边框，那么效果就是第一个和最后一个就会看起来有点奇怪。这时候就可以使用这个伪类来处理这种情况了。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/firstChild.html)
### 27. X:last-child
```css
ul > li:last-child {
   color: green;
}
```
跟`first-child`相反，`last-child`取的是父标签的最后一个标签。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/firstChild.html)
### 28. X:only-child
```css
div p:only-child {
   color: red;
}
```

它允许你获取到那些只有一个子标签的父标签。就像上面那段代码，只有一个段落标签的`div`才被着色。
```html
<div><p> My paragraph here. </p></div>

<div>
   <p> Two paragraphs total. </p>
   <p> Two paragraphs total. </p>
</div>
```
上面例子中，第二个`div`不会被选中。一旦第一个`div`有了多个子段落，那这个就不再起作用了。

[DEMO](http://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/onlyChild.html)
### 29. X:only-of-type
```css
li:only-of-type {
   font-weight: bold;
}
```
结构性伪类可以用的很聪明。它会定位某标签只有一个子标签的目标。设想你想获取到只有一个子标签的`ul`标签？

使用`ul li`会选中所有`li`标签。这时候就要使用`only-of-type`了。
```css
ul > li:only-of-type {
   font-weight: bold;
}
```
[DEMO](https://cdn.tutsplus.com/net/uploads/legacy/840_cssSelectors/selectors/onlyOfType.html)

## css选择器的匹配规则
>扩展部分，理解CSS匹配核心算法
浏览器 CSS 匹配核心算法的规则是以从右向左方式匹配节点的。这样做是为了减少无效匹配次数，从而匹配快、性能更优。

```html
<div>
   <div class="jartto">
      <p><span> 111 </span></p>
      <p><span> 222 </span></p>
      <p><span> 333 </span></p>
      <p><span class='yellow'> 444 </span></p>
   </div>
</div>
```
```css
div > div.jartto p span.yellow{
   color:yellow;
}
```
对于上述例子，如果按从左到右的方式进行查找：
1. 先找到所有 div 节点；
2. 在 div 节点内找到所有的子 div ,并且是 class = “jartto”；
3. 然后再依次匹配 p span.yellow 等情况；
4. 遇到不匹配的情况，就必须回溯到一开始搜索的 div 或者 p 节点，然后去搜索下个节点，重复这样的过程。
> 这样的搜索过程对于一个只是匹配很少节点的选择器来说，效率是极低的，因为我们花费了大量的时间在回溯匹配不符合规则的节点。
来看看从右到左来解析选择器：
1. 首先就查找到 的元素；
2. 紧接着我们判断这些节点中的前兄弟节点是否符合 P 这个规则，这样就又减少了集合的元素，只有符合当前的子规则才会匹配再上一条子规则。

试想一下，如果采用从左至右的方式读取 CSS 规则，那么大多数规则读到最后（最右）才会发现是不匹配的，这样会做费时耗能，最后有很多都是无用的；而如果采取从右向左的方式，那么只要发现最右边选择器不匹配，就可以直接舍弃了，避免了许多无效匹配。























