# nodejs如何使用mongo数据库

首先，请参考下面两个链接，配置并运行express和mongodb。 1.使用express生成一个空白网站 [http://cnodejs.org/topic/501f43eef767cc9a51c7b90e](http://cnodejs.org/topic/501f43eef767cc9a51c7b90e) 2.mongo数据库的安装及命令行连接 [http://cnodejs.org/topic/50285b34f767cc9a512a1977](http://cnodejs.org/topic/50285b34f767cc9a512a1977)

安装mongoose模块
    
    npm install -g mongoose

mongoose官网 [http://mongoosejs.com/](http://mongoosejs.com/)

在express的routes/index.js文件里，加入如下代码
    
    exports.index = function(req, res){
      var mongoose = require('/usr/local/lib/node_modules/mongoose'); 
    
      var db = mongoose.createConnection('localhost', 'test');
    
      var schema = mongoose.Schema({ name: 'string' }); 
      var User = db.model('User', schema);
    
      var kitty = new User({ name: 'Zildjian' }); 
      kitty.save(function (err) {
        if (err) // ...
        res.end('meow');
      }); 
    
    
      User.find({'name':'Zildjian'},function(err,docs){  
        res.render('index', { title: docs});
      }); 
    }; 

打开网页地址 http://localhost:3000/ 就能看到刚才新增的数据库记录输出了。