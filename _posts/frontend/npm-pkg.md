# npm包发布
<!-- author:jingxian.liang@mljr.com -->
## npm注册
**1.注册(输入用户名，密码，邮箱。注册成功会自动登录)**
```bash
npm adduser
```
**2.登录(输入用户名，密码就可登陆成功)**
```bash
npm login
```
**3.发布(注意发布必须是登录状态下)**
```bash
npm publish
```
## mljr-cli
```bash
npm install mljr-cli -g
```
```bash
➜ ~ mljr -h

  Usage: mljr <command> [options]

  Options:

    -V, --version  output the version number
    -h, --help     output usage information

  Commands:

    list|l         显示mljr前端使用的模板列表
    init|i         创建新的项目
```
```bash
➜ ~ mljr list
  ★  fe-quick-cms-spa -基于vue-cli和elem框架快速开发cms项目
  ★  fe-quick-h5-spa -基于vue-cli快速开发H5端项目
```
```
➜  ~ mljr init
    可用模板列表:
     0 : fe-quick-cms-spa
     1 : fe-quick-h5-spa
    请选择模板类型:1
⠦    正在构建，客官請稍等......     ★ 项目构建成功
```
## 实例代码
**目录结构**
```
  ├── bin                           node命令执行目录
  ├── ├── mljr.js                   命令执行入口
  │── index.js                      项目入口文件
  ├── src                           项目源码目录    
  │   ├── init.js                   初始化业务
  │   ├── list.js                   列表业务
  │   ├── templates.js              cli-模板
  │── package.json                  
```
**bin/mljr.js**
```
#!/usr/bin/env node

require('../index').main()
```
**package.json**
```
"bin": {
  "mljr": "./bin/mljr.js"
},
```
**index.js**
```js
let program = require('commander'); //命令插件
let init = require('./src/init');
let list = require('./src/list')
function main() {
    program
        .version("1.0.7")
        .usage('<command> [options]');

    program.command('list')
        .description("显示mljr前端使用的模板列表")
        .alias('l')
        .action(function () {
            list();
        })
    program.command('init (template)')
        .description("创建新的项目")
        .alias('i')
        .action(function (template) {
            if (typeof template === "object") {
                init(0);
            } else {
                init(template);
            }
        })
    program.parse(process.argv);
    if (program.args.length == 0) {
        //这里是处理默认没有输入参数或者命令的时候，显示help信息
        program.help();
    }
}
exports.main = main
```
**src/list.js**
```js
const chalk = require('chalk'); //终端输出时颜色样式输出工具
const templates = require("./templates.js");
module.exports = function () {
    for (let key in templates.list) {
        let temp = templates.list[key]
        console.log(
            '  ' + chalk.green('★') +
            '  ' + chalk.green(temp.name) +
            ' -' + temp.desc)
    };
    if (!templates.list || templates.list.length == 0) {
        console.log(chalk.yellow('当前无可用模板'))
    }
}
```
**src/templates.js**
```js
module.exports = {
    list: [{
        "name": "fe-quick-cms-spa",
        "path": "git@gitlab.mljr.com:FEApply/fe-quick-cms-spa.git",
        "desc": "基于vue-cli和elem框架快速开发cms项目"
    }, {
        "name": "fe-quick-h5-spa",
        "path": "git@gitlab.mljr.com:FEApply/fe-quick-h5-spa.git",
        "desc": "基于vue-cli快速开发H5端项目"
    }]
}
```
**src/init.js**
```js
const co = require('co'); //异步控制
const prompt = require('co-prompt');//命令异步处理
const chalk = require('chalk');
const templates = require("./templates");
const ora = require('ora'); // loading icon
const exec = require('child_process').exec   //子进程
let temps = {};
let temps2 = {};
module.exports = function (name) {
    getTemps(templates);
    co(generator(name));
}
function getTemps(templates) {
    for (let key in templates.list) {
        let item = templates.list[key]
        temps[key] = item.name;
        temps2[item.name] = item.path;
    };
}

let generator = function* (name) {
    let tempName = name;
    let path = temps2[name];
    if (!name) {
        console.log('    可用模板列表:')
        for (let key in temps) {
            let tempName = temps[key]
            console.log(
                '     ' + chalk.green(key) +
                ' : ' + chalk.green(tempName))
        };
        tempName = yield prompt("    请选择模板类型:")
        path = temps2[temps[tempName]];
        downloadTemplates(path);
    }
    else {
        console.log(chalk.red(`   ✘模版[${tempName}]不存在`))
        process.exit(0);
    }
}

function downloadTemplates(path) {
    let spanner = ora("   正在构建，客官請稍等......");
    spanner.start();

    // git命令，远程拉取项目并自定义项目名
    let cmdStr = `git clone ` + path

    // 在nodejs中执行shell命令，第一个参数是命令，第二个是具体的回调函数
    exec(cmdStr, (error, stdout, stderr) => {
        if (error) {
            console.log(error)
            process.exit(0)
        }
        startBuildProject(spanner)
    })
}

function startBuildProject(spanner) {
    console.log('    ', chalk.green('★'), chalk.green('项目构建成功'));
    spanner.stop();
    process.exit(0);
}
```

