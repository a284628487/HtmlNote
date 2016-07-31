# Node.js 连接 MySQL

#### 安装mysql
```
npm install mysql --save
```

#### 连接数据库并进行增删改查操作

```
var mysql = require('mysql');

var conn = mysql.createConnection({
	host: '127.0.0.1',
	user: 'root',
	password: '111111',
	database: 'mydb',
	port: 3306
});

conn.connect();
// 查询
conn.query('select * from User', function(err, result) {
	console.log(result);
});
// 关闭数据库连接 -> 不是直接关闭，而是在所有query完成之后再关闭
conn.end();
// 关闭数据库连接 -> 直接关闭连接
// conn.destory();
```

使用`conn.query`方法查询数据，`query`方法同样也可以进行新增、删除、更新操作，从回调函数的第二个参数中可以获取到对应的执行结果。

```
//新增
conn.query('insert into user (username,password) ...', function(err, result) {
    if (err) throw err;
    console.log(result); // result.insertId -> 获取新插入的数据的自增id
})
 
//删除
conn.query('delete from user where username="wupeigui"', function(err, result) {
    if (err) throw err;
    console.log(result);
})

// 更新 
conn.query('update user set id="1" where username="huxiaona"',function(err,result){
    if (err) {throw err};
    console.log("修改数据成功");
})
// 删除和更新，可以通过result.affectedRows来获取受影响的行数；
```

#### 数据库连接池
```
var connParams = {
	host: '127.0.0.1',
	user: 'root',
	password: '111111',
	database: 'mydb',
	port: 3306
};

var pool = mysql.createPool(connParams);
// 直接使用pool进行查询
pool.query('select * from User',function(err, rs, fields){
	console.log(rs);
});
// 共享一个连接
pool.getConnection(function(err, conn){
	conn.query('select * from User', function(err, rs) {
		console.log(rs);
		conn.release();
	});
});
```

#### 连接池配置选项

`createConnection`方法还可以传入一些其它选项参数。

- waitForConnections: 当连接池没有连接或超出最大限制时，设置为true且会把连接放入队列，设置为false会返回error
- connectionLimit: 连接数限制，默认：10
- queueLimit: 最大连接请求队列限制，设置为0表示不限制，默认：0

#### 连接释放

调用`connection.release()`方法，会把连接放回连接池，等待其它使用者使用!

#### 连接错误处理
```
var conn = 0;

function handleError() {
	var connParams = {
		host: '127.0.0.1',
		user: 'root',
		password: '1111110',
		database: 'mydb',
		port: 3306
	};
	conn = mysql.createConnection(connParams);
	conn.connect(function(err) {
		// 1
		if(err){ 
			// err.code === 'ER_ACCESS_DENIED_ERROR' -> 密码错误
			console.log(err);
		}
	});

	conn.on('error', function(err) {
		console.log('db error');
		// 如果是连接断开，自动重新连接
        if (err.code === 'PROTOCOL_CONNECTION_LOST') {
            handleError();
        } else {
        	// err.code === 'ECONNRESET' -> mysql 进程死掉
            throw err;
        }
	});
}

handleError();
```
上面的代码可以处理`登录密码错误`，`数据库宕机`，`连接超时`；

- 登录密码错误
```
ER_ACCESS_DENIED_ERROR
```
- 数据库宕机
```
ECONNRESET
```
- 连接超时/断开
```
PROTOCOL_CONNECTION_LOST
```
使用root账户修改MySQL的`wait_timeout`参数，设为10秒；
```
~ mysql -u root -p
mysql> show variables like 'wait_timeout';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| wait_timeout  | 28800 |
+---------------+-------+
1 row in set (0.00 sec)

mysql> set global wait_timeout=10;
Query OK, 0 rows affected (0.00 sec)

mysql> show variables like 'wait_timeout';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| wait_timeout  | 10    |
+---------------+-------+
1 row in set (0.00 sec)
```
添加测试代码
```
function query(){
    console.log(new Date());
    var sql = "show variables like 'wait_timeout'";
    conn.query(sql, function (err, res) {
        console.log(res);
    });
}

setInterval(query, 15*1000);
```

#### MySQl连接池超时测试
```
var selectSQL ="show variables like 'wait_timeout'";

pool.getConnection(function (err, conn) {
    if (err) console.log("POOL ==> " + err);
    function query(){
        conn.query(selectSQL, function (err, res) {
            console.log(new Date());
            console.log(res);
            conn.release();
        });
    }
    setInterval(query, 12 * 1000);
});
```

#### 事务
```
connection.beginTransaction(function(err) {
	if (err) { throw err; } 

	connection.query('INSERT INTO posts SET title=?', title, function(err, result) { 
		if (err) {  
			 connection.rollback(function() { 
				 throw err; 
			}); 
		}

		var log = 'Post ' + result.insertId + ' added'; 

		connection.query('INSERT INTO log SET data=?', log, function(err, result) { 
			if (err) {  
				connection.rollback(function() { 
					throw err; 
				}); 
			}
			connection.commit(function(err) { 
			if (err) {  
				connection.rollback(function() { 
		    		throw err; 
				}); 
			} 
			console.log('success!'); 
			}); 
		}); 
	}); 
}); 
```

#### 转义

- 字符串转义

在使用客户端输入的值来组装查询语句时，需要对这些值进行正确的转义，以避免遭受SQL注入攻击。可以使用`escape()`方法来对这些值进行转义：
```
	var sql = 'select * from User where id = ' + conn.escape("27 or id = 28");
	console.log(sql);
	conn.query(sql, function(err, resulst){
		console.log(resulst);
	});
```
还可以在SQL语句中将要转义的值用问号`?`来代替，再在调用`query()`方法时 传入这些待转义的值
```
	conn.query('select * from User where id=? or id=?', [27, 28], function(err, rs){
		console.log(rs);
	});
```
- 标识符转义

与字符串值的转义类似，标识符的转义可以使用`escapeId()`方法来实现：
```
var sorter = 'date';
var query = 'SELECT * FROM posts ORDER BY ' + mysql.escapeId(sorter);
console.log(query); // SELECT * FROM posts ORDER BY `date`
//
var sorter = 'date';
var query = 'SELECT * FROM posts ORDER BY ' + mysql.escapeId('posts.' + sorter);

console.log(query); // SELECT * FROM posts ORDER BY `posts`.`date`
```
也可以在 SQL 语句中使用两个问号??来表示要转义的标识符：
```
var userId = 1;
var columns = ['username', 'email'];
var query = connection.query('SELECT ?? FROM ?? WHERE id = ?', [columns, 'users', userId], function(err, results) {});

console.log(query.sql); // SELECT `username`, `email` FROM `users` WHERE id = 1
```



