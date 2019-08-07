# MongoDB 复习二 - 进阶

---

## 4. MongoDB 进阶操作

### 4.1 查询限制 Limit 与 Skip 

文档：
	
	{ "_id" : ObjectId("56066542ade2f21f36b0313a"), "title" : "PHP 教程", "description" : "PHP 是一种创建动态交互性站点的强有力的服务器端脚本语言。", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "php" ], "likes" : 200 }
	{ "_id" : ObjectId("56066549ade2f21f36b0313b"), "title" : "Java 教程", "description" : "Java 是由Sun Microsystems公司于1995年5月推出的高级程序设计语言。", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "java" ], "likes" : 150 }
	{ "_id" : ObjectId("5606654fade2f21f36b0313c"), "title" : "MongoDB 教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "mongodb" ], "likes" : 100 }

如果要在 MongoDB 中读取指定数量的数据记录，可以使用 MongoDB 的 Limit 方法，limit() 方法接受一个数字参数，该参数指定从 MongoDB 中读取并显示的记录条数。

limit() 方法基本语法如下所示：

	db.COLLECTION_NAME.find().limit(NUMBER)

如果没有指定 limit 参数则显示集合中所有的数据。

	> db.col.find({},{"title":1,_id:0}).limit(2)
	{ "title" : "PHP 教程" }
	{ "title" : "Java 教程" }

还可以使用 skip() 方法在**符合条件**的记录中从第一个记录跳过指定数量的数据，skip 方法同样接受一个数字参数作为跳过的记录条数。

skip() 方法脚本语法格式如下：

	 db.COLLECTION_NAME.find().limit(NUMBER).skip(NUMBER)

如下文实例只会显示第二条文档数据：

	>db.col.find({},{"title":1,_id:0}).limit(2).skip(1)
	{ "title" : "Java 教程" }

这个实例指的就是在符合条件的文档中，要显示两条文档，显示的位置从跳过第一条记录开始，这样看不是很直观，但如果改为：

	>db.col.find({},{"title":1,_id:0}).skip(1).limit(2)

就会很好理解，两个的意思是一样的，skip 和 limit，包括 sort，三者的执行顺序**和位置无关**，是存在固定顺序的：

**先 sort(), 然后是 skip()，最后是显示的 limit()**，

但是在**聚合 aggregate**中使用的时候，具有管道流的特质，执行顺序是按照位置关系顺序执行的。

如果想要读取从 10 条记录后 100 条记录，相当于 sql 中 limit (10,100)，则可以结合使用：

	> db.COLLECTION_NAME.find().skip(10).limit(100)

通过二者的结合可以实现**数据分页**，但只适合小数据量分页，如果是百万级效率就会非常低，因为skip方法是一条条数据数过去的，建议使用where_limit。

### 2. 排序 sort

在 MongoDB 中使用 sort() 方法对数据进行排序，sort() 方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而 -1 是用于降序排列。

sort()方法基本语法如下所示：

	db.COLLECTION_NAME.find().sort({KEY:1})

比如实例：

	>db.col.find({},{"title":1,_id:0}).sort({"likes":-1})

### 3. 索引

索引通常能够极大的提高查询的效率，如果没有索引，MongoDB在读取数据时必须扫描集合中的每个文件并选取那些符合查询条件的记录。

索引是特殊的数据结构，索引存储在一个易于遍历读取的数据集合中，索引是对数据库表中一列或多列的值进行排序的一种结构。

MongoDB使用 createIndex() 方法来创建索引，其基本语法结构为：

	db.collection.createIndex(keys, options)

语法中 Key 值为你要创建的索引字段，1 为指定按升序创建索引，如果你想按降序来创建索引指定为 -1 即可,比如：

	>db.col.createIndex({"title":1})

createIndex() 方法中你也可以设置使用多个字段创建索引（关系型数据库中称作复合索引）：

	>db.col.createIndex({"title":1,"description":-1})

createIndex() 接收可选参数，主要介绍几个：

- background（Boolean）：建索引过程会阻塞其它数据库操作，background可指定以后台方式创建索引，即增加 "background" 可选参数。 "background" 默认值为false。

- unique（Boolean）：建立的索引是否唯一。指定为true创建唯一索引。默认值为false。

- weights（document）：索引权重值，数值在 1 到 99,999 之间，表示该索引相对于其他索引字段的得分权重。

比如在后台创建索引：

	db.values.createIndex({open: 1, close: 1}, {background: true})

除了创建索引外还有以下索引相关操作：

- 查看集合索引

	db.col.getIndexes()

- 查看集合索引大小

	db.col.totalIndexSize()

- 删除集合所有索引

	db.col.dropIndexes()

- 删除集合指定索引

	db.col.dropIndex("Index_Name")

还可以利用 TTL 集合对存储的数据进行失效时间设置：经过指定的时间段后或在指定的时间点过期，MongoDB **独立线程**去清除数据，类似于设置定时自动删除任务，可以清除历史记录或日志等前提条件，设置 Index 的关键字段为日期类型 new Date()。

例如数据记录中 **createDate** 为日期类型时：

- 设置时间180秒后自动清除。
- 设置在创建记录后，180 秒左右删除。

	db.col.createIndex({"createDate": 1},{expireAfterSeconds: 180})

由记录中设定日期点清除。

设置 A 记录在 2019 年 1 月 22 日晚上 11 点左右删除，A 记录中需添加 "ClearUpDate": new Date('Jan 22, 2019 23:00:00')，且 Index中expireAfterSeconds 设值为 0。

	db.col.createIndex({"ClearUpDate": 1},{expireAfterSeconds: 0})

其他注意事项:

- 索引关键字段必须是 **Date** 类型。
- 非立即执行：扫描 Document 过期数据并删除是独立线程执行，默认 60s 扫描一次，删除也不一定是立即删除成功。
- 单字段索引，混合索引不支持。

### 4. 聚合

MongoDB 中聚合(aggregate) 主要用于处理数据(诸如统计平均值,求和等)，并返回计算后的数据结果，类似 sql 语句中的 count(*)。

aggregate() 方法的基本语法格式如下所示：

	>db.COLLECTION_NAME.aggregate(AGGREGATE_OPERATION)

现有集合中数据段：

	{
	   _id: ObjectId(7df78ad8902c)
	   title: 'MongoDB Overview', 
	   description: 'MongoDB is no sql database',
	   by_user: 'runoob.com',
	   url: 'http://www.runoob.com',
	   tags: ['mongodb', 'database', 'NoSQL'],
	   likes: 100
	},
	{
	   _id: ObjectId(7df78ad8902d)
	   title: 'NoSQL Overview', 
	   description: 'No sql database is very fast',
	   by_user: 'runoob.com',
	   url: 'http://www.runoob.com',
	   tags: ['mongodb', 'database', 'NoSQL'],
	   likes: 10
	},
	{
	   _id: ObjectId(7df78ad8902e)
	   title: 'Neo4j Overview', 
	   description: 'Neo4j is no sql database',
	   by_user: 'Neo4j',
	   url: 'http://www.neo4j.com',
	   tags: ['neo4j', 'database', 'NoSQL'],
	   likes: 750
	},

通过以上集合计算每个作者所写的文章数，使用aggregate()计算结果如下：

	> db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : 1}}}])
	{
	   "result" : [
	      {
	         "_id" : "runoob.com",
	         "num_tutorial" : 2
	      },
	      {
	         "_id" : "Neo4j",
	         "num_tutorial" : 1
	      }
	   ],
	   "ok" : 1
	}

就类似 sql 语句：

	select by_user as _id, count(*) as num_tutorial from mycol group by by_user

通过字段 by_user 字段对数据进行分组，并计算 by_user 字段相同值的总和。

常见聚合表达式有：

$sum，计算总和
	
	db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : "$likes"}}}])

$avg，计算平均值

	db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$avg : "$likes"}}}])

$min,$max 获取集合中所有文档对应值得最小（大）值	

$first,$last 根据资源文档的排序获取第一个（最后一个）文档数据

$push 在结果文档中插入值到一个数组中

	db.mycol.aggregate([{$group : {_id : "$by_user", url : {$push: "$url"}}}])

$addToSet 在结果文档中插入值到一个数组中，但不创建副本

聚合中存在**管道**的概念，即将 MongoDB 文档在一个管道处理完毕后将结果传递给下一个管道处理。管道操作是可以重复的。

表达式：处理输入文档并输出。表达式是无状态的，只能用于计算当前聚合管道的文档，不能处理其它的文档。

聚合框架中常用操作有：

- $project: 修改输入文档的结构，可以用来重命名、增加或删除域，也可以用于创建计算结果以及嵌套文档。

- $match：用于过滤数据，只输出符合条件的文档。$match使用MongoDB的标准查询操作。

- $limit：用来限制MongoDB聚合管道返回的文档数。

- $skip：在聚合管道中跳过指定数量的文档，并返回余下的文档。

- $sort：将输入文档排序后输出。(注意聚合中的 limit、skip 和 sort 执行顺序是按顺序进行的）

- $unwind：将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值。

- $group：将集合中的文档**分组**，可用于统计结果。

- $geoNear：输出接近某一地理位置的有序文档。

如：

	db.article.aggregate(
	    { $project : {
	        title : 1 ,
	        author : 1 ,
	    }}
	 );

结果中就只还有_id,tilte和author三个字段了，默认情况下_id字段是被包含的，可以设置为 0 使它不再显示。

	db.articles.aggregate( [
                        { $match : { score : { $gt : 70, $lte : 90 } } },
                        { $group: { _id: null, count: { $sum: 1 } } }
                       ] );

### 7. MongoDB 备份(mongodump)与恢复(mongorestore)

在 Mongodb 中我们使用 mongodump 命令来备份 MongoDB 数据，该命令可以导出所有数据到指定目录中。

mongodump 命令可以通过参数指定导出的数据量级转存的服务器，命令语法脚本如下：

	mongodump -h dbhost -d dbname -o dbdirectory

- -h

MongDB所在服务器地址，例如：127.0.0.1，可以指定端口号：127.0.0.1:27017

- -d

需要备份的数据库实例，例如：test

- -o

备份的数据存放位置，例如：c:\data\dump，该目录需要提前建立，在备份完成后，系统自动在 dump 目录下建立一个 test 目录，这个目录里面存放该数据库实例的备份数据。

mongodb 使用 mongorestore 命令来恢复备份的数据，命令脚本语法如下：

	mongorestore -h <hostname><:port> -d dbname <path>

- --host <:port>, -h <:port>

MongoDB所在服务器地址，默认为： localhost:27017

- --db, -d

需要恢复的数据库实例，例如：test，当然这个名称也可以和备份时候的不一样，比如test2

- --drop

恢复的时候，先删除当前数据，然后恢复备份的数据

- <path>

mongorestore 最后的一个参数，设置备份数据所在位置，例如：c:\data\dump\test。不能同时指定 <path> 和 --dir 选项，--dir也可以设置备份目录。

- --dir

指定备份的目录

### 8. MongoDB 监控

MongoDB 中提供了 mongostat 和 mongotop 两个命令来监控 MongoDB 的运行情况。

mongostat 是 mongodb 自带的状态检测工具，在命令行下使用。它会间隔固定时间获取 mongodb 的当前运行状态，并输出。

mongotop 也是 mongodb 下的一个内置工具，mongotop 提供了一个方法，用来跟踪一个 MongoDB 的实例，查看哪些大量的时间花费在读取和写入数据。mongotop 提供每个集合的水平的统计数据。

### 9. MongoDB 关系（同一集合内）

MongoDB 的关系表示多个文档之间在逻辑上的相互联系，文档间可以通过嵌入和引用来建立联系。

MongoDB 中的关系可以是：

- 1:1 (1对1)
- 1: N (1对多)
- N: 1 (多对1)
- N: N (多对多)

比如有一个用户文档和一个地址文档：

	{
	   "_id":ObjectId("52ffc33cd85242f436000001"),
	   "name": "Tom Hanks",
	   "contact": "987654321",
	   "dob": "01-01-1991"
	}

	{
	   "_id":ObjectId("52ffc4a5d85242602e000000"),
	   "building": "22 A, Indiana Apt",
	   "pincode": 123456,
	   "city": "Los Angeles",
	   "state": "California"
	} 

采用**嵌入式方法**，将用户地址嵌入到用户文档中：

	{
	   "_id":ObjectId("52ffc33cd85242f436000001"),
	   "contact": "987654321",
	   "dob": "01-01-1991",
	   "name": "Tom Benzamin",
	   "address": [
	      {
	         "building": "22 A, Indiana Apt",
	         "pincode": 123456,
	         "city": "Los Angeles",
	         "state": "California"
	      },
	      {
	         "building": "170 A, Acropolis Apt",
	         "pincode": 456789,
	         "city": "Chicago",
	         "state": "Illinois"
	      }]
	} 

这个数据保存在**单一**文档中，便于获取和维护数据，可以通过下面的方法查询用户地址：

	db.users.findOne({"name":"Tom Benzamin"},{"address":1})

但这种单一结构会随着数据量的增加影响读写性能，且比较难观察和维护。

采用**引用式关系**对上面的文档进行改进，这种方法把用户数据文档和用户地址数据文档分开，通过引用文档的 id 字段来建立关系。

	{
	   "_id":ObjectId("52ffc33cd85242f436000001"),
	   "contact": "987654321",
	   "dob": "01-01-1991",
	   "name": "Tom Benzamin",
	   "address_ids": [
	      ObjectId("52ffc4a5d85242602e000000"),
	      ObjectId("52ffc4a5d85242602e000001")
	   ]
	}

以上实例中，用户文档的 address_ids 字段包含用户地址的对象id（ObjectId）数组。

我们可以读取这些用户地址的对象id（ObjectId）来获取用户的详细地址信息。

这种方法需要**两次查询**，第一次查询用户地址的对象id（ObjectId），第二次通过查询的id获取用户的详细地址信息。

	>var addId = db.users.findOne({"name":"Tom Benzamin"},{"address_ids":1})
	>var addresses = db.address.find({"_id":{"$in":addId["address_ids"]}})

注意这一句中的 findOne 不能写成 find，因为 find 返回的数据类型是数组，findOne 返回的数据类型是对象。

如果这一句使用了 find，那么下面一句应该改写为:

	>var addresses = db.address.find({"_id":{"$in":result[0]["address_ids"]}})

### 10. 数据库引用（不同集合）

一个文档从多个集合引用文档，我们应该使用 DBRefs，形式如下：

	{ $ref : 集合名, $id : 引用id, $db : 数据库名陈，参数可选 }

以下实例中用户数据文档使用了 DBRef, 字段 address：

	{
	   "_id":ObjectId("53402597d852426020000002"),
	   "address": {
			   "$ref": "address_home",
			   "$id": ObjectId("534009e4d852427820000002"),
			   "$db": "runoob"
		},
	   "contact": "987654321",
	   "dob": "01-01-1991",
	   "name": "Tom Benzamin"
	}

以下代码中，我们通过指定 $ref 参数（address_home 集合）来查找集合中指定id的用户地址信息：

	>var user = db.users.findOne({"name":"Tom Benzamin"})
	>var dbRef = user.address
	>db[dbRef.$ref].findOne({"_id":ObjectId(dbRef.$id)})

以上实例返回了 address_home 集合中的地址数据。

### 11. MongoDB 原子操作

mongodb **不支持事务**，无论什么设计，都不要要求 mongodb 保证数据的完整性。

但是 mongodb 提供了许多原子操作，比如文档的保存，修改，删除等，都是原子操作。

原子操作符包括：

$set（指定一个键并更新键值，若键不存在并创建），$unset（删除一个键），

$inc（对文档的某个值为数字型（只能为满足要求的数字）的键进行增减的操作），

$push，$pull，$pop，$addToSet，

$rename（{ $rename : { old_field_name : new_field_name } }），

$bit（位操作，integer类型，{$bit : { field : {and : 5}}}）

详细的可以见上一章**修改器**部分。

### 12. 固定集合（Capped Collections）

MongoDB 固定集合（Capped Collections）是性能出色且有着固定大小的集合，对于大小固定，我们可以想象其就像一个环形队列，当集合空间用完后，再插入的元素就会覆盖**最初始的头部**的元素。

通过createCollection来创建一个固定集合，且capped选项设置为true：

	>db.createCollection("cappedLogCollection",{capped:true,size:10000})

还可以指定文档个数,加上max:1000属性：

	>db.createCollection("cappedLogCollection",{capped:true,size:10000,max:1000})

判断集合是否为固定集合:

	>db.cappedLogCollection.isCapped()

如果需要将已存在的集合转换为固定集合可以使用以下命令：

	>db.runCommand({"convertToCapped":"posts",size:10000})

以上代码将已存在的 posts 集合转换为固定集合。

固定集合文档按照插入顺序储存的,默认情况下查询就是按照**插入顺序**返回的,也可以使用 $natural 调整返回顺序。

	>db.cappedLogCollection.find().sort({$natural:-1})

固定集合可以插入及更新,但更新不能超出 collection 的大小,否则更新失败,**不允许删除**,但是可以调用 drop() 删除集合中的所有行,但是 drop 后需要显式地重建集合。

在32位机子上一个 cappped collection 的最大值约为482.5M,64位上只受系统文件大小的限制。

固定集合由于其本身特点存在以下几个优点：

- 插入速度极快
- 按照插入顺序的查询输出速度极快
- 能够在插入最新数据时,淘汰最早的数据

根据其特点，固定集合非常适合实现**缓存日志信息**或者缓存一些少量文档的功能。

### 12. MongoDB 复制（副本集）

MongoDB复制是将数据同步在多个服务器的过程。

复制提供了数据的冗余备份，并在多个服务器上存储数据副本，提高了数据的可用性， 并可以保证数据的安全性。复制还允许从硬件故障和服务中断中恢复数据。

复制有以下优点：

- 安全性（保障数据的安全性，数据高可用性24*7，灾难恢复）
- 无需停机维护（如备份，重建索引，压缩）
- 分布式读取数据

#### MongoDB 复制原理

MongoDB 的复制至少需要两个节点，一个是主节点，一个是从节点，主节点负责处理客户端请求，从节点负责复制主节点上的数据。

MongoDB 各个节点常见的搭配方式为：一主一从、一主多从。

主节点记录在其上的所有操作 oplog，从节点**定期轮询**主节点获取这些操作，然后对自己的数据副本执行这些操作，从而保证从节点的数据与主节点一致。

MongoDB 复制结构图如下所示：

![MongoDB 复制结构图](https://www.runoob.com/wp-content/uploads/2013/12/replication.png)

以上结构图中，客户端从主节点读取数据，在客户端写入数据到主节点时， 主节点与从节点进行数据交互保障数据的一致性。

也就是说所有的写入操作都发生在主节点上，但副本集中任何节点都可以作为主节点，当主节点发生故障时实现自动故障转移和自动恢复（从节点自动接管，变为主节点），提高安全性和可用性。

#### MongoDB 副本集设置和成员添加

通过指定 --replSet 选项来启动mongoDB。--replSet 基本语法格式如下：

	mongod --port "PORT" --dbpath "YOUR_DB_DATA_PATH" --replSet "REPLICA_SET_INSTANCE_NAME"

添加副本集的成员，我们需要使用多台服务器来启动 mongo 服务。进入 Mongo 客户端，并使用 rs.add() 方法来添加副本集的成员，基本语法如下：

	rs.add(HOST_NAME:PORT)

实例：

	mongo --port 27017 --dbpath "D:\set up\mongodb\data" --replSet mongod1.net

以上实例会启动一个名为 mongod1.net 的 MongoDB 实例，其端口号为27017，启动后打开命令提示框并连接上mongoDB服务。

在Mongo客户端使用命令 rs.initiate() 来启动一个新的副本集，用 rs.conf() 来查看副本集的配置，查看副本集状态使用 rs.status() 命令。

假设你已经启动了一个名为 mongod1.net，端口号为27017的 Mongo 服务，在客户端命令窗口使用 rs.add() 命令将其添加到副本集中，命令如下所示：

	>rs.add("mongod1.net:27017")

MongoDB 中你只能通过**主节点**将 Mongo 服务添加到副本集中， 判断当前运行的 Mongo 服务是否为主节点可以使用命令 db.isMaster() 。

### 13. MongoDB 分片

在 MongoDB 里面存在另一种集群，就是分片技术,可以满足 MongoDB 、数据量大量增长的需求。

当 MongoDB 存储海量的数据时，一台机器可能不足以存储数据，也可能不足以提供可接受的读写吞吐量。这时，我们就可以通过在**多台机器上分割数据**，使得数据库系统能存储和处理更多的数据。

使用分片的其他原因：

- 复制所有的写入操作到主节点
- 延迟的敏感数据会在主节点查询（？）
- 单个副本集限制在12个节点
- 当请求量巨大时会出现内存不足
- 本地磁盘不足
- 垂直扩展价格昂贵

下图展示了在MongoDB中使用分片集群结构分布：

[MongoDB分片](https://www.runoob.com/wp-content/uploads/2013/12/sharding.png)

上图中主要有如下所述三个主要组件：

- Shard:
用于存储实际的数据块，实际生产环境中一个 shard server 角色可由几台机器组个一个 replica set 承担，防止主机单点故障。

- Config Server:
mongod 实例，存储了整个 ClusterMetadata，其中包括 chunk 信息。

- Query Routers:
前端路由，客户端由此接入，且让整个集群看上去像单一数据库，前端应用可以透明使用。

###MongoDB 真的好难！！