# koa服务端交互(mysql)
<!-- author:jingxian.liang@mljr.com -->


## 安装&使用
```javascript
// 命令行安装
brew install mysql

bash mysql.server start

//官网下载  https://dev.mysql.com/downloads/mysql/

mysql -u root -p

```
- [可视化管理工具navicat](https://www.navicat.com.cn/) 
- [sql语句](http://www.runoob.com/mysql/mysql-administration.html)
  
## 建立数据库连接池
```javascript
const mysql = require('mysql');

const pool = mysql.createPool({
    host: '127.0.0.1' //服务ip地址
    port: '3306',//端口号
    user: 'root',//用户名
    password: 'password',//密码
    database: 'nodesql'//数据库
});

pool.getConnection(function(err, connection) {
    if(err) {
        console.log('MySQL数据库建立连接失败。')
    } else {
        console.log('数据库建立连接成功。');
        connection.query( sql, function(err, data) {
            if(err) console.log('查询数据操作失败。');
            else{
                console.log(data);
                connection.release()
            }
        });
    }
});
```


#### 数据库连接池代码封装
```javascript
let query = function (sql, values) {
    return new Promise((resolve, reject) => {
        pool.getConnection(function (err, connection) {
            if (err) {
                resolve(err)
            } else {
                connection.query(sql, values, (err, rows) => {
                    if (err) {
                        reject(err)
                    } else {
                        resolve(rows)
                    }
                    connection.release()
                })
            }
        })
    })
}
```
## 交互流程
  ![avatar](https://www.femans.com/images/koa-service.png)

### 项目结构
```
  ├── db                            数据库配置目录
  ├── controller                    sql查询服务目录
  │── routes                        接口路由导出目录
  |—— upload                        上传文件目录
  ├── utils                         工具类目录
  │── app.js                        koa主入口js     
```

### 接口DEMO
  
**1.tips状态管理**
```javascript
const Tips  = {
    0: {
        code: 0,
        msg: '操作成功'
    },
    1002: {
        code: 1002,
        msg: '系统繁忙！',
    },
    .....
}    
```
**2.查询sql**
```javascript
// 查询总数
let findUserCount = function(params) {
  let { id } = params;
  let sql = `SELECT count(*) FROM user `;
  if (id) {
    sql += ` WHERE id = ?  `;
  }
  let value = [id];
  return query(sql, value);
};

// 查询分页列表
let findUser = function(params) {
  let { name, pageSize, page } = params;
  let offset = (page - 1) * pageSize;
  let sql = `SELECT user.id,user.user_name,user.user_email,upload.file_name,upload.file_path FROM user  LEFT JOIN upload ON user.id = upload.user_id  `;

  if (name) {
    offset = 0;
    sql += ` WHERE user.user_name = ?  `;
  }
  sql += ` limit ${offset},${pageSize} ORDER BY user.create_time DESC `;

  let value = [name];
  return query(sql, value);
};
```
### 接口调试
 - 使用工具调试如postman    
 - curl 调试
 ```javascript
 //get
 curl http://localhost:3000/xxxxx?key=value&key2=value2

 //post
 curl -d "key=value&key2=value2" http://localhost:3000/xxxxx
 ```


   

**3.接口**
```javascript
// 查询
router.post('/koa-api/user/list', async (ctx, next) => {
    let params = ctx.request.body;
    let oCount = await userDTO.findUserCount(params);
    await userDTO.findUser(params).then(async res => {
        if (res && res.length > 0) {
            ctx.body = { ...Tips[0],
                data: {
                    list:res,
                    page: params.page,
                    pageSize: params.pageSize,
                    totalCount: oCount[0]['count(*)']
                }
            };
        } else {
            ctx.body = { ...Tips[0],
                data: []
            };
        }
    }).catch(e => {
        console.log(e)
        ctx.body = { ...Tips[1008],
            data: []
        };
    })
});


//上传用户头像
const fs = require('fs');
const path = require('path');
const asyncBusboy = require('async-busboy');
router.post('/koa-api/upload/user',async (ctx,next)=>{
    const {
        files,
        fields
    } = await asyncBusboy(ctx.req);
    // 判断文件数量
    if (files.length === 0) {
        return ctx.body = Tips[1002]
    } else {
        let file = files[0];
        // 判断图片类型
        if (file.mimeType.indexOf('image') === -1) {
            return ctx.body = Tips[1002]
        }
        // 重置图片名
        let imgName = new Date().getTime() + '.' + file.filename.split('.').pop();
        let name = file.filename.split('.').shift();
        // 将图片放在upload目录下
        let savePath = path.join(__dirname, `../upload/${imgName}`);
        // console.log(data)
        let params = {
            file_name: name,
            file_path: `http://${ctx.request.host}/upload/${imgName}`,
            user_id: fields.user_id
        }
        // 存储图片
        let saveImg = function () {
            let img = fs.readFileSync(file.path);
            fs.writeFileSync(savePath, img);
            fs.unlinkSync(file.path); //清除缓存文件
            ctx.body = {
                ...Tips[0],
                data: {
                    name
                }
            };
        }
        let oUserImg = await userDTO.findUserImgCount(params.user_id);
        if (oUserImg[0]['count(*)'] === 0) {
            await userDTO.uploadUserImg(params)
            saveImg()
        }else{
            await userDTO.updateUserImg(params) 
            saveImg()
        }   
    }
})
```

 
 
### [项目地址](http://gitlab.mljr.com/FEApply/koa-service-mysql)
