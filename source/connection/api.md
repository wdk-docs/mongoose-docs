# 连接

## Connection()

**参数**

基于 «Mongoose» 一个猫鼬的例子

连接构造

出于实际的原因，连接等于Db。

## Connection.prototype.readyState

连接准备状态

* 0 = disconnected
* 1 = connected
* 2 = connecting
* 3 = disconnecting

每个状态更改都会发出其关联的事件名称

**示例**

    conn.on('connected', callback);
    conn.on('disconnected', callback);

## Connection.prototype.collections

与此连接关联的集合的散列

## Connection.prototype.db

当连接打开时设置的mongodb.Db实例

## Connection.prototype.config

与此连接关联的全局选项的散列

## Connection.prototype.createCollection()

**参数**

**返回**

## Connection.prototype.dropCollection()

**参数**

**返回**

`dropCollection()`助手. 将删除给定的集合，包括所有文档和索引.

## Connection.prototype.dropDatabase()

**参数**

**返回**

`dropDatabase()`助手. 删除给定的数据库，包括所有集合，文档和索引.

## Connection.prototype.close()

**参数**

* 回调«功能»选项

**返回**

## Connection.prototype.collection()

**参数**

* 选项«对象»可选收集选项

**返回**

* «Collection» 收集实例

检索一个集合，如果没有缓存则创建它
通常不需要应用程序。 Just talk to your collection through your model.

## Connection.prototype.model()

**参数**

* collection «String» name of mongodb collection (optional) if not given it will be induced from model name

**返回**

* «Model» 编译模式

定义或检索模型

    var mongoose = require('mongoose');
    var db = mongoose.createConnection(..);
    db.model('Venue', new Schema(..));
    var Ticket = db.model('Ticket', new Schema(..));
    var Venue = db.model('Venue');

_When no `collection` argument is passed, Mongoose produces a collection name by passing the model `name` to the utils.toCollectionName method. This method pluralizes the name. If you don't like this behavior, either pass a collection name or set your schemas collection name option._

**示例**

    var schema = new Schema({ name: String }, { collection: 'actor' });

    schema.set('collection', 'actor');

    var collectionName = 'actor'
    var M = conn.model('Actor', schema, collectionName)

## Connection.prototype.modelNames()

**返回**

返回在此连接上创建的模型名称数组。