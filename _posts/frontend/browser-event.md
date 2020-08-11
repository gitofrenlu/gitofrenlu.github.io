# 浏览器事件
### 事件概述
事件来自输入设备，我们平时的个人设备上，输入设备有三种：
- 键盘
- 鼠标
- 触摸屏
**其中，*触摸屏和鼠标* 又有一定的共性，它们被称作 pointer 设备，pointer 是指它的输入最终会被抽象成屏幕上面的一个点。**
但他们又有一定的区别，他们的精度、反应时间、支持点的数量都不一样。

现代的UI系统，都源自WIMP系统。我们点击鼠标上的按钮或触摸屏，是操作系统和浏览器把这个信息对应到了一个逻辑上的按钮，再使得它的视图对点击事件有反应。

### 捕获与冒泡

下面的代码展示了事件的传播顺序：
```
<div id="div1">
    <input type="button" value="click me" id="btn"/>
</div>
```
- 捕获过程从外向内 document -> html -> body -> div -> input
- 冒泡过程从内向外 input -> div -> body -> html -> document

``` js
    var div1 = document.getElementById('div1');
    var oBtn = document.getElementById('btn');
    div1.addEventListener('click', () => {
        console.log('div1', 'true')
    }, true)
    oBtn.addEventListener('click', () => {
        console.log('oBtn', 'true')
    }, true)
    div1.addEventListener('click', () => {
        console.log('div1', 'false')
    }, false)
    oBtn.addEventListener('click', () => {
        console.log('oBtn', 'false')
    }, false)
```
对div、及div的子元素input的鼠标按下事件做了监听，捕获和冒泡分别监听，可以看到最终产生的顺序是
- div1 true
- oBtn true
- oBtn false
- div1 false

#### 为什么会有捕获过程和冒泡过程？
- **捕获** 点击事件来自鼠标或触摸屏，鼠标点击并没有位置信息，但是一般操作系统会根据位移的累积计算出来，提供一个坐标给浏览器，把这个坐标转换为具体的元素上的事件，就是捕获过程。
- **捕获**是计算机处理事件的逻辑，**冒泡**是人类处理事件的逻辑。
- 在一个事件发生时，捕获过程跟冒泡过程总是先后发生，跟是否监听毫无关联。

建议：在监听事件时，冒泡和捕获机制建议默认使用冒泡模式，当开发组件时需要父元素控制子元素的行为，可以使用捕获机制。
#### 理解了冒泡和捕获的过程，再看监听事件的 API：
addEventListener 有三个参数：
- 事件名称
- 事件处理函数
- 捕获还是冒泡

事件处理函数不一定是个函数，也可以是个Javascript具有handleEvent对象的方法，具体事例：
``` js
var o = {
        handleEvent: event => console.log(event)
    }
    document.body.addEventListener('keydown', o , false)
```
第三个参数不一定是个bool值，也可以是个对象，可以提供更多选项。
[选项详细使用方法](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)*
- once: 只执行一次
- passive: 承诺此事件不会调用preventDefault, 这有助于性能
- useCapture: 是否捕获（否则冒泡）。

### 焦点

刚讲了pointer事件是由坐标控制，说一下键盘事件。

- 键盘事件是由焦点系统控制的，一般来说，操作系统也会提供一套焦点系统，但是现代浏览器一般都选择在自己的系统内覆盖原本的焦点系统。
- 焦点系统认为整个 UI 系统中，有且仅有一个“聚焦”的元素，所有的键盘事件的目标元素都是这个聚焦元素。
- Tab 键被用来切换到下一个可聚焦的元素，焦点系统占用了 Tab 键，但是可以用 JavaScript 来阻止这个行为。
**浏览器 API 还提供了 API 来操作焦点，如：**
``` js
document.body.focus();
document.body.blur();
```
其实原本键盘事件不需要捕获过程，但为了跟pointer保持一致，也规定了从外向内传播的捕获过程。
### 自定义事件

除了来自输入设备的事件，还可以自定义事件，因为DOM API中的事件不能用于普通对象，所以只能在DOM元素上使用自定义事件。
自定义事件的代码大概是这样：
可以使用 CustomEvent 接口的事件可用于传送自定义数据
**event = new CustomEvent(type [, eventInitDict])**

类似于 Event 事件的构造函数进行工作，除了可选的 eventInitDict 参数现在还允许设置 detail 属性, 其中 detail 属性必须返回其被初始化的值。
使用自定义事件时，“自定义”事件包含两个组件：自定义事件名称和触发该事件的能力。但是，向元素添加事件监听器仍然保持不变

``` js
myElement.addEventListener("userLogin", function(e) {
    console.info("Event is: ", e);
    console.info("Custom data is: ", e.detail);
})
```
添加一个userLogin事件，比较特殊的部分是创建和触发事件。

``` js
// 首先创建一个事件
let myEvent = new CustomEvent("userLogin", {
    detail: {
        username: "davidwalsh"
    }
});

// 触发！
myElement.dispatchEvent(myEvent);
```

CustomEvent 构造函数允许开发人员创建自定义事件，允许传递自定义事件名称以及一些特殊属性；而 dispatchEvent 触发给定元素上的事件。我们通过配置 bubbles，cancelable，detail 属性来触发超级定制（super-customized）的事件：

``` js
let myEvent = new CustomEvent("userLogin", {
    detail: {
        username: "davidwalsh"
    },
    bubbles: true,
    cancelable: false
});
```

``` js
var evt = new Event("look", {"bubbles":true, "cancelable":false});
document.dispatchEvent(evt);
```
这里使用 Event 构造器来创造了一个新的事件，然后调用 dispatchEvent 来在特定元素上触发。我们可以给Event添加自定义属性、方法。

**注：旧的自定义事件方法（使用 document.createEvent 和 initEvent）已经被废弃。**
### 总结
我们讲了浏览器中的事件，分别介绍了事件的捕获与冒泡机制、焦点机制和自定义事件。
- 捕获与冒泡机制来自 pointer 设备输入的处理，捕获是计算机处理输入的逻辑，冒泡是人类理解事件的思维，捕获总是在冒泡之前发生。
- 焦点机制则来自操作系统的思路，用于处理键盘事件。

**找出自己所知道的所有事件类型，和它们的目标元素类型？**
### 常用事件
- 键盘事件
    目标元素：任何指定元素
    事件：
    keyup、keydown、keypress

- 鼠标事件
    目标元素：任何指定元素
    事件：
    click、dbclick、mousedown、mouseup、mousenter、mouseleave、mousemove、mouseover、mouseout、mouseup、select、wheel
- 拖拽事件(Drag & Drag events)
    目标元素： 当前被拖拽的元素
    事件：
    drag、dragend、dragstart、drop
- 存储事件（Storage events）
    change(目标元素：input、select、textarea)
    storage(localStorage、sessionStorage)
- 粘贴板事件(Clipboard events)
  目标元素：任何指定元素或者粘贴板对象
  事件：
  copy、cut、paste
- 视窗事件（View Events）
    目标元素：浏览器（window）或 document
    事件：
    fullscreenchange、fullscreenerror、resize、scroll
- 表单事件（form events）
    目标元素：form元素
    事件：
    reset、submit、change
 - Websocket事件 (Websocket events)
    目标元素：Websocket
    事件：
    open、message、error、close
  - 资源事件 （Resource Events）
    目标元素：有src属性的元素
    事件：
    cached、error、load...

*常用[事件参考](https://developer.mozilla.org/zh-CN/docs/Web/Events)*