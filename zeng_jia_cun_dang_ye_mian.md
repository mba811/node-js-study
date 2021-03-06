# 增加存档页面

现在我们来给博客增加存档功能，当进入存档页时，按年份和日期的降序列出所有的文章。

首先，我们在主页左侧导航中添加存档页（archive）的链接，修改 header.ejs，在 `<span><a title="主页" href="/">home</a></span>` 下添加一行代码：
    
    <span><a title="存档" href="/archive">archive</a></span>
    

打开 post.js ，在最后添加以下代码：
    
    //返回所有文章存档信息
    Post.getArchive = function(callback) {
      //打开数据库
      mongodb.open(function (err, db) {
        if (err) {
          return callback(err);
        }
        //读取 posts 集合
        db.collection('posts', function (err, collection) {
          if (err) {
            mongodb.close();
            return callback(err);
          }
          //返回只包含 name、time、title 属性的文档组成的存档数组
          collection.find({}, {
            "name": 1,
            "time": 1,
            "title": 1
          }).sort({
            time: -1
          }).toArray(function (err, docs) {
            mongodb.close();
            if (err) {
              return callback(err);
            }
            callback(null, docs);
          });
        });
      });
    };
    

接下来我们在 index.js 中添加 `/archive` 的路由规则。打开 index.js ，在`app.get('/u/:name')` 前添加以下代码：
    
    app.get('/archive', function (req, res) {
      Post.getArchive(function (err, posts) {
        if (err) {
          req.flash('error', err); 
          return res.redirect('/');
        }
        res.render('archive', {
          title: '存档',
          posts: posts,
          user: req.session.user,
          success: req.flash('success').toString(),
          error: req.flash('error').toString()
        });
      });
    });
    

在 views 文件夹下新建 archive.ejs 模版文件，添加如下代码：
    
    <%- include header %>
    <ul class="archive">
    <% var lastYear = 0 %>
    <% posts.forEach(function (post, index) { %>
      <% if (lastYear != post.time.year) { %>
        <li><h3><%= post.time.year %></h3></li>
      <% lastYear=post.time.year } %>
      <li><time><%= post.time.day %></time></li>
      <li><a href="/u/<%= post.name %>/<%= post.time.day %>/<%= post.title %>"><%= post.title %></a></li>
    <% }) %>
    </ul>
    <%- include footer %>
    

这里我们通过在模版中设置一个 lastYear 变量判断是否和上次已经显示的年份相同，相同则不再显示年份，不同则显示。

最后，在 style.css 中添加如下样式：
    
    .archive{list-style:none;line-height:28px;}
    .archive h3{margin:0.5em 0;}
    .archive time{float:left;font-size:14px;color:#999999;margin-right:1.2em;}
    

现在，我们增加了文章的存档页面。