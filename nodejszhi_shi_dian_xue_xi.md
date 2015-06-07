# Node.js知识点学习

> 一、基本概念

`Node.js`，或者 Node，是一个可以让 `JavaScript` 运行在服务器端的平台。可以说，`Node.js`开创了`javascript`模块化开发的先河，早期的`javascript`需求都很简单，基本都是写成函数的，然后是面向过程的写法，后来慢慢的引入面向对象开发思想，再后来就慢慢写成 类。最后node.js的出现才开始有了js模块化开发的概念，这使得臃肿的`js`代码免去了命名冲突等一系列开发难题。

`Node`最大的特点就是采用了异步式`I/O`与事件驱动的架构设计。`Node.js`是一个让js运行在浏览器外的平台。其最初目标是实现事件驱动，非阻塞`I/O`的web服务器。

`Node.js` 只是一个 `JavaScript` 的运行环境(或者说是一组库), 为标准 `js` 补充了有关异步 IO, 即读写网络和文件的功能。 一个库嘛，无非是调调 API 什么的，除了略反人类的事件回调之外，和其他后端语言(PHP, Python)也差不了多少。

`Node.js`使用的是单线程模式，每一个线程完成一个功能，一个进程可以有多个线程，对于所有的I/O都采用异步式的请求方式。每个异步式I/O请求完成后都会被推送到事件队列，等待程序进程进行处理。

总之，`node`的核心思想就是：非阻塞，单线程和事件驱动。（同步对应的是阻塞，异步对应的是非阻塞）

ebay选择`Node.js`的理由，可以归纳为以下4点：
    
    动态语言：开发效率非常高，并有能力构建复杂系统，如ql.io。
    性能和I/O负载：Nodejs非常好的解决了IO密集的问题，通过异步IO来实现。
    连接的内存开销：每个Node.js进程可以支持超过12万活跃的连接，每个连接消耗大约2K的内存。
    操作性：实现了Nodejs对于内存堆栈的监控系统。
    

`Node.JS`架构示意图

![](http://lszb811.qiniudn.com/zsd-1.png)

## Node.js可以做什么？

### Web开发：Express + EJS + Mongoose/MySQL
    
    express 是轻量灵活的Nodejs Web应用框架，它可以快速地搭建网站。
    Express框架建立在Nodejs内置的Http模块上，并对Http模块再包装，从而实际Web请求处理的功能。
    
    ejs是一个嵌入的Javascript模板引擎，通过编译生成HTML的代码。
    
    mongoose 是MongoDB的对象模型工具，通过Mongoose框架，可以进行访问MongoDB的操作。
    
    mysql 是连接MySQL数据库的通信API，可以进行访问MySQL的操作。
    

通常用`Node.js`做Web开发，需要3个框架配合使用，就像Java中的SSH。

### Web聊天室(IM)：Express + Socket.io

`socket.io`一个是基于Nodejs架构体系的，支持`websocket`的协议用于时时通信的一个软件包。`socket.io` 给跨浏览器构建实时应用提供了完整的封装，`socket.io`完全由`javascript`实现。

![](http://lszb811.qiniudn.com/zsd-2.png)

### Web爬虫：Cheerio/Request

`cheerio` 是一个为服务器特别定制的，快速、灵活、封装jQuery核心功能工具包。Cheerio包括了 jQuery核心的子集，从jQuery库中去除了所有DOM不一致性和浏览器不兼容的部分，揭示了它真正优雅的API。Cheerio工作在一个非常简 单，一致的DOM模型之上，解析、操作、渲染都变得难以置信的高效。基础的端到端的基准测试显示Cheerio大约比JSDOM快八倍(8x)。 Cheerio封装了@FB55兼容的htmlparser，几乎能够解析任何的 HTML 和 XML document。

### Web博客：Hexo

`Hexo` 是一个简单地、轻量地、基于`Node`的一个静态博客框架。通过`Hexo`我们可以快速创建自己的博客，仅需要几条命令就可以完成。

发布时，`Hexo`可以部署在自己的`Node`服务器上面，也可以部署`github`上面。对于个人用户来说，部署在`github`上好处颇多，不仅可以省 去服务器的成本，还可以减少各种系统运维的麻烦事(系统管理、备份、网络)。所以，基于`github`的个人站点，正在开始流行起来….

### 前端包管理平台: bower.js

Bower 是 `twitter` 推出的一款包管理工具，基于`nodejs`的模块化思想，把功能分散到各个模块中，让模块和模块之间存在联系，通过 Bower 来管理模块间的这种联系。

### Web论坛: nodeclub

`Node Club` 是用 `Node.js` 和 `MongoDB` 开发的新型社区软件，界面优雅，功能丰富，小巧迅速， 已在`Node.js` 中文技术社区 CNode 得到应用，但你完全可以用它搭建自己的社区。

![](http://lszb811.qiniudn.com/zsd-3.png)

## 单线程

`javascript`语言的执行环境是"单线程"（single thread）。 所谓"单线程"，就是指一次只能完成一件任务。如果有多个任务，就必须排队，前面一个任务完成，再执行后面一个任务，以此类推。

这种模式的好处是实现起来比较简单，执行环境相对单纯；坏处是只要有一个任务耗时很长，后面的任务都必须排队等着，会拖延整个程序的执行。常见的浏览器无 响应（假死），往往就是因为某一段`Javascript`代码长时间运行（比如死循环），导致整个页面卡在这个地方，其他任务无法执行。

大部分 Web 应用的瓶颈都在 `I/O`, 即读写磁盘，读写网络，读写数据库。使用怎样的策略等待这段时间，就成了改善性能的关键点

## 同步与异步

为了解决这个问题，`Javascript`语言将任务的执行模式分成两种：同步（Synchronous）和异步（Asynchronous）。

"同步模式"就是上一段的模式，后一个任务等待前一个任务结束，然后再执行，程序的执行顺序与任务的排列顺序是一致的、同步的；"异步模式"则完全不同， 每一个任务有一个或多个回调函数（`callback`），前一个任务结束后，不是执行后一个任务，而是执行回调函数，后一个任务则是不等前一个任务结束就执 行，所以程序的执行顺序与任务的排列顺序是不一致的、异步的。

"异步模式"非常重要。在浏览器端，耗时很长的操作都应该异步执行，避免浏览器失去响应，最好的例子就是Ajax操作。在服务器端，"异步模式"甚至是唯一的模式，因为执行环境是单线程的，如果允许同步执行所有`http`请求，服务器性能会急剧下降，很快就会失去响应。

## 进程与线程

mac系统中的进程与线程

![](http://lszb811.qiniudn.com/zsd-4.png)

从图中我们可以看出，一个进程可以包括多个线程，进程就好比工程里的车间，线程就是这个车间的工人，在引入线程的操作系统中，通常都是把进程作为分配资源的基本单位，而把线程作为独立运行和独立调度的基本单位。由于线程比进程更小，基本上不拥有系统资源，故对它的调度所付出的开销就会小得多，能更高效的提高系统内多个程序间并发执行的程度。

### 区别

线程和进程的区别在于，子进程和父进程有不同的代码和数据空间，而多个线程则共享数据空间，每个线程有自己的执行堆栈和程序计数器为其执行上下文。多线程主要是为了节约CPU时间，发挥利用，根据具体情况而定。线程的运行中需要使用计算机的内存资源和CPU。

![](http://lszb811.qiniudn.com/zsd-5.png)

## 模块和包

### 模块

模块：一个实现某些特定功能的文件，以实现模块化编程。通过require（模块名）引入模块. —模块中的功能（如：变量，函数）通过赋给`exports`对象的某个属性提供给调用者使用。

模块是可重用的代码库。比如用来与数据库交互的模块、支持web开发的模块以及通过web套接字协助通信的模块

### 如何使用模块？

在Node中使用模块是非常方便的，在 `JavaScript` 代码中可以直接使用全局函数 `require()` 来加载一个模块。例如，我们可以使用`require("http")`来加载`node`中自带的http服务器模块，

### 包

包：包是一个文件夹，它将模块封装起来，用于发布、更新、依赖管理和版本控制。通过package.json来描述包的信息：入口文件，依赖的外部包等等。通过`npm install`命令来安装包，并通过`require`使用包。

### package.json

在开发`Node.js`应用程序的时候，一个一个模块的安装显然很耗时，又或是忘了安装某个模块怎么办？`npm`允许开发人员使用`package.json`文件来指定在应用程序中要用的模块，并且通过单个命令来安装它们：
    
    npm install
    

`package.json`文件仅包含以特定格式表示的项目信息。一个最小的`package.json`文件会是这样：
    
    {
        "name" : "example 1",
        "version": "0.0.2",
        "dependencies":{
           "underscore":"~1.2.1"
        }
    }
    

使用`package.json`文件意味着我们无需记忆应用程序会依赖于哪些模块，其他开发人员会发现，可以很简单地安装你的应用程序

## 异步式 I/O 与事件驱动

`Node.js` 的异步机制是基于事件的，`I/O` 是输入输出的意思，指的是计算机和人或者数据处理系统之间的通信。可以将`I/O` 想成是数据在一次输入和一次输出之间的移动。

每一个 `I/O`就是一次请求，所有的磁盘 `I/O`、网络通信、数据库查询都以非阻塞的方式请求，返回的结果由事件循环来处理。如下图所示：

![](http://lszb811.qiniudn.com/zsd-6.png)

`Node.js` 进程在同一时刻只会处理一个事件，完成后立即进入事件循环检查并处理后面的事件。这样做的好处是，CPU 和内存在同一时间集中处理一件事，同时尽可能让耗时的 I/O 操作并行执行

![](http://lszb811.qiniudn.com/zsd-7.png)

## 开始node编程

在这里，我推荐大家使用`webstorm`进行`node.js`的开发，方便又快捷，比起cmd，或者Mac下的终端都好用太多了。

至于node的安装大家就自行百度吧，这里就不赘述了，看下`webstorm`下的node编程界面吧： 我们只需要在编写好的node代码界面按鼠标右键，然后点击Run就行啦，方便又快捷吧

![](http://lszb811.qiniudn.com/zsd-8.png)

下面是node的输出界面：

![](http://lszb811.qiniudn.com/zsd-9.png)

在`Mac`系统下进行web开发，我推荐大家使用的三款工具是：[coda2](http://ifunapple.com/2014/02/coda/),[webstorm](http://www.jetbrains.com/webstorm/)和[Sublime text3](http://www.sublimetext.com/3)这些是目前我已经的最好的开发工具了，大家不妨试试哪个更符合自己的口味。

在`webstorm`进行node开发需要先配置一定的文件，大家就自行百度吧，因为我的`webstorm`已经配置好了，所以没法截图给大家看步骤了，大概步骤是，在mac系统下是先点击顶部栏的`webstorm`，然后点击`perference`，然后点击`Node.js and NPM`，然后在右侧点击configure配置，最后大概会是下面这个样子：

![](http://lszb811.qiniudn.com/zsd-10.png)

`windows`系统下和这个流程步骤大概相似啊，我使用的版本是8.0.4的。

## 全局变量

在js编程中，我们最好给每个变量都添加上var关键字，以免污染全局命名空间，提高代码的耦合风险。

### console

`console`用于向标准输出流`standout`（stdout）和标准错误流(stderr)输出字符。

`console.log()`向标准输出流打印字符并以换行符结束，其接受多个参数，将以类似C语言的`printf（）`格式输出

![](http://lszb811.qiniudn.com/zsd-11.png)
    
    console.log(__dirname)输出文件目录
    

计算代码运行时间
    
        console.time(label)
        console.timeEnd(label)
    

![](http://lszb811.qiniudn.com/zsd-12.png)

我们只需在开始和结束那里给同样一个标签即可，中间放你想要计算执行时间的任何代码。

## `__filename`和`__dirname`
    
        console.log(__filename);// /Users/hwax/Desktop/My Project/avalon/hello.js
        console.log(__dirname);// /Users/hwax/Desktop/My Project/avalon
    

> 二、HTTP模块与事件模块

## Node.js的http服务器

通过使用`HTTP`模块的低级API，`Node.js`允许我们创建服务器和客户端。刚开始学`node`的时候，我们都会遇到如下代码：
    
    var http = require('http');
    http.createServer(function (req,res) {
        res.end('Hello World\n');
    }).listen(3000,"127.0.0.1");
    console.log("Server funning at http://127.0.0.1:3000");
    

这段代码的包括了http模块的信息，意思是：
    
    1.从`Node.js`的核心请求HTTP模块并赋予一个变量，以便在以后的脚本中使用。
    于是脚本就可以访问一些方法来通过`Node.js`使用`HTTP`。
    
    2.使用`createServer`创建新的web服务器对象
    
    3.脚本将一个匿名函数传递给服务器，告诉web服务器对象每当其接收到请求时会发生的是什么
    
    4.脚本第4行定义了web服务器的端口和主机，这意味着可以使用`http://127.0.0.1:3000`
    来访问服务器
    

## Http头

对于每个`HTTP`请求和响应，都会发送`HTTP`头，`HTTP`头发送的是附加的信息，包括内容类型、服务器发送响应的日期以及`HTTP`状态码

`http`头包含了许多信息，以下是我的百度首页包含的`http`头的信息：

![](http://lszb811.qiniudn.com/zsd-13.png)

由于我的百度首页添加了较多的网站，所以这里的数据和读者们的可能不一样。从中我们可以看出百度是web服务器是`BWS/1.1`

下面是刚才上面那段代码的http头部信息：

![](http://lszb811.qiniudn.com/zsd-14.png)

## Node.js中的重定向

在node中，我们可以很容易地创建一个简单的服务器将访问者重定向到另外一个Web页面上，其准则如下：
    
    1.给客户发送301响应代码，告诉客户，资源已经移到另一个位置了；
    2.发送一个位置头告诉客户重定向到哪里。
    

相关代码如下：
    
    var http = require('http');
    http.createServer(function (req,res) {
        res.writeHead(301,{
            'Location':'Http://example-2.com/web'
        });
        res.end();
    }).listen(3000,'127.0.0.1');
    console.log("Server funning at http://127.0.0.1:3000");
    

打开浏览器访问`http://127.0.0.1:3000`页面会被重定向。

## 响应不同的请求

`Node.js`不单可以创建单一的响应，对于多种类型的请求，我们需要给应用程序加一些路由。`Node`通过使用`URL`模块让这一切直截了当。`URL`模块使我们可以读取`URL`、分析它然后对输出做一些事情。
    
    var url = require('url');
    var requestURL = "http://example.com:1234/path?query=string#hash"
    

现在，我们可以分析请求的URL并从中截取内容，例如，要想获得主机名称，我们可以输入：
    
    url.parse(requestURL).hostname
    

这时，他将返回`"example.com"`

取得端口号可以输入：
    
    url.parse(requestURL).port
    

他将返回`“1234”`

## 事件模块

`Node.js`被认为是实现并发的最佳方法。`Events`（事件）模块是Node.js的核心，许多其他模块用它来围绕着事件架构功能。由于`Node.js`运行于单一的线程中，任何同步代码都是阻塞的。所以，在编写`Node.js`代码的时候我们要考虑一些简单的规则：
    
    1.别阻塞——`Node.js`是单线程的，如果代码阻塞的话所有其他一切都停止了
    2.快速返回——操作应当快速返回。如果不能快速返回，就应当将其迁移到另一个进程中
    

Events模块让开发人员可以为事件设置侦听器和处理器。在客户端js中，我们可以对单击事件设置一个监听器，然后在事件发生时执行一些事情：
    
    var tar = document.getElementById("target");
    tar.addEventListener("click", function () {
       alert("click event fired,target was clicked");
    },false);
    

当然，这是在不考虑IE兼容性的一个例子，`Node.js`重点事件更常见的是网络事件，包括：
    
    1.来自web服务器的响应
    2.从文件读取数据
    3.从数据库返回数据
    

使用`Events`模块我们首先要创建一个新的`EventEmitter`实例：
    
    var EventEmitter= require('events').EventEmitter;
    var test = new EventEmitter();
    

一旦在代码中加入上述内容，就可以添加事件和侦听器了，我们可以按如下方法发送事件，比如：
    
    test.emit('msg','the message send by node');
    

第一个参数是对事件进行描述的字符串，以便用于侦听器的匹配

为了接收消息，就必须添加侦听器，侦听器在事件触发时处理它，例如：
    
    test.on('message',function(data){
        console.log(data);
    });
    

`Events`模块`addListener/on，once，removeListener，removeAllListeners，emit`等基本的事件监听模式的方法实现。它与前端`DOM`树上的事件并不相同，因为它不存在冒泡，逐层捕获等属于`DOM`的事件行为，也没有`preventDefault()、stopPropagation()、 stopImmediatePropagation()` 等处理事件传递的方法。
    
    1.类: events.EventEmitter：通过 require('events').EventEmitter 获取 EventEmitter 类。
    2.emitter.on(event, listener)：添加一个 listener 至特定事件的 listener 数组尾部。 返回 emitter，方便链式调用，下同。
    
    3.emitter.removeListener(event, listener)从一个事件的 listener 数组中删除一个 listener
    
    4.emitter.listeners(event)返回指定事件的 listener 数组
    

更多详情见：[Node.js API 文档](http://nodeapi.ucdok.com/api/events.html)

以下代码展示了一条能在5秒内自我毁灭的机密消息：
    
    var EventEmitter = require('events').EventEmitter;
    var secretMessage = new EventEmitter();
    
    secretMessage.on('message', function (data) {
       console.log(data);
    });
    
    secretMessage.on('self destruct', function () {
        console.log('the msg is destroyed!');
    });
    
    secretMessage.emit('message','this is a secret message.It will self deatruct in 5s');
    
    setTimeout(function () {
       secretMessage.emit('self destruct');
    },5000);
    

在这段脚本中，发送了两个事件，有两个侦听器。当脚本运行时，消息事件就发生，并由“`message`”处理器处理

在`Node.js`中到处都用`EventEmitter`，所以掌握它是很重要的。`Node.js`通过`I/O`操作获得的数据并广泛使用`Events`模块来支持异步编程
