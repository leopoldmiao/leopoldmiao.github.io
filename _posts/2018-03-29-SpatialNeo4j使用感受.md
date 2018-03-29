---
layout:     post
title:      <font color="black">Neo4jSpatial使用感受</font>
subtitle:   <font color="black">基础知识笔记</font>
date:       2018-03-09
author:     LM
header-img: img/post-bg-SpatialNeo4j.jpg
catalog: true
tags:
    - 图数据库
---

>相比于关系型数据库，图数据库在关联关系的查询上具有更好的性能。而空间索引多采用树结构，例如R树，天然可以存储在图数据库中，并利用图数据库的强大关联检索性能提供空间检索能力。本文使用了一个针对Neo4j进行扩展的空间索引plugin——[Neo4j Spatial][1]。

# 安装Neo4j

新版3.3.3的neo4j 的配置文件与之前略微不同。安装好以后，修改配置文件conf/neo4j.conf

将下面的两行前面的#去掉即可
```
#dbms.connector.http.listen_address=:7474
#dbms.connectors.default_listen_address=0.0.0.0
```

然后用浏览器连接http://192.168.204.128:7474/browser/

输入新密码

显示下面的结果，表示neo4j安装并配置完成。
```
You are connected as user neo4j
to the server bolt://192.168.204.128:7687
Connection credentials are stored in your web browser.
```

# 安装Neo4jSpatial
从https://github.com/neo4j-contrib/spatial/releases/tag/0.25.4-neo4j-3.2.8下载jar包neo4j-spatial-0.25.4-neo4j-3.2.8-server-plugin.jar

将其复制到/opt/neo4j/plugins里

重启neo4j


#Neo4jSpatial的简单使用
neo4j spatial被实现为一些存储过程，所以采用下面的方式调用（在浏览器的操作界面直接输入即可）
CALL spatial.addPointLayer('geom');
CALL spatial.layers();

CREATE (n:Node {latitude:60.1,longitude:15.2})
WITH n
CALL spatial.addNode('geom',n) YIELD node
RETURN node;

CALL spatial.bbox('geom',{lon:15.0,lat:60.0}, {lon:15.3, lat:61.0});

接着，在浏览器界面输入MATCH (n) return n
可以看到Neo4jSpatial的R树结构。


   [1]: https://github.com/neo4j-contrib/spatial
