---
layout: post
title:  "async&await使用"
date:   2020-06-11  +0800
categories: 基础知识
tag: 知识点
---

* content
{:toc}

# async&await使用
<!-- author:jingxian.liang@mljr.com -->

- Q：假设有3个接口，A,B,C 逐次依赖,求实现方案？

A-->B-->C


- 模拟代码API

```js
const readFile = function (fileName) {
    return new Promise((resolve, reject) => {
        $.get(fileName).then(_ => {
            resolve(_);
        });
    });
}
```

- promise：
```js
    //promise
    readFile('data/a.txt').then(res => {
        console.log(res);
        return readFile('data/b.txt');
    }).then(res => {
        console.log(res);
        return readFile('data/c.txt');
    }).then(res => {
        console.log(res);
    })
```
- generator
```js
    function* gen() {
        yield readFile('data/a.txt');
        yield readFile('data/b.txt');
        yield readFile('data/c.txt');
    }
    let g1 = gen();

    g1.next().value.then(res => {
        console.log(res);
        return g1.next().value;
    }).then(res => {
        console.log(res);
        return g1.next().value;
    }).then(res => {
        console.log(res);
    })
    //补充语法
//定义：
		function * gen(){
		    yield 'aaa';
		    yield 'bbb';
		    return 'ccc';
		}
//调用:
		let obj = gen();
		obj.next();  // {value:'aaa', done:false}
		obj.next();  // {value:'bbb', done:false}
		obj.next();  // {value:'ccc', done:true}
```
- async
```js
    async function getfile(){
        let f1 = await readFile('data/a.txt');
        let f2 = await readFile('data/b.txt');
        let f3 = await readFile('data/c.txt');
        console.log(f1);
        console.log(f2);
        console.log(f3);
    }
    getfile();
```

###  作用
- 解决深度嵌套
```js 
    async function fn(){  //表示异步，这个函数里面有异步任务
        let result = await  xxx	//表示后面结果需要等待
    }  
``` 
```js
   function fn(n){
        return new Promise(resolve=>{
            setTimeout(()=>{
                resolve(n);
            },1000);
        });
    }
    async function show(){
        var a=await fn(2);
        var b=await fn(3);
        return a+b;
    }
    show().then(res=>{
        console.log(res);
    });
```

###  特点
    1. await只能放到async函数中
	2. 比genrator语义化更强
	3. await后面可以是promise对象，也可以数字、字符串、布尔
	4. async函数返回是一个promise对象
    5. 只要await语句后面Promise状态变成 reject, 那么整个async函数会中断执行
###  验证async 返回的是否是promise对象
```js
    async function fn(){
        return 'async';
    }

    fn().then(res=>{
        console.log(res);
    })
```

###  验证await语句后面Promise状态变成 reject
```js
    async function fn2() {
      await Promise.reject('this is error');
      let a = await Promise.resolve('this is success');
      console.log(a);
    }

    fn2().then(res => {
      console.log(res);
    }).catch(err => {
      console.log(err);
    })
```
###  如何解决async函数中抛出错误，影响后续代码
```js
    try{
		let f1 = await readFile('data/a.txt');
		let f3 = await readFile('data/c.txt');
		let f2 = await readFile('data/b.txt');
	}catch(e){}
```

###  业务代码展示
```js
async getInfo(appCode) {
    const oInfo = await approvalAPI.getProppserInfo({
        appCode
    });
    const oMsg = await thirdAPI.loanerGZT({
        appCode,
        type: 2,
        params: `${oInfo.data.proppserName},${oInfo.data.idno}`
    });
    this.checkData.proppserName = oMsg.data.compResult;
},
```




