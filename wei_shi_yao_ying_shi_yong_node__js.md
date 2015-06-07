# 为什么应使用 Node.js

![](http://lszb811.qiniudn.com/node-1.png)

JavaScript 高涨的人气带来了很多变化，以至于如今使用其进行网络开发的形式也变得截然不同了。就如同在浏览器中一样，现在我们也可以在服务器上运行 JavaScript ，从前端跨越到后端，这样巨大的反差让人难以想象，因为仅仅在几年前 Javascript 还如同 Flash 或者 Java applet 那样嵌入网页在沙箱环境中运行。

在深入Node.js之前，你可能需要阅读和了解使用跨栈式 [JavaScript（JavaScript across the stack）](http://www.toptal.com/javascript/guide-to-full-stack-javascript-initjs)带来的好处，它统一了编程语言和数据格式（JSON），让你能最佳地重用开发人员资源。由于这更多的是关于 JavaScript 的特点，这里就不过多讨论它。但它确实是一个让人在开发环节中使用 Node 的关键的优点。

正如 [维基百科](http://en.wikipedia.org/wiki/Nodejs) 所说：“Node.js 是谷歌 V8 引擎、libuv平台抽象层 以及主体使用 Javscript 编写的核心库三者集合的一个包装外壳。” 除此之外，值得注意的是，Node.js 的作者瑞恩·达尔 (Ryan Dahl) 的目标是创建具有实时推送能力的网站。在 Node.js 中，他给了开发者一个使用事件驱动来实现异步开发的优秀解决方案。_（注：V8是谷歌开发的，目前公认最快的 Javascript 解析引擎，libuv 是一个开源的、为 Node 定制而生的跨平台的异步 IO 库。）_

简而言之：Node.js 在实时的 Web应用上采用了基于 WebSocket 的推送技术。这意味着什么样的革命性？Well，在经过了20多年的基于无状态的请求-返机制的无状态交互之后，我们终于有了实时的，双向连接的web应用，客户端和服务器端都可以发起通信，能够自由地交换数据。与此形成鲜明对比的是传统的 web响应模式，客户端总是主动发起通信而服务端被动返回。此外，这些都是基于运行在标准80端口上的开放Web组件（HTML、CSS和JS）。

可能有人会说，我们已经使用 Flash 和 Java Applet 的形式很多年了——但实际上，这些方式只是使用网络将数据传递到客户端上的沙箱环境。他们都是隔离运行的，而且经常操作到需要额外的权限之类的非标准端口。

凭借其独特的优势，Node.js的现在已经在[许多著名公司](http://nodejs.org/industry/)的产品中起到了关键作用。

在这篇文章中，我们不仅将讨论这些优势是如何实现的，而且也会讨论为什么你使用 Node.js 来替代一些经典的Web应用程序模型。

### Node.js 是如何工作的？

Node.js 的主要思路是：使用非阻塞的，事件驱动的 I/O 操作来保持在处理跨平台 (across distributed devices) 数据密集型实时应用时的轻巧高效。这听起来有点绕口。

它的真正含义是，Node.js 不是一个即将主导Web开发的世界的银弹级的平台。相反，它是一个满足特别需求的平台。你肯定不会希望使用 Node.js 去做 CPU密集型操作。事实上，使用它进行繁重的计算等于摒弃 Node 几乎所有的优点。Node 真正的亮点在于建设高性能，高扩展性的互联网应用——因为它能够处理庞大的并且高吞吐量的并发连接。

它的工作原理是相当有趣的。传统的网络服务技术，是每个新增一个连接（请求）便生成一个新的线程，这个新的线程会占用系统内存，最终会占掉所有的可用内存。而 Node.js 仅仅只运行在一个单线程中，使用非阻塞的异步 I/O 调用，所有连接都由该线程处理，在 libuv 的加分下，可以允许其支持数万并发连接（全部挂在该线程的[事件循环](http://en.wikipedia.org/wiki/Nodejs)中）。

![](http://lszb811.qiniudn.com/node-2.png)

做一个简单的计算: 假设是普通的Web程序，新接入一个连接会占用 [2M 的内存](http://nodejs.org/about/)，在有 8GB RAM的系统上运行时, 算上[线程之间上下文切换的成本](http://www.slideshare.net/marcusf/nonblocking-io-event-loops-and-nodejs)，并发连接的最大理论值则为 4000 个。这是在传统 Web服务端技术下的处理情况。而 Node.js 则达到了约 1M 一个并发连接的拓展级别 ([相关证明](http://blog.caustik.com/2012/08/19/node-js-w1m-concurrent-connections/)).

当然，在所有客户端的请求共享单一线程时也会有问题, 这也是一个编写 Node.js 应用的潜在缺陷. 首先, 大量的计算可能会使得 Node 的单线程暂时失去反应, 并导致所有的其他客户端的请求一直阻塞, 直到计算结束才恢复正常。 其次，开发人员需要非常小心，不要让一个 Exception 阻塞核心的事件循环，因为这将导致 Node.js 实例的终止（实际上就是程序崩溃）。（ 笔者注：如 PHP 中某个页面挂掉是不会影响网站运行的，但是 Nodejs 是一个线程一个线程来处理所有的链接，所以不论是计算卡了或者是被异常阻塞了都可能会影响到其他所有的链接。解决方案在稍后讨论。）

用来避免异常抛出时中断进程的方法是将异常使用回调传递出去（而不是抛出他们，就像在其他环境中一样）。即使一些未处理的异常阻塞了程序，依旧有多种应对的解决方案，而且也有很多可用于监视 Node 进程来执行必要的崩溃后恢复工作的策略和工具（虽然你将无法恢复用户的 Session ），最常见的是使用 Forever 模块，或者采用其他的外部系统工具如[upstart and monit](http://jacobmumm.com/2011/08/29/node-as-a-service-in-ubuntu-11-04-with-upstart-monit-and-forever-js/)。

### NPM: The Node Package Manager

当我们讨论 Node.js 的时候，一个绝对不应该忽略地方就是默认内置的模块管理工具 —— [NPM](http://docs.nodejitsu.com/articles/getting-started/npm/what-is-npm)。 其灵感来源与 Ruby Gems（具有版本和依赖管理功能，可以通过在线资料库便捷安装可重用的组件的管理工具）。

一个完整的公用模块列表可以在 NPM 的[网站](https://npmjs.org/)上找到，或者通过使用与 Node.js 一同安装的 NPM CLI 工具放问到。该模块的生态系统向所有人开放，任何人都可以发布自己的模块，所有的模块都可以在 NPM 资料库中找到。你可以在 [页面](http://howtonode.org/introduction-to-npm)找到 NPM 的一个简要介绍（有点旧，但依旧能看）。

目前非常流行的一些 NPM 模块有：

  * [express](http://expressjs.com/) – Express.js,是一个简洁而灵活的 node.js Web应用框架, 并且已经是现在大多数 Node.js 应用的标准框架，你已经可以在很多 Node.js 的书籍中看到它了。
  * [connect](http://www.senchalabs.org/connect/) – Connect 是一个 Node.js 的 HTTP 服务拓展框架，提供一个高性能的“插件”集合，以中间件闻名，是 Express 的基础部分之一。
  * [socket.io](http://socket.io/) 和 [sockjs](https://github.com/sockjs) – 目前服务端最流行的两个 websocket 组件。
  * [Jade](http://jade-lang.com/) – 流行的模板引擎之一，并且是 Express.js 的默认模板引擎。其灵感来源于 HAML。
  * [mongo](https://npmjs.org/package/mongodb) 和 [mongojs](https://github.com/gett/mongojs) – 封装了 MongoDB 的的各种 API，不过笔者平常工作用的是 [mongoose](http://mongoosejs.com/) 也很推荐。
  * [redis](https://github.com/mranney/node_redis) – Redis 的客户端函数库.
  * [coffee-script](https://npmjs.org/package/coffee-script) – CoffeeScript 编译器，允许开发者使用 Coffee 来编写他们的 Node.js 程序。
  * [underscore](https://npmjs.org/package/underscore) ([lodash](http://lodash.com/), [lazy](https://npmjs.org/package/lazy)) – 最流行的 JavaScript 工具库 , 用于 Node.js 的封装包，以及两个采取略有不同的实现方法来获得[更好性能](http://philosopherdeveloper.com/posts/introducing-lazy-js.html)的副本（counterparts）。
  * [forever](https://npmjs.org/package/forever) – 可能是用来确保 node 脚本持续运行的最流行的工具。

还有很多好的模块，这里就不一一列举了（希望没有冒犯到没列举的）。

### Node.js 应该用在什么地方

#### 聊天

聊天是最典型的多用户实时交互的应用。从 [IRC](http://zh.wikipedia.org/wiki/IRC) 开始，有许多开源或者不开源的协议都运行在非标准端口上，而现在，使用 Node.js 则可以解决这些问题——在标准的80端口运行 WebSockets。

聊天应用程序是最能体现 Node.js 优点的例子：轻量级、高流量并且能良好的应对跨平台设备上运行密集型数据（虽然计算能力低）。同时，聊天也是一个非常值得学习的用例，因为它很简单，并且涵盖了目前为止一个典型的 Node.js 会用到的大部分解决方案。

让我们试着来描绘它如何工作。

在最简单的情况下，我们布置了一个聊天室在我们的网站上，用户可以在上面发消息，当然是一对多的形式。例如，假设总共有三个人连接到我们的网站上。

在服务端这边， 我们有一个使用 Express.js 搭建的简单站点，该站点实现了两件事 1) 处理路径为 ‘/’ 的GET请求时，下发包括一个留言板以及一个发送信息的 ‘发送’ 按钮的页面 2) 一个监听客户端发送新消息的 websockets 服务。

在客户端这边，我们有一个 HTML 页面，上面有个两个 js 方法，一个是用于触发事件的 “发送” 按钮，这会把把输入的消息通过 webscoket 发送，另一个方法是用 webscoket 在客户端上监听服务端来的推送（例如，其他用户发送的消息）。

当有一个客户端发送消息的时候，发生的事情是：

  1. 浏览器上，点击发送按钮触发了 js 函数，将输入框中的文字通过 websocket 消息发送到服务器的 websocket 客户端（页面初始化加载的时候连接的）。
  2. 服务端的 websocket 组件收到 消息，然后通过广播方法转发到其他所有连接的客户端。
  3. 通过页面上运行的 websocket 客户端组件，所有的客户端都能收到这条推送的新消息。接着 js 处理函数可以把这个消息添加到文字框内。

![](http://lszb811.qiniudn.com/node-3.png)

这是一个最简单的例子。如果要更好的解决方案，你可以使用 Redis 数据库做一个简单的缓存。在一个更高级的解决方案中，你可能需要一个消息路由来专门处理消息队列，并且需要一个更强健的发送机制，比如发送的时候覆盖上暂时离线的用户或者为离线的注册用户存储尚未接收的消息等等。但是不论你做了怎么样的改进，Node.js 都将遵循一个基本原则：响应事件，处理多个并发连接，并保持流动性的用户体验。

### 对象数据库接口（API ON TOP OF AN OBJECT DB）

尽管，Node.js 确实非常擅长实时交互的应用，同时它也十分适合通过对象数据库（object DB）来查询数据（如 MongoDB）。以 JSON 格式存储的数据允许 Node.js 直接处理，不需要纠结数据转换和匹配的问题。

举个例子，如果你正在使用 Rails，你会将 JSON 数据转成 二进制的 model，当数据再被 Backbone.js, Angular.js 或者 jQuery AJAX 之类的调用又要转回 JSON。如果是 Nodejs 的话，你可以通过一个 REST API 简单的导出 JSON 对象以供客户端使用。另外，从数据库读写时候如果使用的是 MongoDB 的话，你也不用担心的 JSON 与任何数据之间的格式问题。总之，你可以避免多元的数据转换问题，不论是在客户端、服务端还是数据库。

### 队列输入

如果你正在接收一个高量并发的数据，你的数据库可能会成为你处理的瓶颈。正如上面的描述，Node.js 可以轻松的处理并发连接。 但是，由于数据库操作是一个阻塞的操作（在这种情况下），这就是麻烦的地方。Node.js的解决方案是，在数据真正的写入之前就承认客户端的数据是真实的。

用这种方法，在高负载的时候系统继续维持它的响应，这在当客户端不需要严格确认一个数据是否成功的被写入时特别有用。典型的例子包括：日志记录或者用户跟踪数据（user-tracking data）的记录，这会被分批处理并且在稍后才使用；同时也包括[最终一致性](http://www.allthingsdistributed.com/2007/12/eventually_consistent.html)（so, 常用于 NoSQL）可以接受，不需要立即反应的操作（例如 Facebook 上更新点赞的数目）。

数据通过某些缓存或者消息队列的基础组件（例如 RabbitMQ, ZeroMQ）进入队列，并且通过一个独立的数据库批量写入进程来一一消化，或者通过一个更高性能的计算密集型后端服务来进行处理。其他的语言/框架也可以实现相似的操作，但在相同的配置下是达不到 nodejs 的高吞吐量与高并发。

![](http://lszb811.qiniudn.com/node-4.png)

简单的说：使用 Node，你可以把数据库操作扔到一边并在稍后处理它们，假设他们成功了一样继续执行下去。（笔者注：在开发中通常的情况通常是，种耗时的操作通过回调函数来异步处理，主线程继续往下执行）

### 数据流

在较为传统的网络平台上，HTTP 的请求和响应更像是孤立的事件；然而事实上，他们都是数据流。这一观察结果在 Nodejs 上可以用来建立一些很酷的功能。因为数据通以流的形式接收，而我们可以在网站上在线处理正在上传中的文件。这样的话，就可以实现实时的音频和视频编码，以及在不同数据源之间进行代码（代理见下一段）。

_（笔者注：Node 有代替如 apache 这样的 webserver 处理数据，所以开发者可以直接收到客户端一份一份上传的数据，并实时处理。上面这段话听起来有点抽象，不过各位可以简单的想象一下不需要开 YY 或者 QQ，打开网页就能进行语音视频的功能。）_

### 代理

Node.js 可以通过异步的方式处理大量的并发连接，所以很容易作为服务端的代理来使用。这在与不同响应时间的不同服务之间进行代理，或者是收集来自多个来源的数据时尤其有用。

举个例子：考虑一个服务器端的应用程序和第三方资源进行通信以更新自不同来源的数据，或者将服务端上的一些图像和视频资源存储到第三方云服务。

虽然专用代理服务器确实存在，但是如果你还没有专用的代理服务器，或者你需要一个本地开发的解决方案，那么使用 Node 来做代理可能是更好的选择。关于这个解决方案，我的意思是指当你在开发的时候，你可以使用Node.js的开发环境搭建一个服务来处理对资源和代理的请求，而在生产环境下，你可以使用专用的代理服务（比如nginx，HAProxy等）来处理这些交互。

### 股票操盘手的仪表盘

让我们继续讨论应用程序这块。实时网络的解决方案可以很轻松的实现证券交易软件——用于跟踪股票的价格，执行计算、做技术分析，同时生成报表。

使用一个实时的的基于网页的解决方案，将会允许操盘手轻松的切换工作软件以及工作地点。相信不久，我们或许会在 佛罗里达州、伊维萨岛又或者是巴厘岛的海滩上看到他们。

### 应用监听仪盘表

另一种常见的用例中，使用 Node+Web+Socket 非常适合：跟踪网站访问者并且可视化实时它们之间的实时交互。 （如果你有兴趣，可以去看看 [Hummingbird](http://projects.nuttnet.net/hummingbird/)）

你可能需要采集用户的实时状态, 或者甚至当他们到达渠道中某个特定的点时, 打开一个交流频道， 通过有针对性的互动介绍移动到下一个阶段. （如果你感兴趣的话，推荐你看看 [CANDDi](http://canddi.com/)）

想象一下，如果你知道你的访客的实时操作，并能够形象化地看到他们的交互，这将对你的业务带来多大的提升。随着实时的、双向 socket 通信的 Node.js ，现在你可以做到了。

### 系统监控仪表

现在，让我们看看事情的基础设施方面。想象一下，比如，希望为其用户提供服务监控页面（例如，GitHub上的状态页）的 SaaS 运营商 。通过 Node.js 的事件循环，我们可以创建一个基于 Web 的功能强大的仪表板，以异步方式检查服务状态并且使用的 WebSockets 将数据推送到客户端。

内部（公司内部）和公共服务的状态都可以使用该项技术实现实时的上报。让我们把这一想法延伸的远一点，试着想象一个电信运营商中[网络运营中心（NOC）](http://en.wikipedia.org/wiki/Network_operations_center)的监控应用，云/网络/服务器运营商，或者一些金融机构，全都运行在这个由 Node.js 和 WebSocket 组成的应用上，而不是 Java 和/或 Java Applet。

注意：不要尝试使用 Node 打造硬实时系统（即，响应时间要求一致的系统）。 [Erlang是可能是该类应用程序的更好的选择](http://nodeguide.com/convincing_the_boss.html)。

### 什么地方可以使用 Node.js

#### 服务端 WEB 应用

通过 Node.js 使用 Express.js 也可以用来创建服务端上的典型的网页应用。然而，虽然有可能，使用 Node.js 来进行请求+响应的形式来呈现 HTML 并不是最典型的用例。有人赞成也有人反对这一做法。这里有一些看法以供参考:

优点：

  * 如果你不需要进行 CPU密集型计算，你可以从头到尾甚至是数据库（比如 MongoDB）都使用 Javascript 来开发。这显著地减轻了开发工序（包括成本）。
  * 对于一个使用 Node.js 作为服务端的单页应用或者 websocket 应用，爬虫可以收到一个完全 HTML 呈现的响应，这是更为SEO友好的。

缺点：

  * 任何CPU密集型的计算都将阻碍 Node.js 的反应，所以使用多线程的平台是一个更好的方法。或者，您也可以尝试向外扩展的计算[1](http://segmentfault.com/img/bVbJSF)。
  * Node.js 使用关系型数据库依旧十分痛苦（详细见下方）。拜托了，如果你想执行关系型数据操作，请考虑别的环境：Rails， Django 甚至 ASP.NET MVC ...

  * [1](http://segmentfault.com/img/bVbJSF)另一种解决方案是，为这些CPU密集型的计算建立一个高度可扩展的MQ支持的环境与后端处理，以保持 Node 作为一个前台专员来异步处理客户端请求。*

### Node.js 不应该在什么地方使用

#### 使用关系型数据库的服务端 WEB 应用

对比 Node.js 上的 Express.js 和 Ruby on Rails，当你使用关系型数据库的时候请毫不犹豫的选择后者。

Node.js 的关系数据库工具仍处于早期阶段，目前还没有成熟到让人能够愉快地使用它。而与此同时，Rails天生自带了数据访问组件，连同DB schema迁移的支持工具和一些Gems（一语双关，一指这些如同珍宝的工具，二指ruby的gems程序包）。Rails和它的搭档框架们拥有非常成熟且被证明了的活动记录（Active Record）或数据映射（Data Mapper）的数据访问层的实现，而这些是当你在使用纯JavaScript来复制这些应用的时候会非常想要使用的东西。

不过，如果你真的倾向于全部使用 JS（并且做好可能抓狂的准备），那么请继续关注 Sequelize 和 Node ORM2 ，虽然这两者仍然不成熟的，但他们最终会迎头赶上。

注：使用 Node 光是作为前端而 Rails 做后端来连接关系型数据库，这是完全有可能也并不少见的。_（笔者注：国外有种说法，PHP这一类程序员也可以算作是前端）_

### 繁重的服务端的计算和处理

当涉及到大量的计算，Node.js 就不是最佳的解决方案。你肯定不希望[使用 Node.js 建立一个斐波那契数的计算服务](http://zef.me/4561/node-js-and-the-case-of-the-blocked-event-loop)。一般情况下，任何 CPU密集型操作 会削弱掉 Node通过事件驱动， 异步 I/O 模型等等带来的在吞吐量上的优势，因为当线程被非异步的高计算量占用时任何传入的请求将被阻塞。

正如前面所说，Node.js 是单线程的，只使用一个单一的CPU核心。至于，涉及到服务器上多核并发处理，Node 的核心团队已经使用 cluster 模块的形式在这一方面做了一些工作 (参考：[http://nodejs.org/api/cluster.html](http://nodejs.org/api/cluster.html) )。当然，您也可以很容易的通过 nginx 的[反向代理](http://blog.argteam.com/coding/hardening-node-js-for-production-part-2-using-nginx-to-avoid-node-js-load/)运行多个 Node.js 的服务器实例来避免单一线程阻塞的问题。

关于集群(clustering) ，你应该将所有繁重的计算转移到更合适的语言写的后台进程来处理，同时让他们通过像 RabbitMQ 那样通过消息队列服务器来进行通信。

即使你的后台处理可能最初运行在同一台服务器上时看不出什么优点，但是这样的做法具有非常高的可扩展性的潜力。这些后台处理服务可以容易地分割出去，作为单独的 worker 服务器，而不需要配置入口 web服务器的负载。

当然，你也可以在其他语言平台上用同样的方法，但使用 Node.js 你可以得到很高的吞吐量，每个请求都作为一个小任务非常迅速和高效地处理，这一点我们已经讨论过了。

### 结论

我们已经从理论到实践讨论过 Node.js 了，从它的目标和野心，到其优点和缺点。在 Node.js 的开发中99%的问题是由误用阻塞操作而造成的。

请记住：Node.js 从来不是用于解决大规模计算问题而创建的。它的出现是为了解决大规模I/O 的问题，并且在这一点上[做的非常好](http://www.srirangan.net/2012-05-node-js-critics-which-part-of-event-driven-non-blocking-io-model-you-dont-understand)。

综上，如果你项目需求中不包含CPU密集型操作，也不需要访问任何阻塞的资源，那么你就可以利用的 Node.js 的优点，尽情的享受快速、可扩展的网络应用。
