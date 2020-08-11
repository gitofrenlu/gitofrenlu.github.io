# JavaScript事件
<!-- author:jingxian.liang@mljr.com -->
## 问题

- 什么是JavaScript事件?
- JavaScript事件都有哪些？
- 事件都会做哪些事？
- 事件对象是什么？
- 使用事件中会遇到哪些问题？
  
## 前期准备
### dom选择器  
```javascript
//只能根据id选择
var oDom = document.getElementById(xxx)

//可以是id(#xxx),可以是class(.xxx),可以嵌套选择(#xxx .xxx .xxx),返回一个dom对象
var oDom = document.querySelector(xxxx)

//可以是id(#xxx),可以是class(.xxx),可以嵌套选择(#xxx .xxx .xxx),返回一个dom数组
var aDom = document.querySelectorAll(xxxx)


```
[相关资料](https://www.runoob.com/jsref/dom-obj-document.html)

## 什么是JavaScript事件？
JavaScript和HTML之间的交互的方式

## JavaScript事件都有哪些？
### document
onclick,ondblclick,onmouseover,onmouseout,onmousedown,onmouseup,oncontextmenu,onkeydown....
### window
window/img.onload  img.onerror
window.onscroll
window.onresize


## 事件都会做哪些事？
### 选项卡
```javascript
<style>
    *{margin: 0;padding:0}
    #tab{width: 500px;height: 400px;border: 1px solid #000;}
    #tab ul { display: flex; }
    #tab ul li{ flex: 1;height:50px;text-align: center;list-style: none;line-height: 50px;cursor: pointer;}
    #tab ul li.active{background:#f00 }
    #tab div{ display: none; font-size: 32px; } 
    #tab div:nth-of-type(1){display: block}
</style>
<div id="tab">
    <ul>
        <li class="active">1</li>
        <li>2</li>
        <li>3</li>
    </ul>
    <div>content-1</div>
    <div>content-2</div>
    <div>content-3</div>
</div>
<script>
    window.onload = function(){
        //获取所有需要切换的按钮   
        let aLI = document.querySelectorAll("#tab li")
        //获取所有需要被切换的内容
        let aItem = document.querySelectorAll("#tab div")
        aLI.forEach((item,index)=>{
            aLI[index].onclick = function(){
                //全部清除
                for(let i =0;i<aLI.length;i++){
                    aLI[i].className = ""
                    aItem[i].style.display = "none"
                }
                //点击赋值
                this.className = "active"
                aItem[index].style.display = "block"
            }
        })
    }
</script>
```
[运行](https://jsrun.net/n4XKp/edit)
### 下拉菜单
```javascript
<div class="people">
    <div class="people-name">
        个人信息 ∨
    </div>
    <ul>
        <li>订单信息</li>
        <li>修改密码</li>
        <li>退出登录</li>
    </ul>
</div>
<script>
    let people = document.querySelector(".people")
    let oUl = document.querySelector("ul")
    people.onmouseover = function(){
        oUl.style.display = "block"
    }
    people.onmouseout = function(){
        oUl.style.display = "none"
    }

</script>
```
[运行](https://jsrun.net/r4XKp/edit)

### 剩余字数
```javascript
<textarea id="txt" style="width:300px; height:200px;"></textarea>
<span id="span1">还剩10字</span>
<script>
    window.onload = function () {
        var oTxt = document.getElementById('txt');
        var oSpan = document.getElementById('span1');
        var n = 10;
        oTxt.oninput = function(){
            let num = oTxt.value.length
            if(num > n){
                oTxt.value = oTxt.value.slice(0,n)
            }
            let low = n-oTxt.value.length
            oSpan.innerHTML = '还剩' + low + '字';
        }
    };
</script>
```
[运行](https://jsrun.net/m4XKp/edit)

## 事件对象是什么？
描述了事件的一些详细的信息


### 事件对象
```javascript
//event:  兼容 IE系，chrome
//ev:   兼容IE9+ FF chrome

document.body.onclick = function(ev){
  var oEvent=ev || event;
  console.log(oEvent)
}
```
#### 阻止默认事件
`return false` 

**自定义鼠标右键**


```javascript
<div class="people">
    <ul>
        <li>订单信息</li>
        <li>修改密码</li>
        <li>退出登录</li>
    </ul>
</div>
<script>
    document.oncontextmenu = function (ev) {
        var oEvent = ev || event;
        var oDiv = document.querySelector(".people")
        oDiv.style.display = 'block';
        oDiv.style.left = oEvent.clientX + 'px';
        oDiv.style.top = oEvent.clientY + 'px';
        return false;
    };

    document.onclick = function () {
        var oDiv = document.querySelector(".people")
        oDiv.style.display = 'none';
    };
</script>
```
[运行](https://jsrun.net/R4XKp/edit)

**数字输入框**
```javascript
<input type="text" id="txt-num" />
<script>
    var oTxt = document.querySelector('#txt-num');
    oTxt.onkeydown = function (ev) {
        var oEvent = ev || event;
        //0-	48   ,  9-	57
        if (oEvent.keyCode < 48 || oEvent.keyCode > 57) {
            return false;
        }
    };
</script>
```
[运行](https://jsrun.net/u4XKp/edit)

### 事件冒泡
- 冒泡：子级的事件会传递到父级，相同的事件，冒泡一直存在
![](https://femans.com/images/pao.png)

```javascript
<div class="one">
    <div class="two">
        <div class="three"></div>
    </div>
</div>
<script>
let oOne = document.querySelector(".one")
let oTwo = document.querySelector(".two")
let oThree = document.querySelector(".three")

oOne.onclick = function(){
    alert("one")
}
oTwo.onclick = function(){
    alert("two")
}
oThree.onclick = function(){
    alert("three")
}
</script>
```
[运行](https://jsrun.net/yKyKp/)

```javascript
//兼容阻止事件冒泡函数
function cancelBubble (e) = {
    if ( e && e.stopPropagation ){
        //因此它支持W3C的stopPropagation()方法
        e.stopPropagation();
    }else{
        //否则，我们需要使用IE的方式来取消事件冒泡
        window.event.cancelBubble = true;
    }
}
```
```javascript
let oBtn = document.querySelector("#btn")
let oDiv = document.querySelector("div")
oBtn.onclick = function(ev){
    var oEvent = ev || event;
    oDiv.style.display = "block"
    oEvent.cancelBubble = true
}
document.body.onclick = function(){
    oDiv.style.display = "none"
}
```
[运行](https://jsrun.net/A4XKp/edit)

### 事件绑定
同一个事件可以执行多个函数
```javascript
<button>button</button>

<script>
    let oBtn = document.querySelector("button")
    // oBtn.onclick = function(){
    //     alert("first click")
    // }
    // oBtn.onclick = function(){
    //     alert("second click")
    // }

    // 事件类型,事件处理方法,布尔参数(默认false)
    // true捕获阶段调用事件处理方法；false冒泡阶段调用事件处理方法。 
    oBtn.addEventListener("click",function(){
        alert("first click")
    },false)
    oBtn.addEventListener("click",function(){
        alert("second click")
    },false)
</script>
```
[运行](https://jsrun.net/S4XKp/edit)


**绑定**
- obj.addEventListener(事件名,函数名，false)-(firefox、chrome、IE、safari、opera)
- obj.attachEvent(事件名，函数名)-(兼容：IE7、IE8；不兼容firefox、chrome、IE9、IE10、IE11、safari、opera)
  
**解绑定**
- obj.removeEventListener(事件名,函数名,false);
- obj.detachEvent(事件名,函数名);

  
**兼容写法**
```javascript
//绑定监听事件
function addEvent(target,type,fn){
 if(target.addEventListener){
    target.addEventListener(type,fn);
 }else{
    target.attachEvent("on"+type,fn);
 }
}

//移除监听事件
function removeEvent(target,type,fn){
 if(target.removeEventListener){
    target.removeEventListener(type,fn);
 }else{
    target.detachEvent("on"+type,fn);
 }
}

```

### 事件委托
**特点**

利用冒泡的原理，把事件加到父元素或祖先元素上，触发执行效果。

**优点**

- 提高JavaScript性能。事件委托可以显著的提高事件的处理速度，减少内存的占用。
- 动态的添加DOM元素，不需要因为元素的改动而修改事件绑定。

```javascript
<ul>
    <li>哈哈</li>
    <li>苹果</li>
    <li>香蕉</li>
    <li>橘子</li>
    <p>海豚</p>
    <li>橙子</li>
    <p>其他水果</p>
</ul>
<script>
    let oUl =document.querySelector("ul")
    oUl.onclick = function(ev){
        let oEvent = ev || window.event;
        let target = oEvent.target || oEvent.srcElement;
        if(target.nodeName.toLowerCase()==='li') {
            alert(target.innerHTML)
        }
    }
</script>
```
[运行](https://jsrun.net/44XKp/edit)



