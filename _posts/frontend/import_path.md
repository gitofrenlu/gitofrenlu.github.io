# import路径
<!-- author:lingke.wang@mljr.com -->
``` js
import {test} from './test';
```


## 花括号
``` js
import {test} from './test';
import test from './test';
```


如果有默认导出`export default`则可以省略花括号，且`import`的模块名是随意的，如：

``` js
// a.js
export default 'test';


// b.js
import a from './a';
import A from './a';
import myA from './a';

```

非`export default`导出的模板名，在引用时须加花括号，且引用模块名与导出时的命名必须相同，如：

``` js
// a.js
export const a = 'test';


// b.js
import {a} from './a';
import {A} from './a'; // error
import {myA} from './a'; // error

```

一个模块中只允许一个默认导出`export default`，但允许多个命名导出`export`;




## 路径

``` js
import test from 'test';
import test from './test';
```

`babel`默认会把ES6的模块转化为`commonjs`规范。

``` js
import test from 'X';
// 等价于
var test = require('X');
```

查找规则：

1. 如果`X`是内置模块，则直接返回该模块。如`require('http')`。

2. 如果`X`以`./`、`/`、`../`开头：
    * 根据`X`所在的父模块，确定`X`的绝对路径。
    * 将`X`当做文件，依次查找下面的文件，如果找到，则直接返回。
        - `X`
        - `X.js`
        - `X.json`
        - `X.node`
    * 将`X`当做目录，依次查找下面的文件，如果找到，则直接返回。
        - `X/package.json`(查找`main`字段中的文件，规则同上)
        - `X/index.js`
        - `X/index.json`
        - `X/index.node`

3. 如果`X`不带路径：
    * 根据`X`所在的父模块，确定`X`可能的安装目录。
    * 依次在每个目录中，将`X`当成文件名或目录名加载。

4. `"not found"`



## 模块格式

**CommonJS**

同步加载模块，主要用于服务端(node)。

``` js
// math.js
exports.add = function(a, b) {
  return a + b;
}


// main.js
var math = require('./math');
math.add(2, 3); // 5

```

**AMD**

异步模块定义，用于像RequireJS这样的模块加载器，主要用于浏览器。
``` js
// math.js
define(function() {
  var add = function(x, y) {
    return x + y;
  }

  return  {
    add: add
  }
})


// main.js
require(['math'], function(math) {
  alert(math.add(1, 1));
})
```


**UMD**

``` js
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD. Register as an anonymous module.
        define([], factory);
    } else if (typeof module === 'object' && module.exports) {
        // Node. Does not work with strict CommonJS, but
        // only CommonJS-like environments that support module.exports,
        // like Node.
        module.exports = factory();
    } else {
        // Browser globals (root is window)
        root.returnExports = factory();
  }
}(typeof self !== 'undefined' ? self : this, function () {

    // Just return a value to define the module export.
    // This example returns an object, but the module
    // can return a function as the exported value.
    return {};
}));
```

**ES6**

es6通过import、export实现模块的输入输出





## 参考资料

[when-should-i-use-curly-braces-for-es6-import](https://stackoverflow.com/questions/36795819/when-should-i-use-curly-braces-for-es6-import/36796281#36796281)


[what require.resolve() does](https://nodejs.org/api/modules.html#modules_all_together)

[UMD](https://github.com/umdjs/umd)

