# Node.js npm 详解

### 一、npm简介

安装npm请阅读[前辈](http://www.infoq.com/cn/articles/nodejs-npm-install-config)的文章，很详细的介绍。

npm的全称：Node Package Manager.

#### （1）通俗的理解

其实从字面意思就可以理解这个产品有什么作用翻译为“Node包管理器”。对，就是Node的包的一个管理工具，目前我尝试的有

  1. 下载并安装包（npm install [pkg]）
  2. 升级安装包（npm update [pkg]）
  3. 卸载安装包（npm uninstall/rm [pkg]），可以指定卸载包的版本号 …

其实这些命令很简单，常用的必须记住，不常用的查询即可，这才是比较好的学习知识方式。

在终端输入：
    
    //查看npm拥有的全部命令
    $ npm --help
    $ npm help
    //查看某一个npm命令的详细用法
    $ npm <command> --help
    $ npm help <command>
    

#### （2）专业的解释

npm（Node Package Manager）是Node.js下的主流套件管理程式。它在Node.js v0.6.x版本之后，内建于Node系统。通过npm可以协助开发者安装、卸载、删除、更新Node.js套件，并且可以通过npm发布自己的插件。

### 二、类似的产品

其实学习一个产品，可以联系其它产品，能够更好的理解现在手头的产品。第一次学习npm我的第一反应就是，很像linux/mac平台的很多软件，依赖管理的方式可以参考maven…当然相似性可以随便联想。

接下来，举几个例子吧，当然详细了解可以查baidu && google。

  1. gem
  2. PyPL
  3. pear
  4. macPort
  5. Homebrew
  6. rem
  7. apt-get
  8. yum …

是不是很多都很熟悉？这样对于npm的认识就不用局限于概念啦。

### 三、npm基础功能

#### （1）npmrc文件介绍

首先介绍一下npmrc文件，这个文件是npm包管理器的配置文件。

与npmrc相关的三个文件：

  1. 用户配置文件：~/.npmrc
  2. 全局配置文件：$PREFIX/npmrc
  3. npm内部配置文件：安装npm的目录下

下面仔细看一下npm config的配置。

#### （2）npm获取配置的6种方式（优先级从高到低）：

1.命令行参数
    
    $ --proxy http://<server>:<port>
    

2.环境变量

以”npm_config_“为前缀的环境变量将会被认为是npm的配置属性。 像Maven镜像的概念，方便通信吧。
    
    $ npm_config_proxy=http://<server>:<port>
    

3.用户配置文件
    
    //查看文件路径
    $ npm config get userconfig
    
    //mac系统默认路径
    $HOME/.npmrc
    

4.全局配置文件
    
    //查看文件路径
    $ npm config get globalconfig
    
    //mac系统默认路径
    /usr/local/etc/npmrc
    

> 3，4中输入终端的效果如图：

![](http://lszb811.qiniudn.com/nxj1.png)

5.内置配置文件

安装npm的目录下的npmrc文件。

6.默认配置

如果前5条均未设置，npm会使用默认配置参数。

#### （4）npm install

“安装指定包”：这个命令不难，但是也有需要注意的地方，就是安装的模式有两种，在后面会单独讲解。

> 如果不知道包的具体名称，可以在[http://search.npmjs.org](http://search.npmjs.org/)上进行搜索。

#### （5）npm uninstall

“卸载指定包”：在help的时候，会给你推荐npm rm 这个命令，uninstall会卸载掉包的依赖，rm。

#### （6）npm ls

查看安装的包清单，其实和linux的ls命令很像，可以跟很多参数，详情可以使用
    
    $ npm help ls
    

#### （7）npm search

搜索包的详细信息，比如我们搜索express试试。第一次搜索，会提示建立索引，需要耐心等待片刻，大家测试的时候不要就关掉啦终端。
    
    npm WARN Building the local index for the first time, please be patient
    

![](http://lszb811.qiniudn.com/nxj2.png)

其实看上去复杂，只是东西有点大，不过主要包含以下6个部分：

  1. 名称
  2. 描述
  3. 作者
  4. 发布时间
  5. 发布版本号
  6. 关键字

#### （8）npm update

更新安装的包

更多API可以查看官网：[https://npmjs.org/doc/](https://npmjs.org/doc/)

### 四、版本号的知识。

在node.js中的package.json配置文件中，我们需要配置版本号，比如0.1.2

第一位数字：主版本号

第二位数字：子版本号

第三位数字：补丁版本号

找到一个不错的介绍软件项目版本号的文章

[软件项目版本号的命名规则及格式](http://www.cnblogs.com/sdjxcolin/archive/2007/07/02/803376.html)

为什么要解释这个呢？肯定是有用，因为npm安装的时候是可以选择版本号的，有点理解会比较好吧，至少我是这么认为的。

安装Node和npm前半部分的配置可以参考之前我的两篇文章：

  1. [Hello Node](http://60sky.com/2014/01/28/hello-nodejs/)

### 四、本地模式和全局模式

如果你了解环境变量里面的，用户变量和系统变量。可以做一个类比进行理解。当然，windows上面的环境变量概念比较好理解。

#### 1. 本地模式

本地模式下安装包的特点

  * 不会写入PATH变量（也就是环境变量，无法在全局引用该安装包，不能在终端直接使用）
  * 能够在不同的node_modules目录，安装不同版本的安装包
  * 能够通过require()来引入安装包

使用“npm install [@]”安装的包，默认会安装在当前目录的“node_modules”目录下（如果没有该目录，在执行命令的时候，会自动帮你创建）。
    
    //专业的写法
    ./node_modules
    

（1）默认采用本地模式安装
    
    npm install <pkg>
    

（2）信息写入package.json文件
    
    npm install <pkg> --save
    

这个命令在安装包的同时，将信息写入package.json。

> @version表示指定安装包的版本号，是可选项目，默认安装最新版本。
> 
> 项目路径中如果有package.json文件，使用npm install方法就可以根据dependencies配置安装所有的依赖包。
> 
> 如果这样配置，当代码提交到github时，就不用提交node_modules这个文件夹。

#### 2. 全局模式

全局模式安装包的特点

  * 不需要重复安装
  * 不能使用require()引入
  * 会写入PATH，并建立软链接，使用命令行的方式使用
  * 不方便指定特定的版本运行

（1）采用全局模式安装
    
    npm install -g <pkg>
    

（3）在mac中全局的目录
    
    //安装包所在目录
    /usr/local/lib/node_modules/
    //运行命令的软链接所在目录
    /usr/local/bin
    

（4）查看安装包路径
    
    //查看当前包的安装路径
    npm root
    //查看全局的包的安装路径
    npm root -g
    

（5）设置全局模式安装目录
    
    //设置后，以全局模式将会安装在此目录中，不过需要手动加入PATH，切记
    npm config set prefix <global dir>
    //设置npm缓存文件的存放路径
    npm config set cache <cache dir>
    

（6）查看默认模式
    
    //默认返回：false
    $ npm get global
    $ npm config get global
    

（7）设置为默认以全局模式安装，就不用每次加”-g”参数啦。
    
    $ npm set global=true
    $ npm config set global=true
    

> npm set / npm config set与npm get / npm config get的区别和联系单独写吧。其实不难，只是需要实验才能得出结果，这里区别很细节。
> 
> 准备把文章拆分成几篇，写得详细了一点，这里写的话篇幅就太长了。
