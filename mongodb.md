
# MongoDB
#1 MongoDB相关概念
#1.1 业务应用场景
传统的关系型数据库（如MySQL），在数据操作的“三高”需求以及应对Web2.0的网站需求面前，显得力不从心。
解释：“三高”需求：

* High performance - 对数据库高并发读写的需求。
* Huge Storage - 对海量数据的高效率存储和访问的需求。
* High Scalability && High Availability- 对数据库的高可扩展性和高可用性的需求。

具体的应用场景如：

* 1）社交场景，使用 MongoDB 存储存储用户信息，以及用户发表的朋友圈信息，通过地理位置索引实现附近的人、地点等功能。
* 2）游戏场景，使用 MongoDB 存储游戏用户信息，用户的装备、积分等直接以内嵌文档的形式存储，方便查询、高效率存储和访问。
* 3）物流场景，使用 MongoDB 存储订单信息，订单状态在运送过程中会不断更新，以 MongoDB 内嵌数组的形式来存储，一次查询就能将
订单所有的变更读取出来。
* 4）物联网场景，使用 MongoDB 存储所有接入的智能设备信息，以及设备汇报的日志信息，并对这些信息进行多维度的分析。
* 5）视频直播，使用 MongoDB 存储用户信息、点赞互动信息等。

这些应用场景中，数据操作方面的共同特点是：
（1）数据量大
（2）写入操作频繁（读写都很频繁）
（3）价值较低的数据，对事务性要求不高
对于这样的数据，我们更适合使用MongoDB来实现数据的存储。

**什么时候选择MongoDB**
在架构选型上，除了上述的三个特点外，如果你还犹豫是否要选择它？可以考虑以下的一些问题：


- 应用不需要事务及复杂 join 支持新应用，需求会变，数据模型无法确定，想快速迭代开发
- 应用需要2000-3000以上的读写QPS（更高也可以）
- 应用需要TB甚至 PB 级别数据存储
- 应用发展迅速，需要能快速水平扩展
- 应用要求存储的数据不丢失
- 应用需要99.999%高可用
- 应用需要大量的地理位置查询、文本查询

如果上述有1个符合，可以考虑 MongoDB，2个及以上的符合，选择 MongoDB 绝不会后悔。
思考：如果用MySQL呢？
答：相对MySQL，可以以更低的成本解决问题（包括学习、开发、运维等成本）


#1.2 体系结构 #
![](mongodb_struct.png)

# 1.3 数据模型 #
MongoDB的最小存储单位就是**文档(document)对象**。文档(document)对象对应于关系型数据库的行。

数据在MongoDB中以**BSON（Binary-JSON）文档**的格式存储在磁盘上。

BSON（Binary Serialized Document Format）是一种类json的一种二进制形式的存储格式，简称Binary JSON。BSON和JSON一样，支持内嵌的文档对象和数组对象，但是BSON有JSON没有的一些数据类型，如Date和BinData类型。

BSON采用了类似于 C 语言结构体的名称、对表示方法，支持内嵌的文档对象和数组对象，具有轻量性、可遍历性、高效性的三个特点，可以有效描述非结构化数据和结构化数据。这种格式的优点是灵活性高，但它的缺点是空间利用率不是很理想。

# 2 单机部署 #
**2.1 Windows系统中的安装启动**

MongoDB的版本命名规范如：x.y.z；
y为奇数时表示当前版本为开发版，如：1.5.2、4.1.13；
y为偶数时表示当前版本为稳定版，如：1.6.3、4.0.10；

- 方式1：命令行参数方式启动服务

1.在mongodb目录下新建一个文件夹data，在其中创建文件夹db用于存放数据库位置

2.在 bin 目录中打开命令行提示符，输入如下命令：

    `mongod --dbpath=..\data\db`

我们在启动信息中可以看到，mongoDB的默认端口是27017，如果我们想改变默认的启动端口，可以通过--port来指定端口。
为了方便我们每次启动，可以将安装目录的bin目录设置到环境变量的path中， bin 目录下是一些常用命令，比如 mongod 启动服务用的，
mongo 客户端连接服务用的。
 
- 方式2：配置文件方式启动服务

在解压目录中新建 config 文件夹，该文件夹中新建配置文件 mongod.conf ，内如参考如下：		storage:	#The directory where the mongod instance stores its data.Default Value is "\data\db" on Windows.	dbPath: C:\mongodb-win32-x86_64-2008plus-ssl-4.0.28\data\db参考:https://www.mongodb.com/docs/manual/reference/configuration-options/**注意**

1. 配置文件中如果使用双引号，比如路径地址，自动会将双引号的内容转义。如果不转义，则会报错：解决：a. 对 \ 换成 / 或 \\b. 如果路径中没有空格，则无需加引号。s
2. 配置文件中不能以Tab分割字段		error-parsing-yaml-config-file-yaml-cpp-error-at-line-3-column-15-unknown-escape-character-d	解决：	将其转换成空格。***启动方式：***	mongod -f ../config/mongod.conf	或	mongod --config ../config/mongod.conf调试可以命令行启动简单，部署用配置文件。**2.2 Shell连接(mongo命令)**在命令提示符输入以下shell命令即可完成登陆		mongo	或	mongo --host=127.0.0.1 --port=27017查看已经有的数据库		show databases退出mongodb		exit**Linux环境下的mongodb**

- 同windozs下载压缩包，后创建目录并进行配置		#数据存储目录		mkdir -p /mongodb/single/data/db		#日志存储目录		mkdir -p /mongodb/single/log		#新建配置文件		vi /mongodb/single/mongod.conf

- 配置文件的模板
		systemLog:		#MongoDB发送所有日志输出的目标指定为文件		# #The path of the log file to which mongod or mongos should send all diagnostic logging information		destination: file		#mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径		path: "/mongodb/single/log/mongod.log"		#当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾。		logAppend: true		storage:		#mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod。		##The directory where the mongod instance stores its data.Default Value is "/data/db".		dbPath: "/mongodb/single/data/db"		journal:		#启用或禁用持久性日志以确保数据文件保持有效和可恢复。		enabled: true		processManagement:		#启用在后台运行mongos或mongod进程的守护进程模式。		fork: true		net:		#服务实例绑定的IP，默认是localhost		bindIp: localhost,192.168.0.2

- 通过进程来查看服务是否启动了：		[root@bobohost single]# ps -ef |grep mongod

- 停止关闭服务停止服务的方式有两种：快速关闭和标准关闭，下面依次说明：

**1. 快速关闭方法**（快速，简单，数据可能会出错）目标：通过系统的kill命令直接杀死进程：杀完要检查一下，避免有的没有杀掉			#通过进程编号关闭节点		kill -2 4096**补充**如果一旦是因为数据损坏，则需要进行如下操作（了解）：

1. 删除lock文件：
		rm -f /mongodb/single/data/db/*.lock

2. 修复数据：				usr/local/mongdb/bin/mongod --repair --dbpath=/mongodb/single/data/db
**2. 标准的关闭方法**（数据不容易出错，但麻烦）：目标：通过mongo客户端中的shutdownServer命令来关闭服务主要的操作步骤参考如下：	//客户端登录服务，注意，这里通过localhost登录，如果需要远程登录，必须先登录认证才行。	mongo --port 27017	//#切换到admin库	use admin	//关闭服务	db.shutdownServer()	