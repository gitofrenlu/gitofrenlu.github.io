# 单元测试

<!-- author:jingxian.liang@mljr.com -->

**单元测试是用来对一个模块、一个函数或者一个类来进行正确性检验的测试工作。**

## 测试框架

所谓"测试框架"，就是运行测试的工具。通过它，可以为 JavaScript 应用添加测试，从而保证代码的质量。
类似的测试框架还有 Mocha、Jasmine、Karma、Tape 等

## Mocha

Mocha（发音"摩卡"）诞生于 2011 年，是现在最流行的 JavaScript 测试框架之一，在浏览器和 Node 环境都可以使用。

**1.安装**

```
npm install -g mocha
```

**2.测试脚本**

> Mocha 的作用是运行测试脚本，首先必须学会写测试脚本。所谓"测试脚本"，就是用来测试源码的脚本。

add.js:

```javascript
function add(x, y) {
  return x + y;
}
export default add;
```

要测试这个加法模块是否正确，就要写测试脚本。
通常，测试脚本与所要测试的源码脚本同名，但是后缀名为`.test.js`（表示测试）或者`.spec.js`（表示规格）。比如，add.js 的测试脚本名字就是 `add.test.js`。

```javascript
import { expect } from "chai";
import add from "../src/add.js";

describe("加法函数的测试:", function() {
  it("1 加 1 应该等于 2", function() {
    expect(add(1, 1)).to.be.equal(2);
  });
  it("1 加 1 应该不等于 3", function() {
    expect(add(1, 1)).to.not.be.equal(3);
  });
});
```

上面这段代码，就是测试脚本，它可以独立执行。测试脚本里面应该包括一个或多个 `describe` 块，每个 `describe` 块应该包括一个或多个 `it` 块。

`describe` 块称为"测试套件"（test suite），表示一组相关的测试。它是一个函数，第一个参数是测试套件的名称（"加法函数的测试"），第二个参数是一个实际执行的函数。

`it` 块称为"测试用例"（test case），表示一个单独的测试，是测试的最小单位。它也是一个函数，第一个参数是测试用例的名称（"1 加 1 应该等于 2"

### Mocha 用法

```javascript
mocha test/1_test.spec.js
```

Mocha 默认运行 test 子目录里面的测试脚本。所以，一般都会把测试脚本放在 test 目录里面,但是只执行目录第一层

## chai.js

```javascript
expect(add(1, 1)).to.be.equal(2);
```

所谓"断言"，就是判断源码的实际执行结果与预期结果是否一致，如果不一致就抛出一个错误。上面这句断言的意思是，调用 `add(1, 1)`，结果应该等于 2。

所有的测试用例（it 块）都应该含有一句或多句的断言。它是编写测试用例的关键。断言功能由断言库来实现，`Mocha` 本身不带断言库，所以必须先引入断言库。

```javascript
import { expect } from "chai";
```

断言库有很多种，Mocha 并不限制使用哪一种。上面代码引入的断言库是 chai，并且指定使用它的 expect 断言风格。

`expect` 断言的优点是很接近自然语言，下面是一些例子。[详细](https://www.jianshu.com/p/f200a75a15d2)

```javascript
// 相等或不相等
expect(4 + 5).to.be.equal(9);
expect(4 + 5).to.be.not.equal(10);
expect(foo).to.be.deep.equal({ bar: "baz" });

// 布尔值为true
expect("everthing").to.be.ok;
expect(false).to.not.be.ok;

// typeof
expect("test").to.be.a("string");
expect({ foo: "bar" }).to.be.an("object");
expect(foo).to.be.an.instanceof(Foo);

// include
expect([1, 2, 3]).to.include(2);
expect("foobar").to.contain("foo");
expect({ foo: "bar", hello: "universe" }).to.include.keys("foo");

// empty
expect([]).to.be.empty;
expect("").to.be.empty;
expect({}).to.be.empty;

// match
expect("foobar").to.match(/^foo/);
```

基本上，`expect` 断言的写法都是一样的。头部是 `expect` 方法，尾部是断言方法，比如 `equal`、`a/an`、`ok`、`match` 等。两者之间使用 `to` 或 `to.be` 连接。

如果 `expect` 断言不成立，就会抛出一个错误。事实上，只要不抛出错误，测试用例就算通过。

```javascript
it("1 加 1 应该等于 2", function() {});
```

上面的这个测试用例，内部没有任何代码，由于没有抛出了错误，所以还是会通过。

## 生命周期

Mocha 在 `describe` 块之中，提供测试用例的四个钩子：`before()`、`after()`、`beforeEach()`和 `afterEach()`。它们会在指定时间执行。

```javascript
describe("hooks", function() {
  before(function() {
    // 在本区块的所有测试用例之前执行
  });

  after(function() {
    // 在本区块的所有测试用例之后执行
  });

  beforeEach(function() {
    // 在本区块的每个测试用例之前执行
  });

  afterEach(function() {
    // 在本区块的每个测试用例之后执行
  });

  // test cases
});
```

## vue-test-utils

> Vue Test Utils 是 Vue.js 官方的单元测试实用工具库。

```javascript
<template>
  <div class="hello">
    <h1>{{ msgs }}</h1>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msgs: String
  }
}
</script>
```

上述代码是个 vue 组件传值的例子,通过 msgs 展示值的传递

```javascript
import { expect } from "chai";
import { shallowMount } from "@vue/test-utils";
import HelloWorld from "@/components/HelloWorld.vue";

describe("HelloWorld.vue", () => {
  it("测试子组件传值", () => {
    const msgs = "new message";
    // 现在挂载组件，你便得到了这个包裹器
    const wrapper = shallowMount(HelloWorld, {
      propsData: {
        msgs
      }
    });
    expect(wrapper.text()).to.include(msgs);
  });
});
```

上述代码中看到`shallowMount`,这是一个官方提供的测试函数，它有 2 个参数。第一个参数是组件名，
第二个参数是个 [Object](https://vue-test-utils.vuejs.org/zh/api/#shallowmount)

- Count.vue

```javascript
<template>
  <div>
    <input v-model="count" type="number" class="count">
    <button class="add" @click="add">+</button>
    <button class="minus" @click="minus">-</button>
    </div>
</template>
<script>
export default {
  data () {
    return {
      count: 0
    }
  },
  methods:{
      add(){
          this.count++
      },
      minus(){
          this.count--
      }
  }
}
</script>
```

```javascript
import { expect } from "chai";
import { shallowMount } from "@vue/test-utils";
import Count from "@/components/Count.vue";

describe("Count.vue", () => {
  let wrapper;
  afterEach(() => {
    wrapper && wrapper.destroy();
  });
  it("点击按钮应该使得计数递增", () => {
    wrapper = shallowMount(Count);
    expect(wrapper.vm.count).to.equal(0);
    wrapper.find(".add").trigger("click");
    expect(wrapper.vm.count).to.equal(1);
  });
  it("点击按钮应该使得计数递减", () => {
    wrapper = shallowMount(Count);
    expect(wrapper.vm.count).to.equal(0);
    wrapper.find(".minus").trigger("click");
    expect(wrapper.vm.count).to.equal(-1);
  });
});
```

上述用例中提到了 vm,find,trigger,其中`vm`是 wrapper 的属性，可以访问一个实例所有的方法和属性。
而`find`返回匹配选择器的第一个 DOM 节点或 Vue 组件的 Wrapper。`trigger`,在该 Wrapper DOM 节点上触发一个事件。

- Input.vue

```javascript
<template>
  <div>
    <input v-model="username">
    <div
      v-if="error"
      class="error"
    >
      {{ error }}
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      username: ''
    }
  },

  computed: {
    error () {
      return this.username.trim().length < 7
        ? '请输入大于6位的账号'
        : ''
    }
  }
}
</script>
```

```javascript
describe("Input.vue", () => {
  it("测试Input组件校验长度", () => {
    // 渲染这个组件
    const wrapper = shallowMount(Input);
    // `username` 在除去头尾空格之后不应该少于 7 个字符
    wrapper.setData({
      username: " ".repeat(7)
    });
    // 确认错误信息被渲染了
    expect(wrapper.find(".error").exists()).to.be.true;

    // // 将名字更新至足够长
    wrapper.setData({
      username: "Lachlan"
    });
    // // 断言错误信息不再显示了
    expect(wrapper.find(".error").exists()).to.be.false;
  });
});
```

### Wrapper

[Wrapper](https://vue-test-utils.vuejs.org/zh/api/wrapper/) 是一个包括了一个挂载组件或 vnode，以及测试该组件或 vnode 的方法。

包含以下属性：vm ，element ，options ；

以下方法：attributes([key])，classes([className]) ，contains(selector) ，destroy() ，emitted()....

## 参考资料

[测试框架 Mocha 实例教程](http://www.ruanyifeng.com/blog/2015/12/a-mocha-tutorial-of-examples.html)

[Vue Test Utils](https://vue-test-utils.vuejs.org/zh/)

[chai.js api 英文](https://www.chaijs.com/api/bdd/)

[chai.js api 中文](https://www.jianshu.com/p/f200a75a15d2)

[vue-cli 3.0](https://cli.vuejs.org/zh/guide/creating-a-project.html#%E4%BD%BF%E7%94%A8%E5%9B%BE%E5%BD%A2%E5%8C%96%E7%95%8C%E9%9D%A2)
