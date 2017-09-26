# MongoDB

## 简介
	MongoDB被称为文档型数据库，当然也叫nosql数据库（不使用sql作为查询，同时也没有关系型数据库严格的表结构）

	注意：表与表之间的关系叫做E-R。但是redis和mongoDB都不存在E-R模型，不叫关系型数据库，被称为非关系型数据库。

	> 网站：http://www.nosql-database.org/  收录了很多nosql数据库。

	文档：存储的是文档，专业术语叫做Bson(Bson->json的二进制化后的数据)

	> 扩展：百度的地图接口【geojson】

	特点：mongoDB内部使用js解释引擎来实现数据的分析，在插入的时候，讲数据转换成二进制的Bson来存储；
	在查询的时候，将数据Bson转换成json对象返回。

	注意：一般MongoDB数据库主要是和node.js(web服务器和一个js解释器)配合使用。

	+ MongoDB和传统型数据库的区别
		1. mongoDB没有表结构的概念，传统的关系型数据库必须按照表的结构（行，列形成的二维表，
			二维表之间形成实体关系，ER模型）来存储
		2. 在mongoDB中没有表的概念，将表换了一种称呼，叫做集合(Collection),由多个collection
			组成起来叫做数据库
		3. 在mysql中的记录，在mongoDB中叫做document文档

		资料：http://www.runoob.com/mongodb/mongodb-tutorial.html

## 安装
	1. 下载对应的稳定的源代码，并放置到(/usr/local/src)
		官网：www.mongodb.org
		中文手册：http://www.runoob.com/mongodb/mongodb-tutorial.html
		中文社区：http://www.mongoing.com/
				  http://www.open-open.com/
				  http://www.infoq.com.cn

	2. 上传，解压文件

		> tar zxvf packageName -C dest
		-C 代表解压的目录

	3. 为mongoDB创建一个符合连接
		
		> ln -sv mongodb-linux-i686-3.0.6 mongodb

		注释：ln命令用来为文件创建连接，连接类型分为硬连接和符合连接，默认的连接类型是硬连接，
		如果要创建符合连接必须使用"-s"选项

		-v 显示指令执行过程
	
	4. 查看bin目录下的文件
		mongod属于c/s架构的软件，mongod代表的是服务器端
		mongo代表的是客户端，如果要使用mongodb,必须先启动服务，然后使用客户端连接。

	5. 如何启动服务？
		a. 创建db文件夹【保存mongodb运行时候的数据库信息】
			> mkdir /data/dn -pv

		b. 启动服务
			> ./bin/mongod --dbpath=/data/db --logpath=/data/db/log.txt --fork

			参数说明
			--dbpath:代表数据库保存的目录
			--logpath:代表日志文件log.txt
			--fork:代表服务在后台运行

		+ 扩展：如何在Linux下查看一个服务是否正常的启动？【1、进程名称；2、服务端口】

			1. > ps -axu | grep mongod
			2. > netstat -tunple | grep 27017

	6. 如何停止，关闭mongodb?

		答：使用kill命令，不要使用pkill -9;
		pkill -9 进程名称，把进程全部干掉。-9 代表的就是一个信号量
	
	7. 如何去连接服务器端

		答：使用mongo客户端进行连接
		> ./bin/mongo --host localhost --port 27017
		类比 > mysql -hlocalhost -p3306 -uroot -p

	简单使用：只需要遵循js语法，基本都可以使用。能使用的只是ECMAScript定义的函数

## mongoDB使用

### ①-入门命令
	1.	> show dbs; 查看当前的数据库

	2. > use databaseName; 选库
		答：MongoDB可以隐式的创建，直接use databaseName 可以创建一个数据库

	3. > show tables; 查看当前库下的数据表
	   > show collections; 也可以查看
		注意:即使数据库不存在，也可以直接切换，mongodb会自己创建	
	
	4. 创建数据库
		mongodb的库是隐式创建的，当use一个不存在的库的时候，然后在该库下创建tables(collection)后
		即可创建数据库
	
#### 创建表
  	1. 显示创建
  		> db.createCollection('collectionName');

  	2. 隐式创建
  		> db.collectionName.insert(document);
  		向一个不存在的表中插入数据的时候，隐式的创建表

#### 删除表
  	> db.collectionName.drop();

#### 删除数据库
  	> db.dropDatabase();

### ②-基本的增删改查命令

#### -增加数据

	> db.collectionName.insert(document);
	注意：这里的document是一个json对象

#### -删除数据

	> db.remove(查询表达式, 选项);  	
	
	注意：1. 查询表达式依旧是json对象
		  2. 查询表达式匹配的行，将被删掉
		  3. 如果不写查询表达式，tables中的所有文档将被删除

#### -修改数据
	
	新值直接替换旧值，*而不是修改*
	> db.collectionName.update(查询表达式， 新值);
	
	例：db.news.update({name:'wamp'}, {name:'lamp'});
	这样修改后，文档中的其他列将消失，只剩_id和name列数据。

	要想修改数据的某列，可以使用$set关键字
	例：
		> db.collectionName.update({name:'wamp'}, {$set:{name:'lamp'}});

#### -查询数据 find, findOne
	
	> db.collectionName.find(查询表达式, 显示的列);

	例子： 1. > db.user.find();  //查询所有数据
		   2. > db.user.find({}, {gender:1}); 	//查询所有文档分数据，但是只显示gender列数据
		   3. > db.user.find({}, {gender:1, _id:0});  
		   //查询所有文档数据，但是显示gender列数据，并且不显示_id列数据
		   4. > db.user.find({gender:'male'},{name:1,_id:0});	
		   //查询所有gender值为male的数据，但是只显示name这一列

	> db.collectionName.findOne(查询表达式, 显示的列);

	例子： > db.students.findOne({gender:male}, {male:1,_id:0});
			//查询所有gender为male的数据，但是只显示male这列信息，并且只获得一条文档


## php操作mongoDB

	一般使用php客户端来操作mongoDB,对我们php程序员来说很少使用CLI 

### ①-windows下安装
	
	1. 下载源码
	下载地址：http://pecl.php.net/package/mongo

	2. 点击对应的【dll:dynamic link library 动态链接 window用来操作mongoDB的扩展】

	3. 选择合适的php版本，这个是时候需要注意的是，选择版本的时候，存在php线程安全选择
		
		注意：线程安全的查看，使用phpinfo函数，进行查看以下信息
		PHP Extension Build
			NTS: 非线性安全
			TS： 线性安全

	4. 复制压缩包中的两个文件到php的ext目录下

	5. 修改php.ini文件

	6. 重启Apache，见到mongo扩展则代表成功

### ②-Linux下安装
	
	1. 上传扩展包到/usr/local/src【php操作mongoDB的扩展文件】
		解压：
			> unzip mogo-php-driver-1.6.zip

	2. 执行phpize命令
		> /usr/local/php/bin/phpize

		执行上面的操作后，利用生成的configure文件，收集操作系统和php-config命令的信息
		> ./configure --with-php-config=/usr/local/php/bin/php-config

	3. 执行make&&make install

	4. 执行上面的操作后会在如下的目录中生成一个.so文件 

	5. 修改php.ini
		;extension_dir="/usr/local/php/lib/php/extensions/no-debug-non-zts-20090626"
		;extension="mongo.so"

	6. 重启Apache

### 创建测试文件
	
	``` 
	<?php 

	 	$mongo = new MongoClient('mongodb://127.0.0.1:27017');

	 	// 连接数据库并选择表，如果不存在，则创建
	 	$tableObj = $mongo->selectCollection('dbname', 'students');
	 	$data = array('name'=>'vamair', 'age'=>22);
	 	$status = $tableObj->insert($data);

	 	var_dump($status);

	 	$list = $tableObj->find();
	 	foreach ($list as $k => $v) {
	 		var_dump($v);
	 	}
	 ```
