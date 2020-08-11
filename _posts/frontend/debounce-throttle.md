# 函数节流与防抖
<!-- author:dongliang.yang02@mljr.com -->
函数节流和防抖是前端性能优化的一部分，对于在短时间内频繁触发事件的场景，可以通过通过节流或防抖技术限制函数调用的频率
以达到优化代码性能的目的
## 节流和防抖的可视化
可以从这个链接在感官上对节流和防抖一个具体的认识 [查看](http://demo.nimius.net/debounce_throttle/)
## 函数节流
#### 定义：
在规定的时间内无论事件被触发多少次，只执行一次事件处理函数。
如在scroll resize等事件都存在短时间内被频繁触发的现象。
#### 生活场景的例子：
比如地铁站的限流措施，水的限流等等
### 实现
```javascript
/**
* func: 事件处理函数
* wait：频次时间
*/
function throttle(func, wait) {
        let last = 0;
        return function () {
            const now = +new Date();
            if (now - last > wait) {
                // 因为又进行了一次函数调用，所以重置上次时间变量last
                last = now;
                func.apply(this, arguments);
            }
        }
    }
```
#### 示例：demo01
## 函数防抖
#### 定义：
在某段时间内，无论事件被触发了多少次，都只在最后一次事件触发时执行函数调用
#### 生活场景的例子：
在上下班乘坐电梯的时候，在忽略承重的情况下，不断的有人进入电梯内，则电梯门一直不会关闭，直到最后一个人进入数秒后
电梯门才关闭。人不断的进入电梯相当于程序中事件的频繁触发，电梯门关闭则相当于事件处理函数的执行
#### 实现：
```javascript
// 方法一(普通版)
    function debounceOne(func, wait) {
        let time;
        return function() {
            if (time) clearTimeout(time);
            time = setTimeout(() => {
                func.apply(this, arguments);
                console.log(arguments)
            }, wait);
        }
    }

    // 方法二(加强版) 在规定的时间内必须响应一次
    function debounceTwo(func, wait) {
        let time = null, last = 0;
        return function () {
            const now = +new Date();
            if (now - last > wait) {
                // 重置上次执行的时间即本次执行将成为上次执行
                last = now;
                // 保留调用时的上下文及参数
                func.apply(this, arguments);
            } else {
                if (time) clearTimeout(time);
                // 这里使用了箭头函数 所以不需要在setTimeout函数外保存this
                time = setTimeout(() => {
                    func.apply(this, arguments);
                }, wait)
            }
        }
    }
```
#### 示例: demo02

## 扩展
在underscore和lodash库中,throttle及debounce函数还会有一个选项对象参数(option). 即在一定的时间范围内
对第一次事件及最后一次事件的响应函数的处理，有兴趣的话可以看下相关源码

## 参考资料
1. [前端性能优化原理与实践](https://juejin.im/book/5b936540f265da0a9624b04b?referrer=56dea4aa7664bf00559f002d)
2. [Debouncing and Throttling Explained Through Examples](https://css-tricks.com/debouncing-throttling-explained-examples/)
3. [前端进阶-防抖与节流](https://juejin.im/post/5cedd320f265da1ba77c85be)

