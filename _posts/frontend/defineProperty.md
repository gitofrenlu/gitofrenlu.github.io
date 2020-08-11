# Object.defineProperty
<!-- author:f2engineer@outlook.com -->
# Object.defineProperty(obj, prop, descriptor)

**obj** 要在其上定义属性的对象。

**prop**
要定义或修改的属性的名称。

**descriptor**
将被定义或修改的属性描述符。

#### descriptor
> 数据描述
> configurable
> enumerable
> writable
> value
> 	
> 存储器描述
> get
> set

```
Note：当使用了getter或setter方法，不允许使用writable和value这两个属性
```
##### 对象字面量
```javascript
var o = {};

o.a = 1;
// 等同于 :
Object.defineProperty(o, "a", {
  value : 1,
  writable : true,
  configurable : true,
  enumerable : true
});


// 另一方面，
Object.defineProperty(o, "a", { value : 1 });
// 等同于 :
Object.defineProperty(o, "a", {
  value : 1,
  writable : false,
  configurable : false,
  enumerable : false
});
```
##### 案例

```javascript
var dom = document.getElementById('target')
Object.defineProperty(dom, 'translateX', {
	set: function(value) {
	   var transformText = 'translateX(' + value + 'px)';
	   dom.style.webkitTransform = transformText;
	   dom.style.transform = transformText;
	}
}

dom.translateX = 10;
dom.translateX = -10;
//甚至可以拓展设置如scale, originX, translateZ,等各个属性，达到下面的效果
dom.scale = 1.5;  //放大1.5倍
dom.originX = 5;  //设置中心点X
```

```javascript
// 如在Express4.0中，该版本去除了一些旧版本的中间件，为了让用户能够更好地发现，其有下面这段代码，通过修改get属性方法，让用户调用废弃属性时抛错并带上自定义的错误信息。
[
  'json',
  'urlencoded',
  'bodyParser',
  'compress',
  'cookieSession',
  'session',
  'logger',
  'cookieParser',
  'favicon',
  'responseTime',
  'errorHandler',
  'timeout',
  'methodOverride',
  'vhost',
  'csrf',
  'directory',
  'limit',
  'multipart',
  'staticCache',
].forEach(function (name) {
  Object.defineProperty(exports, name, {
    get: function () {
      throw new Error('Most middleware (like ' + name + ') is no longer bundled with Express and must be installed separately. Please see https://github.com/senchalabs/connect#middleware.');
    },
    configurable: true
  });
});

```

##### 数据劫持案例
```javascript
var data = {name: 'kindeng'};
observe(data);
data.name = 'dmq';

function observe(data) {
    if (!data || typeof data !== 'object') {
        return;
    }
    // 取出所有属性遍历
    Object.keys(data).forEach(function(key) {
	    defineReactive(data, key, data[key]);
	});
};

function defineReactive(data, key, val) {
    observe(val); // 监听子属性
    Object.defineProperty(data, key, {
        enumerable: true, // 可枚举
        configurable: false, // 不能再define
        get: function() {
            return val;
        },
        set: function(newVal) {
            console.log('监听到值变化了 ', val, ' --> ', newVal);
            val = newVal;
        }
    });
}
```
