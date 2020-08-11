# css 布局
<!-- author:ying.lin@mljr.com -->

## 文档流(normal-flow)

了解什么是文档流,就是先了解下流的概念,我们可以比喻成河流从上而下流下来……  

![EH6RD1.jpg](https://s2.ax1x.com/2019/05/16/EH6RD1.jpg)
文档流呢,由于这是显示在浏览器上面的，显示在电脑屏幕前的。如果我们将屏幕的两侧想象成河道，将屏幕的上面作为流的源头，将屏幕的底部作为流的结尾的话，那我们就抽象出来了文档流 ！
![EH6hE6.jpg](https://s2.ax1x.com/2019/05/16/EH6hE6.jpg)
像水流,流动的是水,那文档流流动的是什么呢???

![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
 <font size=100>**就是元素!**</font> ![EH6oCD.jpg](https://s2.ax1x.com/2019/05/16/EH6oCD.jpg)

文档流是文档中可显示对象在排列时所占用的位置。
将窗体自上而下分成一行行显示，并在每行中按从左到右的顺序排放元素的效果，就是文档流直观的表现。元素分为两种,一种是块状元素(block elements),一种是内联元素(inline elements),
建新哥哥在第一节课讲过这个还记得不?[内联元素、块级元素的差异](http://conf.op.mljr.com/pages/viewpage.action?pageId=37061437&preview=/37061437/37061438/%E5%89%8D%E7%AB%AF.pdf)

* 块级元素：四四方方的块，在文档中自己占一行。如`<div><p>`  
* 内联元素：（内联元素）多个内联元素，可以在一行显示。如`<span><img>`  

[🌰举一个例子🌰](https://codepen.io/anon/pen/OYRWKO#anon-login)  

为啥元素会变成这样子呢?  

![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
## 1.display-控制布局的属性(文档流布局) ![EH6oCD.jpg](https://s2.ax1x.com/2019/05/16/EH6oCD.jpg)

之所以元素变成这样,是因为每个原始都有一个默认的 display,通过类型不同设置为内联元素和块状元素

* `display:inline`  把元素设置为内联元素
* `display:block`   把元素设置为块状元素
* `display:none`    把元素设置为隐藏,页面显示你不存在,这个跟`visibility: hidden` 做区别, `visibility: hidden`虽然也是把 dom 隐藏了,但是还占有空间,元素披了一件隐身衣,去摸它还在那里~
* `display:inline-block`  把元素设置为内联块元素,简单的说就是不独占一行的块级元素  

`display`还有其他属性,自己可以[了解一下](https://developer.mozilla.org/en-US/docs/Web/CSS/display)  
[🌰举一个例子🌰](https://codepen.io/nuansediao/pen/Xwjgay)

**自己写的时候可能遇到的问题**:
- 自己写内联元素发现会出现缝隙[原因](https://blog.csdn.net/u010600693/article/details/51505476)
- 内联块元素出现缝隙[原因](https://www.zhangxinxu.com/wordpress/2012/04/inline-block-space-remove-%E5%8E%BB%E9%99%A4%E9%97%B4%E8%B7%9D/)

  
咦?不对!!可是实际中好像网页更复杂些,那些飞舞在页面上的小广告貌似没有跟着文档流走,可烦人了~
![EH67gH.jpg](https://s2.ax1x.com/2019/05/16/EH67gH.jpg)

![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  
![EH64UK.jpg](https://s2.ax1x.com/2019/05/16/EH64UK.jpg)  

## 脱离文档流!  
![EH6oCD.jpg](https://s2.ax1x.com/2019/05/16/EH6oCD.jpg)  

脱离水跑到水的上面飘着，就像河流上的小船,拜托文档流的束缚. 
![EH6LDI.jpg](https://s2.ax1x.com/2019/05/16/EH6LDI.jpg)  
**以下 float 和 position 就是脱离文档流的布局方式**
## 2.float(浮动布局)
使用 float 脱离文档流时，其他盒子会无视这个元素，但其他盒子内的文本依然会为这个元素让出位置，环绕在该元素的周围。
[🌰举一个例子🌰](https://codepen.io/nuansediao/pen/WBoGoy)  
[🌰再举一个例子🌰](https://codepen.io/nuansediao/pen/MdbyEj)  

float 详细介绍-[链接](https://developer.mozilla.org/zh-CN/docs/CSS/float)
```css
float:left | right | none | inline-start | inline-end
```
#### 特质
* 包裹性:block 元素不指定 width 的话，默认是 100%，一旦让该 div 浮动起来，立刻会像 inline 元素一样产生包裹性，宽度会跟随内容自适应。（这也是通常 float 元素需要手动指定 width 的原因）  
[🌰举一个例子🌰](https://codepen.io/nuansediao/pen/MdbjXM)

* 高度欺骗性:子元素如果设置 float,会出现高度塌陷的欺骗性,父元素以为子元素 height 为 0.  
[🌰举一个例子🌰](https://codepen.io/nuansediao/pen/GaNjwx)  
如何解决这个高度塌陷的问题呢,css 有个很吊的属性叫做`clear`,[🌰举一个例子🌰](https://codepen.io/nuansediao/pen/qGqqOX)  
```css
clear:none | left | right | both | inline-start | inline-end
```
clear 详细介绍-[链接](https://developer.mozilla.org/zh-CN/docs/Web/CSS/clear)

 综合应用 - [🌰举一个例子🌰](https://codepen.io/nuansediao/pen/PvbbxM)
## 3.position(定位布局)
`position`属性用于指定一个元素在文档中的定位方式
```css
position:static | relative | absolute | sticky | fixed
```
position 详细介绍-[链接](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position)
![Eb3yY6.jpg](https://s2.ax1x.com/2019/05/16/Eb3yY6.jpg)
* static 元素默认值,表示正常处于文档流中
* relative 相对定位,相对于之前正常位置进行定位,也可以理解为根据父元素定位  
[🌰举一个例子🌰](https://codepen.io/nuansediao/pen/gJLWpd)
* absolute 绝对定位,找到只要不是 static 定位的元素为基础来定位.不是根据父元素定位  
[🌰举一个例子🌰](https://codepen.io/nuansediao/pen/eaBWWd)
* fixed 根据浏览器窗口进行定位.  
[🌰举一个例子🌰](https://codepen.io/nuansediao/pen/EzNXKR)
* sticky 粘性定位 页面滚动超出目标区域时，它的表现就像 position:fixed,相当于屏幕定位,当父元素不在浏览器窗口内,它也就不在浏览器窗口内,但是这个属性有兼容性问题,写的时候要注意哦~  
[🌰举一个例子🌰](https://codepen.io/nuansediao/pen/byBRJr)

## 4.flex(弹性布局)
传说中的弹性盒子,可以实现各种响应式页面布局~是目前比较常用的响应式布局,这个不详细讲了讲了要好久好久...  
[阮一峰大大写的很详细可以看看](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)


容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做 main start，结束位置叫做 main end；交叉轴的开始位置叫做 cross start，结束位置叫做 cross end。

项目默认沿主轴排列。单个项目占据的主轴空间叫做 main size，占据的交叉轴空间叫做 cross size。
[![EH6jVP.png](https://s2.ax1x.com/2019/05/16/EH6jVP.png)](https://imgchr.com/i/EH6jVP)
![](https://user-gold-cdn.xitu.io/2018/4/2/1628695cb210abb4?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

**父元素:**
```css
display:flex  /*把元素定位为弹性容器*/
flex-direction  /* 决定主轴的方向，即：项目的排列方向。 */
flex-wrap  /*如何换行*/
flex-flow: <flex-direction> || <flex-wrap> /* flex-direction 和 flex-wrap 的简写形式*/
justify-content /*定义项目在主轴上的对齐方式*/
align-items /*定义项目在交叉轴上的对齐方式*/
align-content /*定义多根轴线的对齐方式。如果只有一根轴线，则，该属性无效*/
```
**子元素:**
```css
order /*属性定义项目的排列顺序。数值越小，排列越靠前，默认为0*/
flex-grow /*属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。*/
flex-shrink /*属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小*/
flex-basis /*常用的属性 属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小 */
flex:none | [ <flex-grow> <flex-shrink> || <flex-basis> ]
align-self /*align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch*/
```

[🌰举一个简单的例子🌰](https://codepen.io/nuansediao/pen/YbproB)  
[🌰举一个实用的例子🌰](https://codepen.io/nuansediao/pen/ZNBxev)

## 5.百分比布局
就是设置宽度高度相当于父元素的百分占比,适合响应式布局
`width`:宽度的百分比是相对于父盒子 width 内容宽的比。没有父盒子就是相对于浏览器的宽。
`height`:高度的百分比是相对于父盒子 height 内容高的比,没有父盒子就是相对于浏览器的高。
`padding | margin`:padding 和 margin 不管任何方向百分比都是相对于父盒子 width 内容宽的比。
border：不能书写百分数
```css
width:100%;
height:100%;
box-sizing: border-box; 
```
传统的盒模型
![EHc92Q.png](https://s2.ax1x.com/2019/05/16/EHc92Q.png)
padding + border + width= 盒子的宽度
padding+ border + height = 盒子的高度
![EHcFrn.png](https://s2.ax1x.com/2019/05/16/EHcFrn.png)

[🌰我又来举例子了🌰](https://codepen.io/nuansediao/pen/gJLKRQ)  
[🌰设置 pading 百分比的例子🌰](https://codepen.io/nuansediao/pen/vwyagq)

## 6.网格布局
这个使用较少,它的意思就是把网页化为一个个网格,再任意组合不同的网格
![EHckbq.png](https://s2.ax1x.com/2019/05/16/EHckbq.png)
```css
display: grid
display: inline-grid
```
水平区域称为"行"（row），垂直区域称为"列"（column）
`n`行有`n + 1`根水平网格线，`m`列有`m + 1`根垂直网格线  

![EHcmPU.png](https://s2.ax1x.com/2019/05/16/EHcmPU.png)  
[🌰举例子🌰](https://codepen.io/nuansediao/pen/pmReOq)  

[🌰实际例子🌰](https://30ke.cn/run/137)  
教程[链接](http://topic.42du.cn/grid)  

## 7.多列布局(栅格布局)
使用较少
```css
columns: column-width /*栏宽度*/ | column-count /*栏数*/;
column-gap /*栏之间的间隔*/
column-rule /*分割线 与border的写法几乎一毛一样*/
/*column-rule分开写是这样的：*/
column-rule-width /*分割线宽*/
column-rule-style /*分割线样式*/
column-rule-color /*分割线颜色*/
column-span /*类似于表格布局中的colspan这个HTML属性，表示某一个内容是否跨多栏显示*/
column-fill /* 当内容分栏的时候，如何平衡每一栏填充的内容 */
column-gap /*每一栏之间的那个空白间隙大小*/
``` 
相关教程[链接](https://www.zhangxinxu.com/wordpress/2019/01/css-css3-columns-layout/)  
[🌰举一个例子🌰](https://codepen.io/nuansediao/pen/vwgWLb)

## 剩下其他零零碎碎补充  

<!-- ### 媒体查询
不同的媒体类型定义不同的样式,最典型的例子[苹果官网](https://www.apple.com/cn/?afid=p238%7CsFhsR8oVX-dc_mtid_18707vxu38484_pcrid_179254463517_&cid=aos-cn-kwgo-brand--slid--product-)

具体语法-[链接](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@media)  
相关教程[链接](https://www.jianshu.com/p/eb1123d5b20b)  
pc  
![](https://www.runoob.com/wp-content/uploads/2015/06/rwd_desktop.png)  
手机  
![](https://www.runoob.com/wp-content/uploads/2015/06/rwd_phone.png)  
平板电脑  
![](https://www.runoob.com/wp-content/uploads/2015/06/rwd_tablet.png)

[🌰举一个例子🌰](https://codepen.io/nuansediao/pen/pmNOyO) -->


### 如何使用 element 创建一个页面

为了方便前端人员提高效率,更专注于写业务逻辑,催生了不少 css 框架来帮助我们,现在简单到只要看 api 复制对应代码就能生成对应的页面,或者直接用构造器来直接生产页面,然后根据具体业务修改对应的 css 样式就行.

[bootstrap 表单构造器](http://www.bootcss.com/p/bootstrap-form-builder/) 
[element 组件](https://element.eleme.io/#/zh-CN/component/container) 
表单构造器原理很简单,就是把对应的组件 html 代码拼接在一起,element 我查了下没有对应的构造器,但是实际用法跟 bootstrap 一样,简单的搬砖过程.

1. 先确定布局容器
```html
<el-container>：外层容器。当子元素中包含 <el-header> 或 <el-footer> 时，全部子元素会垂直上下排列，否则会水平左右排列。

<el-header>：顶栏容器。

<el-aside>：侧边栏容器。

<el-main>：主要区域容器。

<el-footer>：底栏容器。

<el-menu>: 左边导航条

```
[🌰例子🌰](https://codepen.io/nuansediao/pen/xNgWWa)  

2. 具体页面
是在`<el-main>`里做文章
一般具体页面都是一个模块一个内容,通常是上面是表单,下面是列表居多
```html
<el-breadcrumb>  面包屑
<el-form> 表单  (inline 行内表单模式,默认为false)
<el-form-item label='名称'>  表单子项目
<el-table> table
```
[🌰例子🌰](https://codepen.io/nuansediao/pen/joyzjG?editors=1010)

### 层叠上下文
文档流显示元素不是平级的,是有上下层的
相关资料[链接](https://juejin.im/post/5b876f86518825431079ddd6)
层叠的顺序可以用一张图表示:  
![](https://user-gold-cdn.xitu.io/2018/8/30/1658910c5cb364b6?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

1. 谁大谁上：当具有明显的层叠水平标示的时候，如识别的 z-index 值，在同一个层叠上下文领域，层叠水平值大的那一个覆盖小的那一个。通俗讲就是官大的压死官小的。
2. 后来居上：当元素的层叠水平一致、层叠顺序相同的时候，在 DOM 流中处于后面的元素会覆盖前面的元素(那些脱离文档流的娃们)

`background/border`指的是在一个元素中,内容会在背景之上  
[🌰举一个例子🌰](https://codepen.io/nuansediao/pen/qGRJOa)

`z-index` 控制层叠顺序,但是前提是该元素要脱离文档流,就要用到`position` 
[🌰举一个例子🌰](https://codepen.io/nuansediao/pen/joyeeO)

<!-- ### css 优先级和继承性
继承: 指的是子元素会继承父元素的样式
哪些属性会被继承-[链接](https://www.jianshu.com/p/34044e3c9317)
```
能继承的属性:
修饰性属性
1.字体相关的 font
2.文本系列  text(text-align)
3.表格布局 border-collapse
4.列表属性 list-style

5.元素可见性 visibility:hidden  display:none

不能继承的属性:
1.定位相关的不会被继承(float position)
2.盒子模型的属性:(width height padding margin border)
3.display 
```
[🌰举例子🌰](https://codepen.io/nuansediao/pen/vwxWWb)
一些样式去覆盖，有时你会发现无效或只是一部分起作用，这就是涉及到 CSS 优先级的问题.
之前克朋哥哥讲过几种选择器,[还记得不?](http://192.168.49.104/fe-docs/docs/share/cssselector.html#%E6%89%A9%E5%B1%95%E9%80%89%E6%8B%A9%E5%99%A8)  
这些选择器是有优先级的,优先级就是分配给指定的 CSS 声明的一个权重，它由匹配的选择器中的每一种选择器类型的数值 决定。
```css
ID 选择器， 如 #id{}

类选择器， 如 .class{}

属性选择器， 如 a[href="segmentfault.com"]{}

伪类选择器， 如 :hover{}

伪元素选择器， 如 ::before{}

标签选择器， 如 span{}

通配选择器， 如 *{}
优先级关系:内联(style="") > 内联样式表(<style>) | 外链样式表(<link>) > 浏览器缺省
样式表优先级关系：ID选择器 > 类选择器 | 属性选择器 | 伪类选择器 > 元素选择器

1. 最近的祖先样式比其他祖先样式优先级高
2. "直接样式"比"祖先样式"优先级高  
```
来张最火的图片增加记忆
![EHcu24.jpg](https://s2.ax1x.com/2019/05/16/EHcu24.jpg)

[自己可以去看下具体教程](https://user-gold-cdn.xitu.io/2018/1/14/160f4ceb5d61ae52?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

[在实际项目怎么使用](https://codepen.io/nuansediao/pen/KLarem) -->

<!-- ### normalize.css与reset.css
因为原生css 会默认带一些样式,比如p 会自带margin,body会自带margin:8px,a[href]标签字体颜色为蓝色并带有下划线 ....外加早期的浏览器支持和理解的CSS规范不同，导致渲染页面时效果不一致，会出现很多兼容性问题,比如input buttom 火狐 谷歌 ...每个浏览器显示的都不一样,每次写要把原生css替换成现在需要的样式.
**所以需要重置浏览器自带样式**

* reset.css  
是将所有的浏览器的自带样式重置掉，这样更易于保持各浏览器渲染的一致性。这也是我们公司前端目前采用就是reset.css,有兴趣的同学可以点开我们h5项目查找下
![EHIwQK.png](https://s2.ax1x.com/2019/05/16/EHIwQK.png)
```css
html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
article, aside, canvas, details, embed,
figure, figcaption, footer, header, hgroup,
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
    margin: 0;
    padding: 0;
    border: 0;
    font-size: 100%;
    font: inherit;
    vertical-align: baseline;
}
```

* Normalize.css  
Normalize.css 比较温和.
原理是浏览器默认样式既然存在就存在的价值,保护有用的浏览器默认样式而不是完全去掉它们,并且修复浏览器自身的bug并保证各浏览器的一致性,并且有大量注释和详细文档告诉你为啥这么做
```css
/**
 * 1. Addresses appearance set to searchfield in S5, Chrome
 * 2. Addresses box-sizing set to border-box in S5, Chrome (include -moz to future-proof)
 */

input[type="search"] {
  -webkit-appearance: textfield; /* 1 */
  -moz-box-sizing: content-box;
  -webkit-box-sizing: content-box; /* 2 */
  box-sizing: content-box;
}

/**
 * Removes inner padding and search cancel button in S5, Chrome on OS X
 */

input[type="search"]::-webkit-search-decoration,
input[type="search"]::-webkit-search-cancel-button {
  -webkit-appearance: none;
}
``` -->