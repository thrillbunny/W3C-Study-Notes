# MongoDB 复习一 - 基础

mongod 的含义：

d 指的是 daemon，就是守护进程，linux 下的程序经常这么起名，表示它是一个服务性质的进程，相当于 windows 下的service。

mongod 是 mongodb 的服务端，类似于 mysql 的 mysqld。

mongod 参数（常用）：--port（指定服务端口号，默认端口27017），--dbpath（指定数据库路径），--logpath（指定MongoDB日志文件，注意是指定文件不是目录）

mongo 的含义：

一个命令行工具，用于连接一个特定的 mongod 实例。当没有带参数运行 mongo 命令它将使用默认的端口号和 localhost 连接。

---

MongoDB 是 C++ 语言编写的基于**分布式文件存储**的开源数据库，旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。

MongoDB 将数据存储为一个文档，数据结构由键值 (key=>value) 对组成。MongoDB 文档类似于 JSON 对象，字段值可以包含其他文档，数组及文档数组。

MongoDB 是一种非关系型数据库（NoSQL，not only sql),但它是非关系数据库当中功能最丰富，最像关系数据库的，下面介绍下 NoSQL 和分布式这两个概念。

## 1. NoSQL

NoSQL(NoSQL = Not Only SQL )，意即"不仅仅是 SQL "，是对不同于传统的关系型数据库的数据库管理系统的统称。

NoSQL 适用于超大规模数据的存储，如大数据和 Web 服务（例如谷歌或 Facebook 每天为他们的用户收集万亿比特的数据）。这些类型的数据存储不需要固定的模式，无需多余操作就可以横向扩展。

如今我们可以通过第三方平台（如：Google,Facebook 等）可以很容易的访问和抓取数据。用户的个人信息，社交网络，地理位置，用户生成的数据和用户操作日志已经成倍的增加。我们如果要对这些用户数据进行挖掘，那 SQL 数据库已经不适合这些应用了（表结构限制）, NoSQL 数据库的发展却能很好的处理这些大的数据。

NoSQL 具有高可扩展性、可以实现分布式计算、实现成本低（上面2点都是分布式计算系统带来的优势）、具有架构的灵活性和半结构化数据、没有复杂的数据关系；但相对的，它的数据结构没有标准化，查询功能有限，且**最终一致性**是不直观的程序。

#### 关系型数据库遵循ACID

事务在英文中是 transaction ，和现实世界中的交易很类似，指的是将一组读写操作组合在一起形成的一个逻辑单元，它有如下四个特性：

1. A (Atomicity) 原子性

事务里的所有操作要么全部做完，要么都不做，事务成功的条件是事务里的所有操作都成功，只要有一个操作失败，整个事务就失败，需要回滚。

比如银行转账，从A账户转100元至B账户，分为两个步骤：1）从A账户取100元；2）存入100元至B账户。这两步要么一起完成，要么一起不完成，如果只完成第一步，第二步失败，钱会莫名其妙少了100元。

2. C (Consistency) 一致性

数据库要一直处于一致的状态，事务的运行不会改变数据库原本的一致性约束。

例如现有完整性约束a+b=10，如果一个事务改变了a，那么必须得改变b，使得事务结束后依然满足a+b=10，否则事务失败。

3. I (Isolation) 隔离性

指并发的事务之间不会互相影响，如果一个事务要访问的数据正在被另外一个事务修改，只要另外一个事务未提交，它所访问的数据就不受未提交事务的影响。

比如现在有个交易是从A账户转100元至B账户，在这个交易还未完成的情况下，如果此时B查询自己的账户，是看不到新增加的100元的。

4. D (Durability) 持久性

持久性是指一旦事务提交后，它所做的修改将会永久的保存在数据库上，即使出现宕机也不会丢失。

### 分布式系统

分布式系统（distributed system）由多台计算机和通信的软件组件通过计算机网络连接（本地网络或广域网）组成。

分布式系统是建立在网络之上的**软件系统**，正是因为软件的特性，所以分布式系统具有高度的内聚性（每一个数据库分布节点高度自治，有本地的数据库管理系统）和透明性（每一个数据库分布节点对用户的应用来说都是透明的，看不出是本地还是远程）。

在分布式数据库系统中，用户感觉不到数据是分布的，即用户不须知道关系是否分割、有无副本、数据存于哪个站点以及事务在哪个站点上执行等。

分布式计算系统具有可靠性（容错）、可拓展性、资源共享、高速率和更高性能等优点，但也存在故障排除较难，相关软件支持少、网络传输可能存在的错误（传输问题，高负载，信息丢失等）、安全性低等问题。

### CAP定理（CAP theorem）

在计算机科学中, CAP定理（CAP theorem）, 又被称作**布鲁尔定理**（Brewer's theorem）, 它指出对于一个**分布式**计算系统来说，不可能同时满足以下三点:

- 一致性(Consistency) (所有节点在同一时间具有相同的数据)
- 可用性(Availability) (保证每个请求不管成功或者失败都有响应)
- 分隔容忍(Partition tolerance) (系统中任意信息的丢失或失败不会影响系统的继续运作)


CAP 理论的核心是：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求，最多只能同时较好的满足两个。

因此，根据 CAP 原理将 NoSQL 数据库分成了满足 CA 原则、满足 CP 原则和满足 AP 原则三大类：

- CA 单点集群，满足一致性，可用性的系统，通常在可扩展性上不太强大
- CP 满足一致性，分区容忍性的系统，通常性能不是特别高
- AP 满足可用性，分区容忍性的系统，通常可能对一致性要求低一些

### BASE

BASE：Basically Available, Soft-state, Eventually Consistent。

CAP 理论的核心是：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求，最多只能同时较好的满足两个。

BASE 是 NoSQL 数据库通常对可用性及一致性的弱要求原则:

- Basically Availble --基本可用
- Soft-state --软状态/柔性事务。 "Soft state" 可以理解为"无连接"的, 而 "Hard state" 是"面向连接"的
- Eventual Consistency --**最终一致性**， 也是是 ACID 的最终目的。

### RDBMS VS NoSQL， ACID VS BASE

RDBMS 
- 高度组织化结构化数据 
- 结构化查询语言（SQL） (SQL) 
- 数据和关系都存储在单独的表中。 
- 数据操纵语言，数据定义语言 
- 严格的一致性
- 基础事务

NoSQL 
- 代表着不仅仅是SQL
- 没有声明性查询语言
- 没有预定义的模式
- 键值对存储，列存储，文档存储，图形数据库
- 最终一致性，而非ACID属性
- 非结构化和不可预知的数据
- CAP定理 
- 高性能，高可用性和可伸缩性

### NoSQL 数据库分类

1. 列存储
2. 文档存储，如 MongoDB
3. key-value 存储，如 Redis
4. 图存储
5. 对象存储
6. xml 存储

## 2. MongoDB 基本概念


SQL术语/概念 | MongoDB术语/概念 |  解释/说明  
-|-|-
database | database | 数据库 |
table | collection | 数据库表/集合 |
row | document | 数据记录行/文档 |
column | field | 数据列/域（字段） |
index | index | 索引 |
table joins | ... | 表连接,MongoDB不支持，改用嵌入文档|
primary key | primary key | 主键,MongoDB自动将_id字段设置为主键 |

用图表示可以更加直观看出：

![DB-MongoDB](https://www.runoob.com/wp-content/uploads/2013/10/Figure-1-Mapping-Table-to-Collection-1.png)

1) 数据库

一个 mongodb 中可以建立多个数据库。MongoDB 的默认数据库为 "db"，该数据库存储在 data 目录中。

MongoDB 的单个实例可以容纳多个独立的数据库，每一个都有自己的集合和权限，不同的数据库也放置在不同的文件中。

"show dbs" 命令可以显示所有数据的列表，"db" 可以显示当前数据库对象或集合。

运行"use"命令，可以连接到一个指定的数据库。

	> use local
	switched to db local
	> db
	local

有一些数据库名是保留的，可以直接访问这些有特殊作用的数据库。

- admin：从权限的角度来看，这是 "root" 数据库。要是将一个用户添加到这个数据库，这个用户自动继承所有数据库的权限。一些特定的服务器端命令也只能从这个数据库运行，比如列出所有的数据库或者关闭服务器。

- local: 这个数据永远不会被复制，可以用来存储限于本地单台服务器的任意集合。

- config: 当Mongo用于分片设置时，config数据库在内部使用，用于保存分片的相关信息。

2） 文档(Document)

文档是一组键值 (key-value) 对 (即 BSON，Binary Serialized Document Format)。

MongoDB 的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型，这与关系型数据库有很大的区别，也是 MongoDB 非常突出的特点。

	{"site":"www.runoob.com", "name":"菜鸟教程"}

需要注意的是：

1. 文档中的键/值对是**有序**的。

2. 文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。

3. MongoDB 区分类型和大小写。

4. MongoDB 的文档不能有重复的键。

5. 文档的键是字符串。除了少数例外情况，键可以使用任意 UTF-8 字符。

这里简要介绍一下 **BSON**：

BSON( Binary Serialized Document Format) 是一种类 json 的一种二进制形式的存储格式，简称 Binary JSON，它和 JSON 一样，支持内嵌的文档对象和数组对象，但是 BSON 有 JSON 没有的一些数据类型，如 Date 和 BinData 类型。BSON 主要用于 MongoDB ，是 mongodb 的数据存储格式。

BSON有三个特点：轻量性、可遍历性、高效性。BSON 是基于 JSON 格式改造的，比较 JSON 有以下三个优势：

1. 更快的遍历速度

对 JSON 格式来说，太大的 JSON 结构会导致数据遍历非常慢。在 JSON 中，要跳过一个文档进行数据读取，需要对此文档进行扫描才行，需要进行麻烦的数据结构匹配，比如括号的匹配，而 BSON 对 JSON 的一大改进就是，它会将 JSON 的每一个元素的长度存在元素的头部，这样你只需要读取到元素长度就能直接 seek 到指定的点上进行读取了。（比较类似 C++ 里面）

2. 操作更简易

对 JSON 来说，数据存储是无类型的，比如你要修改基本一个值，从9到10，由于从一个字符变成了两个，所以可能其后面的所有内容都需要往后移一位才可以。而使用 BSON，你可以指定这个列为数字列，那么无论数字从9长到10还是100，我们都只是在存储数字的那一位上进行修改，不会导致数据总长变大。当然，在 MongoDB 中，如果数字从整形增大到长整型，还是会导致数据总长变大的。

3. 增加了额外的数据类型

JSON 是一个很方便的数据交换格式，但是其类型比较有限。BSON 在其基础上增加了“byte array”数据类型。这使得二进制的存储不再需要先 base64 转换后再存成 JSON。大大减少了计算开销和数据大小。

下面看两个简单的 BSON 结构体：

	{
	    title:"MongoDB",
	    last_editor:"192.168.1.122",
	    last_modified:new Date("27/06/2011"),
	    body:"MongoDB introduction",
	    categories:["Database","NoSQL","BSON"],
	    revieved:false
	}

	
	{
	    name:"lemo",
	    age:"12",
	    address:{
	        city:"suzhou",
	        country:"china",
	        code:215000
	    } ,
	    scores:[
	        {"name":"english","grade:3.0},
	        {"name":"chinese","grade:2.0}
	    ]
	}

3） 集合（Collection）

集合就是 MongoDB **文档组**，类似于 RDBMS （关系数据库管理系统：Relational Database Management System)中的**表格**。

集合存在于数据库中，集合没有固定的结构，这意味着你在对集合可以插入不同格式和类型的数据，但通常情况下我们插入集合的数据都会有一定的关联性。

	{"site":"www.baidu.com"}
	{"site":"www.google.com","name":"Google"}
	{"site":"www.runoob.com","name":"菜鸟教程","num":5}

这里介绍一个我没有涉及过的概念 capped collections：

Capped collections 就是固定大小的 collection，它有很高的性能以及队列过期的特性(过期按照插入的顺序)。

Capped collections 是高性能自动的维护对象的插入顺序。它非常适合类似记录日志的功能。和标准的 collection 不同，你必须要显式的创建一个 capped collection，指定一个 collection 的大小，单位是**字节**。collection 的数据存储空间值**提前分配**的。

Capped collections 可以按照文档的插入顺序保存到集合中，而且这些文档在磁盘上存放位置也是按照插入顺序来保存的，所以当我们更新 Capped collections 中文档的时候，更新后的文档不可以超过之前文档的大小，这样话就可以确保所有文档在磁盘上的位置一直保持不变。

由于 Capped collection 是按照文档的**插入顺序**（？？）而不是使用索引确定插入位置，这样的话可以提高增添数据的效率。MongoDB 的操作日志文件 oplog.rs 就是利用 Capped Collection 来实现的。

4） 元数据

数据库的信息是存储在集合中。它们使用了系统的命名空间：

	dbname.system.*

在MongoDB数据库中名字空间 <dbname>.system.* 是包含多种系统信息的特殊集合(Collection)，如下:

集合命名空间 | 描述
-|-
dbname.system.namespaces | 列出所有**名字空间**
dbname.system.indexes | 列出所有索引
dbname.system.profile | 包含数据库概要(profile)信息
dbname.system.users | 列出所有可访问数据库的用户
dbname.local.sources | 包含复制对端（slave）的服务器信息和状态

在 {{system.indexes}} 插入数据，可以创建索引，但除此之外该表信息是不可变的(特殊的 drop index 命令将自动更新相关信息)， {{system.users}} 是可修改的，{{system.profile}} 是可删除的。

5） 数据类型

**ObjectId**

ObjectId 类似唯一主键（primary key），可以很快的去生成和排序，包含 12 bytes，含义是：

- 前 4 个字节表示创建 unix 时间戳,格林尼治时间 UTC 时间，比北京时间晚了 8 个小时
- 接下来的 3 个字节是机器标识码
- 紧接的两个字节由进程 id 组成 PID
- 最后三个字节是随机数

![ObjectId](https://www.runoob.com/wp-content/uploads/2013/10/2875754375-5a19268f0fd9b_articlex.jpeg)

MongoDB 中存储的文档必须有一个 _id 键,这个键的值可以是任何类型的，默认是个 ObjectId 对象。

由于 ObjectId 中保存了创建的时间戳，所以不需要为你的文档保存时间戳字段，可以通过 getTimestamp 函数来获取文档的创建时间。

	> var newObject = ObjectId()
	> newObject.getTimestamp()
	ISODate("2017-11-25T07:21:10Z")

**字符串**

BSON 字符串都是 UTF-8 编码

**时间戳**

BSON 有一个特殊的时间戳类型用于 MongoDB 内部使用，与普通的日期 类型不相关。 时间戳值是一个 64 位的值。其中：

- 前32位是一个 time_t 值（与Unix新纪元相差的秒数）
- 后32位是在某秒中操作的一个递增的序数

在单个 mongod 实例中，时间戳值通常是唯一的。

在复制集中， oplog 有一个 ts 字段。这个字段中的值使用BSON时间戳表示了操作时间。

BSON 时间戳类型主要用于 MongoDB 内部使用。在大多数情况下的应用开发中，也可以使用 BSON 日期类型。

**日期**

表示当前距离 Unix新纪元（1970年1月1日）的毫秒数。日期类型是有符号的, 负数表示 1970 年之前的日期。

	> var mydate1 = new Date()     //格林尼治时间
	> mydate1
	ISODate("2018-03-04T14:58:51.233Z")
	> typeof mydate1
	object

	> var mydate2 = ISODate() //格林尼治时间
	> mydate2
	ISODate("2018-03-04T15:00:45.479Z")
	> typeof mydate2
	object

可以使用 toString() 方法返回一个时间类型的字符串：

	> var mydate1str = mydate1.toString()
	> mydate1str
	Sun Mar 04 2018 14:58:51 GMT+0000 (UTC) 
	> typeof mydate1str
	string

## 3. MongoDB 基本操作

### 连接（基本操作的第一步）

我们平时在使用 MongoDB 时首先需要与它建立连接，标准 URI 连接语法：

	mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]

其中 port 默认端口是 27017。

?options 是连接选项。如果不使用 /database，则前面需要加上 /。所有连接选项都是键值对 key=value，键值对之间通过 & 或 ;（分号）隔开。

标准的连接格式包含了多个选项(options)，其中有几个涉及到了 [MongoDB ReplicaSet(副本集)](https://www.cnblogs.com/keithtt/p/6536883.html)，这个我目前还没涉及到，之后讨论。

看一组连接实例：

a. 连接本地数据库服务器，端口是默认的(27017)

	mongodb://localhost

b. 使用用户名fred，密码foobar登录localhost的admin数据库

	mongodb://fred:foobar@localhost

c. 使用用户名fred，密码foobar登录localhost的baz数据库

	mongodb://fred:foobar@localhost/baz

d. 连接 replica pair, 服务器1为 example1.com 服务器2为example2.com

	mongodb://example1.com:27017,example2.com:27017

e. 连接 **replica set** 三台服务器 (端口 27017, 27018, 和27019)

	mongodb://localhost,localhost:27018,localhost:27019

f. 连接 replica set 三台服务器, 写入操作应用在主服务器 并且分布查询到从服务器

	mongodb://host1,host2,host3/?slaveOk=true

g. 直接连接第一个服务器，无论是replica set一部分或者主服务器或者从服务器

	mongodb://host1,host2,host3/?connect=direct;slaveOk=true

f&g 涉及到 options 选项 slaveOk=true|false ：

- true: 在 connect=direct 模式下，驱动会连接第一台机器，即使这台服务器不是主；在 connect=replicaSet 模式下，驱动会发送所有的写请求到主并且把读取操作分布在其他从服务器。

- false: 在 connect=direct 模式下，驱动会自动找寻主服务器；在connect=replicaSet 模式下，驱动仅仅连接主服务器，并且所有的读写命令都连接到主服务器。

h. 安全模式连接到localhost:

	mongodb://localhost/?safe=true

i. 以安全模式连接到 replica set，并且等待至少两个复制服务器成功写入，超时时间设置为2秒:

	mongodb://host1,host2,host3/?safe=true;w=2;wtimeoutMS=2000

### 数据库创建和删除

创建数据库语法：

	use DATABASE_NAME

删除数据库语法：

	db.dropDatabase()

来看一组实例：

	> use tmp
	switched to db tmp
	> db
	tmp
	> show dbs
	admin      0.000GB
	config     0.000GB
	local      0.000GB
	test       0.000GB
	> db.tmp.insert({"name":"thrilly"})
	WriteResult({ "nInserted" : 1 })
	> show dbs
	admin      0.000GB
	config     0.000GB
	local      0.000GB
	test       0.000GB
	tmp        0.000GB

**TIPS：** 

1. 在 MongoDB 中，集合只有在内容插入后才会创建! 就是说，创建集合(数据表)后要再插入一个文档(记录)，集合才会真正创建。所以第一次输入 show dbs 时没有显示新创建的数据库；

2. MongoDB 中默认的数据库为 test，如果你没有创建新的数据库，集合将存放在 test 数据库中。

	> use tmp
	switched to db tmp
	> db.dropDatabase()
	{ "dropped" : "tmp", "ok" : 1 }

### 集合创建和删除

创建集合语法：

	db.createCollection(name, options)

其中：

- name： 要创建的集合名称

- options: 可选参数, 指定有关内存大小及索引的选项，包括：

字段 | 类型 | 描述
- | - | -
capped|布尔|①
size|数值|②
max|数值|③

①（可选）如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。当该值为 true 时，必须指定 size 参数。
②（可选）为固定集合指定一个最大值（以字节计）。如果 capped 为 true，也需要指定该字段。
③（可选）指定固定集合中包含文档的最大数量。

删除集合语法：

	db.collection.drop()

一组实例：

	> use tmp
	switched to db tmp
	> db.createCollection("test")
	{ "ok" : 1 }
	> show collections
	test
	> show tables
	test
	> db.createCollection("test2",{capped:true,size:6000,max:1000})
	{ "ok" : 1 }
	> db.test2.drop()
	true

其中，查看已有集合，可以使用 show collections 或 show tables 命令。

在 MongoDB 中，不需要创建集合。当你插入一些文档时，MongoDB 会自动创建集合。

	> db.test3.insert({"name":"thrilly"})
	WriteResult({ "nInserted" : 1 })
	> show collections
	test
	test3

### 增删改查

**增：**MongoDB 使用 insert() 或 save() 方法向集合中插入文档，语法如下：

	db.COLLECTION_NAME.insert(document)

实例如下：

	> db.test3.insert({"name":"thrilly"})
	WriteResult({ "nInserted" : 1 })
	> db.test3.save({title:"MongoDB学习",tags:["mongodb","database","NoSQL"],likes:100})
	WriteResult({ "nInserted" : 1 })

查看已插入文档：

	> db.test3.find()
	{ "_id" : ObjectId("5d403ab8c053a1c710b555fc"), "name" : "thrilly" }
	{ "_id" : ObjectId("5d403b6ac053a1c710b555fd"), "title" : "MongoDB学习", "tags" : [ "mongodb", "database", "NoSQL" ], "likes" : 100 }
	> db.test3.find().pretty()
	{ "_id" : ObjectId("5d403ab8c053a1c710b555fc"), "name" : "thrilly" }
	{
	        "_id" : ObjectId("5d403b6ac053a1c710b555fd"),
	        "title" : "MongoDB学习",
	        "tags" : [
	                "mongodb",
	                "database",
	                "NoSQL"
	        ],
	        "likes" : 100
	}

3.2 版本后还有以下几种语法可用于插入文档:

- db.collection.insertOne():向指定
- 
- 
- 中插入一条文档数据
- db.collection.insertMany():向指定集合中插入多条文档数据

	> db.test3.insertOne({"a": 3})
	{
	        "acknowledged" : true,
	        "insertedId" : ObjectId("5d403cdfc053a1c710b555ff")
	}
	> db.test3.insertMany([{"b": 3}, {'c': 4}])
	{
	        "acknowledged" : true,
	        "insertedIds" : [
	                ObjectId("5d403cfcc053a1c710b55600"),
	                ObjectId("5d403cfcc053a1c710b55601")
	        ]
	}

也可以把数据定义为一个变量再执行插入操作：

	> document=({title:"Happy every day",by:"thrilly",date:new Date(),likes:100})
	{
	        "title" : "Happy every day",
	        "by" : "thrilly",
	        "date" : ISODate("2019-07-30T12:47:25.603Z"),
	        "likes" : 100
	}
	> db.test3.insert(document)
	WriteResult({ "nInserted" : 1 })

据此可以采用新的方法插入多条数据：

	> var arr = [];
	> for(var i = 0; i <= 10; i++){
	... arr.push({num:i})
	... }
	11
	> db.test3.insert(arr)
	BulkWriteResult({
	        "writeErrors" : [ ],
	        "writeConcernErrors" : [ ],
	        "nInserted" : 11,
	        "nUpserted" : 0,
	        "nMatched" : 0,
	        "nModified" : 0,
	        "nRemoved" : 0,
	        "upserted" : [ ]
	})

**改：** MongoDB 使用 update() 和 save() 方法来更新集合中的文档，其中update() 方法用于**更新**已存在的文档，语法格式如下：

	db.collection.update(
	   <query>,
	   <update>,
	   {
	     upsert: <boolean>,
	     multi: <boolean>,
	     writeConcern: <document>
	   }
	)

参数说明：

- query : update的查询条件，类似sql update查询内where后面的。
- update : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的。
- upsert : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
- multi : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
- writeConcern :可选，抛出异常的级别。

	> db.test3.update({"title":"MongoDB学习"},{$set:{"title":"Mongo"}})
	WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
	> db.test3.find().pretty()                                       })
	{ "_id" : ObjectId("5d403ab8c053a1c710b555fc"), "name" : "thrilly" }
	{
	        "_id" : ObjectId("5d403b6ac053a1c710b555fd"),
	        "title" : "Mongo",
	        "tags" : [
	                "mongodb",
	                "database",
	                "NoSQL"
	        ],
	        "likes" : 100
	}
	{
	        "_id" : ObjectId("5d403c6fc053a1c710b555fe"),
	        "title" : "Happy every day",
	        "by" : "thrilly",
	        "date" : ISODate("2019-07-30T12:47:25.603Z"),
	        "likes" : 100
	}

以上语句只会修改第一条发现的文档，如果你要修改多条相同的文档，则需要设置 multi 参数为 true。

save() 方法通过传入的文档来**替换**已有文档，语法格式如下：

	db.collection.save(
	   <document>,
	   {
	     writeConcern: <document>
	   }
	)

3.2版本开始，MongoDB提供以下更新集合文档的方法：

- db.collection.updateOne() 向指定集合更新单个文档
- db.collection.updateMany() 向指定集合更新多个文档

	> db.test_collection.insert( [
	... {"name":"abc","age":"25","status":"zxc"},
	... {"name":"dec","age":"19","status":"qwe"},
	... {"name":"asd","age":"30","status":"nmn"},
	... ] )
	> db.test_collection.updateOne({"name":"abc"},{$set:{"age":"28"}})
	{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
	> db.test_collection.find()
	{ "_id" : ObjectId("5d40448b4aba77d2cd3d9995"), "name" : "abc", "age" : "28", "status" : "zxc" }
	{ "_id" : ObjectId("5d40448b4aba77d2cd3d9996"), "name" : "dec", "age" : "19", "status" : "qwe" }
	{ "_id" : ObjectId("5d40448b4aba77d2cd3d9997"), "name" : "asd", "age" : "30", "status" : "nmn" }
	> db.test_collection.updateMany({"age":{$gt:"10"}},{$set:{"status":"xyz"}})
	{ "acknowledged" : true, "matchedCount" : 3, "modifiedCount" : 3 }
	> db.test_collection.find()
	{ "_id" : ObjectId("5d40448b4aba77d2cd3d9995"), "name" : "abc", "age" : "28", "status" : "xyz" }
	{ "_id" : ObjectId("5d40448b4aba77d2cd3d9996"), "name" : "dec", "age" : "19", "status" : "xyz" }
	{ "_id" : ObjectId("5d40448b4aba77d2cd3d9997"), "name" : "asd", "age" : "30", "status" : "xyz" }

**删：** deleteOne() 和 deleteMany() 方法语法如下：

	db.collection.deleteOne(
	   <filter>,
	   {
	      writeConcern: <document>,
	      collation: <document>
	   }
	)

	db.collection.deleteMany(
	   <filter>,
	   {
	      writeConcern: <document>,
	      collation: <document>
	   }
	)

删除集合下全部文档：

	db.inventory.deleteMany({})

删除 status 等于 A 的全部文档：

	db.inventory.deleteMany({ status : "A" })

删除 status 等于 D 的一个文档：

	db.inventory.deleteOne({ status: "D" })

**查：**MongoDB 查询文档使用 find() 方法，find() 方法以非结构化的方式来显示所有文档，查询数据的语法格式如下：

	db.collection.find(query, projection)

如果你需要以易读的方式来读取数据，可以使用 pretty() 方法，以格式化方法显示所有文档，语法格式如下：

	db.collection.find().pretty()

若不指定 projection，则默认返回所有键，指定 projection 格式如下，有两种模式：

	db.collection.find(query, {title: 1, by: 1}) // inclusion模式 指定返回的键，不返回其他键
	db.collection.find(query, {title: 0, by: 0}) // exclusion模式 指定不返回的键,返回其他键

但其中 _id 默认返回，需要主动指定 _id:0 才会隐藏。

两种模式不可混用（因为这样的话无法推断其他键是否应返回）：

	db.collection.find(query, {title: 1, by: 0}) // 错误

只能全1或全0，除了在inclusion模式时可以指定_id为0：

	db.collection.find(query, {_id:0, title: 1, by: 1}) // 正确

若不想指定查询条件参数 query 可以 用 {} 代替，但是需要指定 projection 参数：

	querydb.collection.find({}, {title: 1}）

还有一个查询方式，只返回一个文档：

	db.collection.findOne(query, projection)

**与操作**：MongoDB 的 find() 方法可以传入多个键(key)，每个键(key)以**逗号**隔开，即常规 SQL 的 AND 条件，语法格式如下：

	db.col.find({key1:value1, key2:value2}).pretty()

就类似 WHERE 语句：WHERE key1="value1" AND key2="value2"

还可以指定字段：

	db.col.find("opt":{key1:value1, key2:value2}).pretty()

**或操作**：MongoDB OR 条件语句使用了关键字 $or,语法格式如下：

	db.collection.find(
		{
			$or: [
				{key1:value1},{key2:value2}
			]
		}
	).pretty()

**AND和OR联合：**类似常规 SQL 语句为： 'where likes>50 AND (by = '菜鸟教程' OR title = 'MongoDB 教程')'

	db.col.find({"likes": {$gt:50}, $or: [{"by": "菜鸟教程"},{"title": "MongoDB 教程"}]}).pretty()

另外还有如下语法，将增删改查相结合：

	db.collection.findAndModify({
	    query: <document>,
	    sort: <document>,
	    remove: <boolean>,
	    update: <document>,
	    new: <boolean>,
	    fields: <document>,
	    upsert: <boolean>,
	    bypassDocumentValidation: <boolean>,
	    writeConcern: <document>,
	    collation: <document>,
	    arrayFilters: [ <filterdocument1>, ... ]
	});

但这一方法只能更新一条文档。

	db.collection.findOneAndDelete(
	   <filter>,
	   {
	     projection: <document>,
	     sort: <document>,
	     maxTimeMS: <number>,
	     collation: <document>
	   }
	)
	
	db.collection.findOneAndReplace(
	   <filter>,
	   <replacement>,
	   {
	     projection: <document>,
	     sort: <document>,
	     maxTimeMS: <number>,
	     upsert: <boolean>,
	     returnNewDocument: <boolean>,
	     collation: <document>
	   }
	)
	
	db.collection.findOneAndUpdate(
	   <filter>,
	   <update>,
	   {
	     projection: <document>,
	     sort: <document>,
	     maxTimeMS: <number>,
	     upsert: <boolean>,
	     returnNewDocument: <boolean>,
	     collation: <document>,
	     arrayFilters: [ <filterdocument1>, ... ]
	   }
	)

### 修改器（$inc/$set/$unset/$push/$pop/upsert......）

对于文档的更新除替换外，针对某个或多个文档只需要部分更新可使用**原子**的更新修改器，能够高效的进行文档更新。更新修改器是中特殊的键，
用来指定复杂的操作，比如增加、删除或者调整键，还可能是操作数组或者内嵌文档。

**$inc:** 修改器 $inc 可以对文档的某个值为数字型（只能为满足要求的数字）的键进行增减的操作。

	> db.b.insert({"uid":"201203","type":"1",size:10})
	> db.b.find()
	{ "_id" : ObjectId("5003b6135af21ff428dafbe6"), "uid" : "201203", "type" : "1",
	"size" : 10 }
	> db.b.update({"uid" : "201203"},{"$inc":{"size" : 1}})
	> db.b.find()
	{ "_id" : ObjectId("5003b6135af21ff428dafbe6"), "uid" : "201203", "type" : "1",
	"size" : 11 }
	> db.b.update({"uid" : "201203"},{"$inc":{"size" : -1}})
	> db.b.find()
	{ "_id" : ObjectId("5003b6135af21ff428dafbe6"), "uid" : "201203", "type" : "1",
	"size" : 10 }

**$set:**用来指定一个键并更新键值，若键不存在并创建。

	> db.a.findOne({"uid" : "20120002","type" : "3"})
	{ "_id" : ObjectId("500216de81b954b6161a7d8f"), "desc" : "hello world2!", "num"
	: 40, "sname" : "jk", "type" : "3", "uid" : "20120002" }
	--size键不存在的场合
	> db.a.update({"uid" : "20120002","type" : "3"},{"$set":{"size":10}})
	> db.a.findOne({"uid" : "20120002","type" : "3"})
	{ "_id" : ObjectId("500216de81b954b6161a7d8f"), "desc" : "hello world2!", "num"
	: 40, "size" : 10, "sname" : "jk", "type" : "3", "uid" : "20120002" }
	--sname键存在的场合
	> db.a.update({"uid" : "20120002","type" : "3"},{"$set":{"sname":"ssk"}})
	> db.a.find()
	{ "_id" : ObjectId("500216de81b954b6161a7d8f"), "desc" : "hello world2!", "num"
	: 40, "size" : 10, "sname" : "ssk", "type" : "3", "uid" : "20120002" }
	{ "_id" : ObjectId("50026affdeb4fa8d154f8572"), "desc" : "hello world1!", "num"
	: 50, "sname" : "jk", "type" : "1", "uid" : "20120002" }

**$unset:**用来删除键值对，只需指定 key 值，value 使用1、0、-1或者具体的字符串等都是可以删除该目标键。


**$push:**向文档的某个数组类型的键添加一个**数组**元素，不过滤重复的数据。添加时键存在，要求键值类型必须是数组；键不存在，则创建数组类型的键。

	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "type" : "suv",
	"size" : { "height" : 8, "width" : 7, "length" : 15 } }
	--先push一个当前文档中不存在的键title
	> db.c.update({"name" : "toyota"},{$push:{"title":"t1"}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1" ], "type" : "suv" }
	--再向title中push一个值
	> db.c.update({"name" : "toyota"},{$push:{"title":"t2"}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t2" ], "type" : "suv" }

**数组修改器--$ne/$addToSet**

给数组类型键值添加一个元素时，避免在数组中产生重复数据（也就是实现 set 数组）

	> db.c.update({"title" : {$ne:"t2"}},{$push:{"title":"t2"}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t2", "t2" ], "type" : "suv" }
	
	> db.c.update({"name" : "toyota"},{$addToSet:{"title":"t2"}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t2", "t2" ], "type" : "suv" }

**$pop:**

$pop从数组的头或者尾删除数组中的元素：

	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t2", "t3", "t4" ],"type" : "suv" }

	--从数组的尾部删除 1
	
	> db.c.update({"name" : "toyota"},{$pop:{"title":1}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t2", "t3" ], "type" : "suv" }

	--从数组的头部 -1
	
	> db.c.update({"name" : "toyota"},{$pop:{"title":-1}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t2", "t3" ], "type" : "suv" }

	--从数组的尾部删除 0
	
	> db.c.update({"name" : "toyota"},{$pop:{"title":0}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t2" ], "type" : "suv" }

**$pull:**

从数组中删除满足条件的元素：

	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t2", "t2", "t3" ],"type" : "suv" }
	 
	> db.c.update({"name" : "toyota"},{$pull:{"title":"t2"}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t3" ], "type" : "suv" }

**数组的定位修改器：**

在需要对数组中的值进行操作的时候，可通过位置或者定位操作符（"$"）.数组是0开始的，可以直接将下标作为键来选择元素。

	{"uid":"001",comments:[{"name":"t1","size":10},{"name":"t2","size":12}]}
	
	> db.c.find({"uid":"001"})
	{ "_id" : ObjectId("5003da405af21ff428dafbe8"), "uid" : "001", "comments" : [ {
	"name" : "t1", "size" : 10 }, { "name" : "t2", "size" : 12 } ] }
	> db.c.update({"uid":"001"},{$inc:{"comments.0.size":1}})
	> db.c.find({"uid":"001"})
	{ "_id" : ObjectId("5003da405af21ff428dafbe8"), "uid" : "001", "comments" : [ {
	"name" : "t1", "size" : 11 }, { "name" : "t2", "size" : 12 } ] }
	> db.c.update({"comments.name":"t1"},{$set:{"comments.$.size":1}})
	> db.c.find({"uid":"001"})
	{ "_id" : ObjectId("5003da405af21ff428dafbe8"), "uid" : "001", "comments" : [ {
	"name" : "t1", "size" : 1 }, { "name" : "t2", "size" : 12 } ] }

**$upsert:**

upsert是一种特殊的更新。当没有符合条件的文档，就以这个条件和更新文档为基础创建一个新的文档，如果找到匹配的文档就正常的更新。

使用upsert，既可以避免竞态问题，也可以减少代码量（update的第三个参数就表示这个upsert，boolean类型参数）

	> db.c.remove()
	> db.c.update({"size":11},{$inc:{"size":3}})
	> db.c.find()
	> db.c.update({"size":11},{$inc:{"size":3}},false)
	> db.c.find()
	> db.c.update({"size":11},{$inc:{"size":3}},true)
	> db.c.find()
	{ "_id" : ObjectId("5003ded6c28f67507a6df1de"), "size" : 14 }

**$save:**

$save 实现相对而言比较复杂，类似一种自适应实现：

1. 可以在文档不存在的时候插入，存在的时候更新，只有一个参数文档；
2. 要是文档含有"_id"，会调用upsert，否则，会调用插入。



本章节参考[mongodb_修改器（$inc/$set/$unset/$push/$pop/upsert......）](https://www.cnblogs.com/chen-lhx/p/6004623.html)，具体请查看文档里内容，非常详细。

### 条件操作符

MongoDB中条件操作符有：

- (>) 大于 - $gt
- (<) 小于 - $lt
- (>=) 大于等于 - $gte
- (<= ) 小于等于 - $lte
- (!=) not equal - $ne
- (=) equal - $eq
- 基于BSON类型来检索集合中匹配的数据类型，并返回结果 - $type 

比如：

	db.col.find({likes : {$gt : 100}})

就类似 SQL 语句：

	select * from col where likes > 100;

又如：

	db.col.find({"title" : {$type : 2}})

就等于：

	db.col.find({"title" : {$type : "string"}})

模糊查询（类似正则表达式）：

查询 title 包含"教"字的文档：

	db.col.find({title:/教/})

查询 title 字段以"教"字开头的文档：

	db.col.find({title:/^教/})

查询 titl e字段以"教"字结尾的文档：

	db.col.find({title:/教$/})