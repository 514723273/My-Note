# Node.js web server 博客项目

## 1 nodejs 介绍

## 1.1 nodejs 和 js 的区别

### 1.1.1 ECMAScript

- 不能操作 DOM ，不能监听 click 事件，不能发送 ajax 请求
- 不能处理 http 请求，不能操作文件

即，只有 ECMAScript ，几乎做不了任何实际的项目

### 1.1.2 javascript

- 使用 ECMAScript 语法规范，外加 Web API ，缺一不可
- DOM 操作， BOM 操作，事件绑定， Ajax 等

两者结合，即可完成浏览器端的任何操作

### 1.1.3 nodejs

- 使用 ECMAScript 语法规范，外加 nodejs API ，缺一不可
- 处理 http ，处理文件等，具体 参考 [Node.js v10.16.0 文档](http://nodejs.cn/api/)

二者结合，即可完成 server 端的任何操作

## 1.2 nodejs 处理 http 请求

### 1.2.1 http 请求概述

从你在浏览器中输入域名，敲击回车，显示页面中，都经历了什么？

1. DNS 解析，TCP 三次握手建立连接，发送 HTTP 请求
2. server 接收到 http 请求，处理，并返回
3. 客户端接收到返回数据，处理数据（如渲染页面，执行 js）

### 1.2.2 nodejs 处理 get 请求

```js
const http = require('http');
const querystring = require('querystring');             // !!处理 url 传递的参数

const server = http.createServer((req, res) => {
    console.log('method: ', req.method);                // 请求的方式
    console.log('url: ', req.url);                      // 请求的地址

    req.query = querystring.parse(req.url.split('?')[1]);   // localhost:8000?username=123&password=456 // querystring 解析的是问号后的([1])，即 username=123&password=456
    console.log('query: ', req.query);
    
    res.setHeader('Content-type', 'application/json');

    res.end(                                            // 返回写入
        JSON.stringify(req.query);                      // 返回的永远是字符串（但可以通过设置 res 的 Header 来设置浏览器解析的格式
    );
});

server.listen(8000);
console.log('OK');
```

### 1.2.3 nodejs 处理 post 请求

```js
const http = require('http');

const server = http.createServer((req, res) => {
    if(req.method === 'POST') {
        console.log('content-type', req.headers['content-type']);   // 数据格式

        // 接收数据（一部分一部分接收）（数据流）
        let postData = "";
        req.on('data', chunk => {       // 每次来数据，都会被触发
            postData += chunk.toString()
        });
        req.on('end', () => {           // 结束后，触发 end 事件
            console.log(postData);
            res.end('hello world!');    // 在这里返回，因为是异步的
        });
    }
});

server.listen(8000);
console.log('OK');
```

## 2 搭建开发环境

1. 使用 nodemon 检测文件变化，自动重启 node
2. 使用 cross-env 设置环境变量
3. 修改 package.json 中的 scrpits
```js
//package.json
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "cross-env NODE_ENV=dev nodemon ./bin/www.js",
    "prd": "cross-env NODE_ENV=production nodemon ./bin/www.js"
}
```

4. 启动项目
```
npm run dev
```

## 3 登录

### 3.1 cookie 介绍

#### 3.1.1 什么是 cookie

- 存储在浏览器的一段字符串（最大 5kb）（最好只存放关键信息）
- 跨域不共享
- 格式如 k1=v1;k2=v2;k3=v3; 因此可以存储结构化数据
- **每次发送 http 请求，会将请求域的 cookie 一起发送给 server**
- server 可以修改 cookie 并返回给浏览器
- 浏览器也可以通过 javascript 修改 cookie （有限制）

#### 3.1.2 客户端查看 cookie 的三种方式

- Console -> Network -> Headers -> Request Headers
- Console -> Application -> Storage -> Cookies
- Console 输入 `document.cookie` ，也可以使用 `document.cookie = "k1=v1;k2=v2"` 在 cookie 中累加键值对。

### 3.2 session 介绍

单用 cookie 确实可以完成标识用户操作，但是有诸多不利之处。例如不能存放明文的用户信息（危险），大小限制。

所以，可以通过 cookie 中存储 userid，server 端对应 username ，即使用 session ，server 端存储用户信息。（多了一层在服务器的代理人）

#### 3.2.1 使用 `SESSION_DATA = {}` 用作 session 的问题

目前 session 直接是 js 变量，放在 nodejs 进程*内存*中
1. 进程内存有限，访问量过大，内存暴增怎么办？
2. 正式线上运行的是多进程，进程之间内存无法共享。（请求先被统一拦截，再被随机分配给其中一个比较空闲的进程处理）

操作系统会限制一个进程的最大可用内存。nodejs 在 32 位操作系统中内存限制为 1.6g ，在 64位中也不会超过 3g 。

所以，例如在一个内存为 8g 的服务器中，跑一个 nodejs 进程（占 2 g ，其他没用上）太浪费内存了，所以一台服务器上跑 3 个 nodejs （多核处理器可以并行处理多个进程），充分利用内存。（但是各自的内存不共享）

### 3.3 redis 介绍

- web server 最常用的缓存数据库，数据存放在内存中
- 相比于 mysql ，访问速度快（内存和硬盘不是一个数量级的）
- 但是成本更高，可存储的数据量更小（内存的硬伤）

#### 3.3.1 解决方案

- 将 web server 和 redis 拆分为两个单独的服务
- 双方都是独立的，都是可扩展的（例如都扩展成集群）
- （包括 mysql ，也是一个单独的服务，也可以扩展）

上小节中，js对象存储 session 的弊端由 redis 解决。

已经被拆出来了，session 不再存储在 nodejs 进程中，挤爆 nodejs 进程已经不存在了。并可以多个 nodejs 进程访问一个 redis 进程，就像访问 mysql 一样，数据存储在一个地方。

#### 3.3.2 为何 session 适合用 redis

- session 访问频率高，对性能的要求极高
- 可以不考虑断电丢失 session 的问题
- session 的数据量不会太大

#### 3.3.3 docker 内安装 redis

参考 [使用Docker安装Redis](https://www.jianshu.com/p/fef5e1a7bd0e)

#### 3.3.4 redis 的基本命令

- `redis-server` 启动 redis
- `redis-cli` 进入 redis 的命令行
- `set myname kiyonami` 设置键值对
- `get myname` 获得该 key 对应的 value
- `keys *` 获得所有的 key
- `del myname` 删除该 key value

### 3.4 启动前端服务器

```
npm install http-server -g

cd html-test

http-server -p 8001
```

### 3.5 nginx 介绍

[nginx: download](http://nginx.org/en/download.html)

- 高性能的 web 服务器，开源免费
- 一般用于做静态服务（图片之类）、负载均衡（均摊流量）
- 反向代理

（正向代理：一般是在客户端，用户可控请求。反向代理，一般在服务器，对于用户来说是个黑盒。）

#### 3.5.1 nginx 命令

- `nginx -t` 测试配置文件格式是否正确
- `nginx` 启动
- `nginx -s load` 重启
- `niginx -s stop` 停止

#### 3.5.2 nginx 配置

修改 nginx-1.16.0\conf\nginx.conf 文件。

访问 localhost:8080 ，会根据 location 访问不同的地址。

如果 访问 localhost:8080 则转发至 http://localhost:8001

如果 访问 localhost:80880/api/ 则转发至 http://localhost:8000 ，此时设置了`proxy_set_header Host $host` ，即**允许重新定义或添加字段传递给代理服务器的请求头**。

[nginx](./img/nginx.png)

```
# 注意键值对 没有冒号

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       8080;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        # location / {
        #     root   html;
        #     index  index.html index.htm;
        # }

        # 新增两个代理
        location / {
            proxy_pass http://localhost:8001;
        }
        location /api/ {
            proxy_pass http://localhost:8000;
            proxy_set_header Host $host;
        }
```

### 3.6 登录流程

1. 输入账号密码，使用 POST 方式发送至服务器。
2. 服务器核验账号密码通过后，通过 `res.setHeader('Set-Cookie', \`username=${data.username};path=/; httpOnly; expires=${getCookieExpires()}\`)` 设置 cookie 。（第一个为 cookie key value 值，后三个为设置 cookie 属性）
3. 访问域名下的其他页面，会自动发送 cookie

path 用于设置该 cookie 的作用域。

httpOnly 限制前端修改 cookie ，只允许后端修改。（虽然可以继续操作 document.cookie ，但是无法覆盖之前的值 例如：k1=v2;k1=v1 服务器读取，依旧只会用后者覆盖前者）

过期时间 Expires 1969 年就是没有设置浏览器的过期时间。（项目中，只控制了浏览器的 cookie 过期。即使浏览器过期，并没有把服务器中 redis 中旧的 session 删除（所以可能不断堆积废旧的 session））



### 3.7 文件分层介绍

src/router 确定路由，根据 request 不同的 path 和 method ，处理基本的参数，进行不同的处理。这里分别处理 blogRouter 和 userRouter ，再被 app.js 引入，传入 req 和 res 处理，直到有一个 router 可以处理并返回信息。

src/controller 新建 blog.js 和 user.js ，拿到 router 传递的参数，分别处理不同路由下的业务逻辑。

src/model/resModel 创建两个model，用于固定返回 success 和返回 error 的 response 格式。

src/config 新建 db.js 放置数据库的连接信息，根据不同的环境选择不同的配置。 `const env = process.env.NODE_ENV;`




### 临时笔记

数据库的使用

varchar(10) 可以存储 10个数字 10个英文字母 10个汉字 没有问题！已统一！

当使用修改操作的时候可能会报错 SET SQL_SAFE_UPDATES = 0

数据库中无法使用汉字，首先先查询数据库的字符设置，再查询列的字符设置。

promise 防止回调地狱（不断嵌套）（同时分配线程处理？
