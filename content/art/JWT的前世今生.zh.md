---
date: 2024-01-17T21:23:15+08:00
title: "JWT 的前世今生"
description: "JSON Web Tokens"
tags: ["Web", "JWT"]
series: ["The history of JWT"]
---

# JWT 的前世今生

## 发展史

- 在 Web 发展史中, 浏览器和服务器之间的是通过 HTTP 协议交流的, 但 HTTP 协议最重要的特点之一就是无状态
- 最开始 Web 基本就是文档的浏览, 所以服务端无需知道是谁在请求, 客户端每次请求都是一个新的 HTTP 协议
- 随着交互式 Web 应用的兴起, 例如购物商城网站, 社区形式的网站… 都是这些需要登录的网站, 我们要知道哪些人买了哪些商品,
  哪些人往自己的购物车放了哪些商品…
- 由于 HTTP 是无状态的协议, 一旦客户端和服务器的数据交换完毕, 就会断开连接, 再次请求, 会重新连接,
  这就说明服务器单从网络连接上是没有办法知道用户身份的。为了解决这个问题, 就给每次新的用户请求时, 发一个身份证,
  每次访问都要带上身份证, 这样服务器就知道是谁来访问了, 针对不同的用户做出不同的响应。

> 会话跟踪是Web程序中常用的技术, 用来跟踪用户的整个会话。
> 常用的会话跟踪技术是Cookie与Session。Cookie 通过在客户端记录信息确定用户身份, Session 通过在服务器端记录信息确定用户身份。

## Cookid 和 Session

### Cookie

- HTTP Cookie(也叫 Web Cookie或浏览器 Cookie)是服务器发送到用户浏览器并保存在本地的一小块数据,
  它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。
- 通常, 它用于告知服务端两个请求是否来自同一浏览器, 如保持用户的登录状态。Cookie 使基于无状态的 HTTP 协议记录稳定的状态信息成为了可能

> cookie 是浏览器访问服务器后, 服务器传给浏览器的一段数据, 是浏览器提供的一种机制。
> 浏览器需要保存这段数据, 不得轻易删除。
> 此后每次浏览器访问该服务器, 都必须带上这段数据。

#### 创建和删除

- 当前 Cookie 有两个版本, 分别对应两种设置响应头`Set-Cookie`和`Set-Cookie2`。
- 在 Servlet 中并不支持 `Set-Cookie2`, 所以这里只说 `Set-Cookie`(详见Set-Cookie
  MDN)：`Set-Cookie: value[; expires=date][; domain=domain][; path=path][; secure]`

| 属性                                                                 |                                                                                                                    属性介绍                                                                                                                     |
|--------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
| Set-Cookie: <cookie-name>=<cookie-value>                           |                                          1.键值对, 可以设置保存的 Key/Value, 这里 NAME 不能和其他属性项名字一样 <br>2. name=value 键值对, 设置 cookie 的名称及相对应的值, 都必须是字符串类型,如果值为 Unicode 字符, 需要为字符编码。如果值为二进制数据, 则需要使用 BASE64 编码。                                          |
| Set-Cookie: <cookie-name>=<cookie-value>; Expires=<date>           |                                                                                               过期时间, 在这个时间点后 cookie 失效, 形式为符合 HTTP-date 规范的时间戳                                                                                               |
| Set-Cookie: <cookie-name>=<cookie-value>; Max-Age=<non-zero-digit> | 1. 在 cookie 失效之前需要经过的秒数。一位或多位非零(1-9)数字。假如二者 (指 Expires 和 Max-Age) 均存在, 那么 Max-Age 优先级更高。<br>2. cookie 失效的时间, 单位秒。如果为整数, 则该 cookie 在 maxAge 秒后失效。如果为负数, 该 cookie 为临时 cookie , 关闭浏览器即失效, 浏览器也不会以任何形式保存该 cookie 。如果为 0, 表示删除该 cookie , 默认为 -1。 |
| Set-Cookie: <cookie-name>=<cookie-value>; Domain=<domain-value>    |                                                                                生成cookie域名,指定 cookie 可以送达的主机名。假如没有指定, 那么默认值为当前文档访问地址中的主机部分(但是不包含子域名)。默认是当前域名。                                                                                |
| Set-Cookie: <cookie-name>=<cookie-value>; Path=<path-value>        |                                                                       cookie 在哪个路径(路由)下生效, 指定一个 URL 路径, 默认是 ‘/’。如果设置为 /abc, 则只有 /abc 下的路由可以访问到该 cookie, 如：/abc/read。                                                                        |
| Set-Cookie: <cookie-name>=<cookie-value>; Secure                   |                                                                    加密设置, cookie只有在请求使用安全协议SSL或者HTTPS的时候才会被发送到服务器；默认为false。(注意：非安全站点(http:)已经不能再在 cookie 中设置 secure 指令了)                                                                     |
| Set-Cookie: <cookie-name>=<cookie-value>; HttpOnly                 |          1. 设置了 HttpOnly 属性的 cookie 不能使用 JavaScript 经由 Document.cookie 属性、XMLHttpRequest 和 RequestAPIs 进行访问, 以防范跨站脚本攻击(XSS)。<br>2. 如果给某个 cookie 设置了 httpOnly 属性, 但还是能通过 Application 中手动修改 cookie, 所以只是在一定程度上可以防止 XSS 攻击, 不是绝对的安全.           |

##### 创建

1. 通过 `Set-Cookie` 响应头创建
    ```Java
    if (true) {
        response.setHeader('Set-Cookie', `login_email=${email}`)
        response.statusCode = 200
        response.write('success')
    }
    ```
2. node 中的 cookie 操作
    ```Shell
    const Koa = require('koa')
    const Router = require('koa-router')
    const static = require('koa-static')
    const app = new Koa();
    const router = new Router();
    app.use(static(__dirname,'/'))

    router.get('/',async ctx=>{
        //观察cookie存在
        console.log('cookie:',ctx.header.cookie)
        //设置cookie
        ctx.set('Set-Cookie','cookie1=1234')
    })

    app.use(router.routes())
    app.listen(1024,() => {
        console.log('1024已开启');
    })
    ```
3. Javascript 操作 cookie
    - 目前 document.cookie 对 cookie 的操作很不友好, 所以在项目中一般会引入第三方插件库来操作 cookie
    ```Javascript
    var dates=new Date();
    dates.setDate(dates.getDate()+3); // 按天数设置
    document.cookie=”user1=YY; expires=”+dates;
    ```

![cookie_add.png](..%2F..%2F..%2Fstatic%2Fimages%2FJWT%2Fcookie_add.png)

##### 删除

- 修改 cookie 有效期, 使有效期早于当前时间, 就可以删除 cookie
    ```Javascript
     var delete_cookie = function(name) {
         document.cookie = name + '=;expires=Thu, 01 Jan 1970 00:00:01 GMT;';
      };
    ```

#### Domain 的应用

- 以公司内部统一 Auth 登录中心为例

> 假设现在有两个域名：
> - 域名 A: a.test.f.com.cn
> - 域名 B: b.test.f.com.cn
>
> 显然, 域名 A 和域名 B 都是 f.com.cn 的子域名
> 1. 如果我们在域名 A 中的 cookie 的domain设置为 f.com.cn, 那么 f.com.cn 及其子域名都可以获取这个 cookie,
     即域名A和域名B都可以获取这个 cookie。
> 2. 如果域名 A 和域名 B 同时设置 cookie 的 doamin 为 f.com.cn, 那么将出现覆盖的现象。
> 3. 如果域名 A 没有显式设置 cookie 的 domain 方法, 那么 domain 就为 a.b.f.com.cn, 不一样的是, 这时, 域名 A 的子域名将无法获取这个
     cookie。

#### 特性

##### 分类

1. 会话级别cookie: 所谓会话级别cookie, 就是在浏览器关闭之后cookie就会失效。
2. 持久级别cookie: 保存在硬盘的cookie, 只要设置了过期时间就是硬盘级别cookie。

##### 特点

1. 域和路径：cookie 不可跨域, cookie可以跨越同域名下的多个网页, 实现跨页面全局变量。
2. 时间：Cookie 的默认有效期理论上在用户关闭页面后就失效, 实际上在在20分钟左右,
   不同浏览器策略不同。但是后端可以强制设置有效期(如何设置见下文)。
3. 空间：cookie存在客户端, 只能存储4kb。
4. 数量：一个浏览器针每个域最多存 20 个cookie, 浏览器一般只允许存放 300 个cookie。
5. 储存类型：cookie只能存储字符串。
6. 无感知: cookie 可以借助 HTTP 头、浏览器的能力做到做到前端无感知传输数据。

##### 缺点

1. 不安全性——cookie很容易被用户篡改。(session 可以解决这个问题, 防止用户篡改)
2. 储存空间——cookie存储空间很小。(只有4kb左右)
3. 数量有限——一个浏览器针每个域最多存 20 个cookie, 浏览器一般只允许存放 300 个cookie。
4. cookie可能被客户禁用。

#### 应用场景

- cookie的主要应用场景是以下三方面：
    - 会话状态管理(如用户登录状态、购物车、游戏分数或其它需要记录的信息)
    - 个性化设置(如用户自定义设置、主题等)
    - 浏览器行为跟踪(如跟踪分析用户行为等)

> ex：用户登录识别(实际会更谨慎)
>
> 1. 用户 A 用浏览器访问了 http://a.com, 那么 http://a.com 的服务器就会立刻给 A 返回一段数据「uid=1」(cookie)。
> 2. A 把这段数据「uid=1」(cookie)储存在本地。 
> 3. 当 A 再次访问 http://a.com 的其他页面时, 就会附带上「uid=1」这段数据。
> 4. 这样服务端就能识别 A 用户的具体信息。

#### 需要注意的问题
- 因为存储在客户端，容易被客户端篡改，使用前需要验证合法性 
- 不要存储敏感数据，比如用户密码，账户余额 
- 使用 httpOnly 在一定程度上提高安全性 
- 尽量减少 cookie 的体积，能存储的数据量不能超过 4kb 
- 设置正确的 domain 和 path，减少数据传输 
- cookie 无法跨域(关于 cookie 跨域可以看一下 withCredentials)
- 一个浏览器针对一个网站最多存 20 个 cookie，浏览器一般只允许存放 300 个 cookie 
- 移动端对 cookie 的支持不是很好，而 session 需要基于 cookie 实现，所以移动端常用的是 token

### Session

