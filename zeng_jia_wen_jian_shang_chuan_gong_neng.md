# 增加文件上传功能

一个完整的博客怎么能缺少图片呢？现在，我们来给博客添加文件上传功能，这样我们就可以使用 markdown 来链接图片了。

我们使用express的第三方中间件 multer 实现文件上传功能。打开 package.json，在 dependencies 中添加：
    
    "multer": “0.1.6"
    

并 npm install 安装模块。然后修改 app.js，添加：
    
    var multer  = require('multer');
    
    app.use(multer({
      dest: './public/images',
      rename: function (fieldname, filename) {
        return filename;
      }
    }));
    

其中，dest 是上传的文件所在的目录，rename 函数用来修改上传后的文件名，这里设置为保持原来的文件名。

打开 header.ejs ，在 `<span><a title="发表" href="/post">post</a></span>` 前添加一行代码：
    
    <span><a title="上传" href="/upload">upload</a></span>
    

然后打开 index.js ，在 `app.get('/logout')` 函数后添加如下代码：
    
    app.get('/upload', checkLogin);
    app.get('/upload', function (req, res) {
      res.render('upload', {
        title: '文件上传',
        user: req.session.user,
        success: req.flash('success').toString(),
        error: req.flash('error').toString()
      });
    });
    

**注意**：我们设置 `app.get('/upload', checkLogin);` 限制只有登陆的用户才能上传文件。

接下来，我们在 views 文件夹下新建 upload.ejs ，添加如下代码：
    
    <%- include header %>
    <form method='post' action='/upload' enctype='multipart/form-data' >
      <input type="file" name='file1'/><br>
      <input type="file" name='file2'/><br>
      <input type="file" name='file3'/><br>
      <input type="file" name='file4'/><br>
      <input type="file" name='file5'/><br>
      <input type="submit" />
    </form>
    <%- include footer %>
    

现在我们就可以访问文件上传页面了。这里我们限制一次最多可以上传 5 个文件。清空数据库，重新注册登录后，上传文件页面如下图：

![](https://github.com/nswbmw/N-blog/raw/master/public/images/3.1.jpg?raw=true)

我们现在只是有了一个可以上传文件的表单而已，并不能上传文件，接下来我们添加对上传文件的支持。

在 `app.get('/upload')` 后添加如下代码：
    
    app.post('/upload', checkLogin);
    app.post('/upload', function (req, res) {
      req.flash('success', '文件上传成功!');
      res.redirect('/upload');
    });
    

**注意**：假如从桌面启动的博客上传图片时失败，尝试在命令行中从博客根目录下启动。

现在，我们给博客添加了文件上传功能。假如我们上传了一张名为 **lufei.jpg** 的图片，示例：

**发表前**

![](https://github.com/nswbmw/N-blog/raw/master/public/images/3.2.jpg?raw=true)

**发表后**

![](https://github.com/nswbmw/N-blog/raw/master/public/images/3.3.jpg?raw=true)