# koa服务端交互-mongo
<!-- author:jingxian.liang@mljr.com -->

## restful-api

- GET：读取（Read）
- POST：新建（Create）
- PUT：更新（Update）
- PATCH：更新（Update），通常是部分更新
- DELETE：删除（Delete）

[相关资料-RESTful API 最佳实践](http://www.ruanyifeng.com/blog/2018/10/restful-api-best-practices.html)

## 相关依赖

- [koa-json-error](https://www.npmjs.com/package/koa-json-error)
解决400 500 等状态码错误以JOSN的相识抛出

```javascript
{
    "message": "Not Found",
    "name": "NotFoundError",
    "status": 404
}
```

- [koa-parameter](https://www.npmjs.com/package/koa-parameter)
 参数校验组件

```javascript
    ctx.verifyParams({
    name: {
        type: 'string',
        required: true
    },
    password:{
        type:'string',
        require:true
    }
    })
```

- [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken)
签名token验证  JWT

- [koa-jwt](https://www.npmjs.com/package/koa-jwt)
token认证权限

## MongoDB

- NoSQL 非关系型数据库的统称  （MongoDB=>文档存储-json,Redis=>key-value）
- 使用目的-简单-so easy
- 拓展性高-适合超大规模存储
- 优点  性能好，大规模存储，安全，适合存储复杂结构的数据（schema free）
- [MongoDB云（https://docs.atlas.mongodb.com/)](https://docs.atlas.mongodb.com/)

 注册 创建集群  添加数据库用户  设置白名单IP  获取链接地址

```
 mongodb+srv://femans:<password>@femans-xpvff.mongodb.net/test?retryWrites=true&w=majority 
```

```javascript
    const mongoose = require('mongoose')

    const {Schema,model} = mongoose
    const userSchema = new Schema({
        name:{type:String,required:true},
        age:{type:Number,default:0}
    })
    // 集合名称 ，schema
    module.exports = model('User', userSchema)

 ```

- 增 new User(ctx.request.body).save()
- 删 User.findByIdAndRemove(ctx.params.id)
- 改 User.findByIdAndUpdate(ctx.params.id,ctx.request.body)
- 查  User.find()，User.findById(ctx.params.id)，User.findOne({name})
  
`.select('+password')`,  `populate('producter')`

- [mongoose中文文档](http://mongoosejs.net/docs/guide.html)

## 使用jwt

```javascript
const jwt = require('jsonwebtoken')
//生成  sign（内容，秘钥，过期时间）
const token = jwt.sign({name:'femans'},'password',{expiresIn:'1d'})
// eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYW1lIjoiZmVtYW5zIiwiaWF0IjoxNTYzOTYyMTI1fQ._hWjRXf6uwYeS8Jtj4c7r40lT8ioYy7UDSGSc14pUPU
//验证
jwt.verify(token,'password')
//{ name: 'femans', iat: 1563962238 }
```

## 用户信息数据设计
![avatar](http://assets.processon.com/chart_image/5d3a7162e4b0511f13138978.png)

## 用户关系
![avatar](http://assets.processon.com/chart_image/5d3a73b4e4b0e6d9199ce74b.png)
