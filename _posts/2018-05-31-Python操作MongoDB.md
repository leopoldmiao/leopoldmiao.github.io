---
layout:     post
title:       <font color="black">Python操作MongoDB</font>
subtitle:   <font color="black">创建索引，增删改查</font>
date:       2018-05-31
author:     LM
header-img: img/post-bg-PythonMongoDB.jpg
catalog: true
tags:
    - MongoDB
    - Python
---

>MongoDB是一个文档数据库，它存储数据的方式是类似于JSON格式的BSON格式文档。即将传统数据库中的记录（行）以一个BSON文档进行存储，例如｛“Name”：“James”，“Age”：“18”｝就是一个文档，或者说传统数据库表中的一条记录。MongoDB里的BSON文档也会组织为Collection的形式，相当于传统关系数据库中的表。但不同的是Collection中的BSON文件可以具有不同的属性名和值。例如名为Student的Collection中可以包含有｛“Name”：“James”，“Age”：“18”｝和｛“Name”：“Tom”，“grade”：“1”｝，甚至｛“Name”：“Mary”｝。因此，MongoDB善于存储稀疏数据，且易于扩展属性列。个人认为用来存储日志或爬虫采集的数据是非常适合的。在具体使用时，需要注意以下几个问题：

# 安装配置
安装MongoDB后，需要在系统变量PATH中添加XXX\MongoDB\Server\3.4\bin，以便命令行调用时可以直接使用mongod命令。
此外，需要手工创建MongoDB数据的存储位置。通常情况下的结构如下：I:\MongoDBData，其下有两个目录db和log，再在log中创建MongoDB.log文件。

# 启动与测试
命令行输入mongod -dbpath "N:\MongoDBData\db"即可启动MongoDB数据库服务进程。
此时，就可以用客户端来操作MongoDB数据库了。打开MongoDB安装目录下bin中的mongo.exe，就可以启动Shell。在其中输入数据库操作命令可以对数据库进行管理和查询。

# 基本命令

（1）show dbs    显示所有数据库，但是如果数据库里没有任何集合，是不展示出来的。

（2）use test 连接数据库test，切换数据库时如果数据库不存在，则会创建它。

（3）db.XXXX()     在连接某个数据库以后，就可以用各种以db开头的db.XXXX()函数进行数据库操作了。
例如，db.getCollectionNames()获得数据库中的所有Collection（数据库表）
db.createCollection("abcd")在该数据库中创建名为abcd的Collection（数据库表）

（4）db.abcd.XXX()   针对数据库表abcd的各种增删改查操作。
插入数据
db.abcd.insert ({"name":"jack","age":30})

查找数据
db.abcd.find()                  --全查
db.abcd.findOne()               --只取多个的第一个
 db.abcd.find({"name":"jack"})   --根据条件查找

更新数据
db.abcd.update({"name":"jack"},{"name":"jack","age":18})
注意，前面的参数是查询条件，后面的是新数据，如果不加操作符$set，就会覆盖整个文档，在新数据内容中未提及的字段都会被舍弃。
例如，db.abcd.update({"name":"jack"},{"age":18})更新效果就是新文档数据为{"age":18}，没有了name字段。。。。

删除文档（行）或集合（表）
db.abcd.remove()                --删除所有文档（行），集合保留
db.abcd.remove({"name":"jack"}) --删除复合条件的文档
db.abcd.drop()                  --删除集合，与此同时所有文档也删除了

（5）创建索引
注意在 3.0.0 版本前创建索引方法为 db.abcd.ensureIndex()，之后的版本使用了 db.abcd.createIndex() 方法，ensureIndex() 还能用，但只是 createIndex() 的别名。
ensure_index([("fid", 1), ("date", 1)], unique=True)为两列属性建立了联合索引，1表示升序，-1表示降序。
ensure_index("fid", unique=True)  也可以不指定升降序。

# Python操作MongDB
调用方式参见下面的例子：
```
from pymongo import MongoClient
def getFundList(self):  
	conn = MongoClient('127.0.0.1', 27017) #连接MongoDB
	db = conn.funds  # 连接数据库funds，如果没有则创建
	fundList = db.FundList  # 使用FundList表，如果没有则创建
	lists = []

	cursor = fundList.find() 
	#find的参数中没有查询条件，则查询全部数据。find({"name":"zhangsan"})这种是加条件的查询

	for f in cursor:  
		# print f["fid"] #查询出来的f就是一个字典对象，字典可以用key作为[]来检索属性值。
		fund = Fund()
		fund.id = f["fid"]
		fund.name = f["fname"]
		fund.infourl = f["finfo_url"]
		lists.append(fund)
	conn.close()
	return lists
```



