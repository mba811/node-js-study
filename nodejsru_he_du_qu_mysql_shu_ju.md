# node.js如何读取MySQL数据

先安装mysql模块。

node.js默认安装时，模块文件放在 /usr/local/lib/node_modules 这个目录下，为了便宜管理，模块还是统一安装到这里好。
    
    $ cd /usr/local/lib         
    $ npm install mysql         

程序文件mysql.js
    
    var Client = require('/usr/local/lib/node_modules/mysql').Client;
    var client = new Client();
    
    client.user = 'root';
    client.password = '';
    
    console.log('Connecting to MySQL...');
    
    client.query('USE tiny_shop');     //如果MySQL中没有库表，赶紧建。
    
    http = require("http");
    
    var server = http.createServer(function(request, response) {
        response.writeHeader(200, {"Content-Type": "text/html"});
    
        client.query('SELECT * FROM tags', function selectCb(err, results, fields) {  
            if (err) {  
                throw err;  
            }  
    
            var data = '';
            for (var i=0; i<results.length; i++) {          
                var firstResult = results[i];
                data += 'id: ' + firstResult['id']+'tag: ' + firstResult['tag']; 
            } 
    
            response.write(data); 
            response.end();
        });
    });
    
    server.listen(8080);
    
    var sys = require("util");
    sys.puts("Server running at http://localhost:8080/"); 

运行
    
    $ node mysql.js

在浏览器里，输入 http://localhost:8080 ，就能显示数据库里的数据了。
