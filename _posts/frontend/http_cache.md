# http缓存
<!-- author:lingke.wang@mljr.com -->

都是服务端通过配置`http headers`来告诉浏览器是否能缓存，以及缓存多长时间。

强缓存：不去请求服务端，直接从本地缓存读取资源。

协商缓存：请求服务端，返回`304`，然后从本地缓存读取资源。

## 强缓存

浏览器不会去请求服务器，主要通过`expires`和`cache-control`控制。

`nginx`通过配置`expires`指令，来控制缓存有效期。

``` nginx

location ~ .*\.(js|css)?$
{
    expires      1d; # 缓存一天
}

```

配置成功后，会在`Response Headers`中添加`Expires`和`Cache-control`字段。

如上述缓存一天的配置，最终的`Response Headers`会是这样：

![](https://ws1.sinaimg.cn/large/006tKfTcly1fsrx2eymasj30as06qjrf.jpg)

`Cache-control`的值表示最大缓存时间，即一天(`60 * 60 * 24 = 86400秒`)，是一个相对值。

`Date`为当前请求的时间。

`Expires`为缓存过期时间。

`Expires`是`http1.0`的功能，为一个绝对时间。如果浏览器的时间小于`Expires`的时间，则命中强缓存，直接从缓存读取资源。不过浏览器和服务端的时间可能会出现比较大的误差，所以`http1.1`新增了`Cache-control`。

浏览器在第一次请求成功之后，会将该`Response Headers`缓存。下次请求的时候，浏览器会先从缓存的`Response Headers`中读取`Date`和`Cache-control`的值计算出缓存有效期。如果浏览器的时间没有超过缓存有效期，则命中强缓存，直接从缓存读取资源。



> 优先级`Cache-control` > `Expires`

### from disk cache && from memory cache

![](https://ws4.sinaimg.cn/large/006tKfTcly1fsrxq08idqj308l020wec.jpg)

![](https://ws1.sinaimg.cn/large/006tKfTcly1fsrxp3jrrjj308y01uwec.jpg)

> Chrome employs two caches — an on-disk cache and a very fast in-memory cache. The lifetime of an in-memory cache is attached to the lifetime of a render process, which roughly corresponds to a tab. Requests that are answered from the in-memory cache are invisible to the web request API. If a request handler changes its behavior (for example, the behavior according to which requests are blocked), a simple page refresh might not respect this changed behavior. To make sure the behavior change goes through, call handlerBehaviorChanged() to flush the in-memory cache. But don't do it often; flushing the cache is a very expensive operation. You don't need to call handlerBehaviorChanged() after registering or unregistering an event listener.

内存缓存是和渲染进程绑定的，大部分情况下与浏览器Tab对应。

## 协商缓存
主要通过`ETag`和`Last-Modified`来控制。

`nginx`默认都是[开启](http://nginx.org/en/docs/http/ngx_http_core_module.html#etag)状态。

静态资源默认都会打上`ETag`，通过添加`etag off`指令禁止生成`ETag`。

`Last-Modified`表示此文件在服务期端最后被修改的时间。


<img src="https://ws3.sinaimg.cn/large/006tKfTcly1fss25hltwwj30lu0iw3zc.jpg" width="400">


`Last-modified` / `If-Modified-Since`: 浏览器首先请求资源时，服务端在`Response Headers`返回请求的资源上次修改时间，就是`Last-modified`，浏览器会缓存下这个时间。然后浏览器在下次请求中，`Request Headers`中会带上`If-Modified-Since`(保存的`Last-modified`的值)。服务端接收到这个值后，会与资源的修改时间做对比，如果一致，则返回`304`，浏览器从缓存读取资源。

`last-modified`依赖的是一个绝对时间值，可能各机器读到的时间不一致，就会出现误差，为了改善这个问题，就有了`ETag`。


`ETag` / `If-None-Match`: `HTTP协议`定义`ETag`为"被请求变量的实体值"，也可以理解为Web资源关联的记号(token)。

生成`ETag`常用的方法包括对资源内容使用抗碰撞散列函数，使用最近修改的时间戳的哈希值，甚至只是一个版本号。和last-modified一样，浏览器会先发送一个请求得到`ETag`的值，然后再下一次请求在`Request Headers`中带上`If-None-Match`(保存的`ETag`的值)。通过这个值进行对比，如一致，则返回`304`，浏览器从缓存读取资源。


![](https://images2017.cnblogs.com/blog/292443/201802/292443-20180205175852779-1749419397.png)

<br />
<br />
<br />






### 参考资料

[http缓存](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=zh-cn)


[Node.js描述 HTTP 中最基本的缓存机制](https://harttle.land/2017/04/04/using-http-cache.html)