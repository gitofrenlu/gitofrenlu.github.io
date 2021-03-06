# setTimeout和Promise的任务队列
<!-- author:kepeng.chu01@mljr.com -->

栈，是指只有在一端进行存取的表结构。可以看做一个打开的箱子，永远只能对最上面的东西进行操作。也就是先进（栈底）后出（必须把上面的全都拿走），后进（栈顶）先出的顺序。

队列，是指排队过安检，先进先出。

在JS的单线程中，任务队列可以有多个。这些队列都是依靠函数调用栈来循环执行，这也就是我们常说的事件轮循。事件轮循决定了代码的执行顺序。

任务队列包括宏任务（script全局，setTimeout，setInterval）和微任务（Promise）。

setTimeout和Promise作为任务分发器，将未来需要执行的任务分发到各自队列中。

Promise在每次事件循环的队尾。

## 轮循机制
从script(整体代码)开始第一次循环，全局上下文进入函数调用栈（栈底），如果有可执行代码就进行正常的入栈出栈，如果有上面提到的setTimeout和Promise，就将任务分发到各自队列，直到调用栈清空(只剩全局)，然后执行所有的微任务队列（Promise队列），这就是第一次循环。当所有可执行的微任务执行完毕之后，循环再次从宏任务（setTimeout队列）开始执行入栈出栈任务分发等，执行完毕，然后再执行所有的微任务，第二次循环结束。。。这样一直循环下去，直到再也没有可执行的任务。这就是JS的循环机制。

等到浏览器关闭页面，在栈底的全局上下文才会出栈。

注：setTimeout分发的是内部的函数，如果是立即执行表达式，将会同步执行。而且它执行的时间不是绝对可控的，这要取决于前面代码的执行效率。

相关阅读：[https://www.tuicool.com/articles/MnY7N3a](https://www.tuicool.com/articles/MnY7N3a)

[https://www.zhihu.com/question/36972010](https://www.zhihu.com/question/36972010)
