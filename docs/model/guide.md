# 模型

[资源](http://mongoosejs.com/docs/models.html "Mongoose v5.0.1的永久链接：模型")

[模型s][1] are fancy constructors compiled from our `Schema` definitions. Instances of these models represent [documents][2] which can be saved and retrieved from our database. All document creation and retrieval from the database is handled by these models.

## 编译你的第一个模型

var schema = new mongoose.Schema({ name: 'string', size: 'string' }); var Tank = mongoose.model('Tank', schema);

The first argument is the _singular_ name of the collection your model is for. ** Mongoose automatically looks for the _plural_ version of your model name. ** Thus, for the example above, the model Tank is for the **tanks** collection in the database. The `.model()` function makes a copy of `schema`. Make sure that you've added everything you want to `schema` before calling `.model()`!

## 构建文档

[Documents][2] are instances of our model. Creating them and saving to the database is easy:

var Tank = mongoose.model('Tank', yourSchema); var small = new Tank({ size: 'small' }); small.save(function (err) { if (err) return handleError(err); // saved! }) // or Tank.create({ size: 'small' }, function (err, small) { if (err) return handleError(err); // saved! })

Note that no tanks will be created/removed until the connection your model uses is open. Every model has an associated connection. When you use `mongoose.model()`, your model will use the default mongoose connection.

mongoose.connect('localhost', 'gettingstarted');

If you create a custom connection, use that connection's `model()` function instead.

var connection = mongoose.createConnection('mongodb://localhost:27017/test'); var Tank = connection.model('Tank', yourSchema);

## 查询

Finding documents is easy with Mongoose, which supports the [rich][3] query syntax of MongoDB. Documents can be retreived using each `models` [find][4], [findById][5], [findOne][6], or [where][7] static methods.

Tank.find({ size: 'small' }).where('createdDate').gt(oneYearAgo).exec(callback);

See the chapter on [querying][8] for more details on how to use the [Query][9] api.

## 删除

Models have a static `remove` method available for removing all documents matching `conditions`.

Tank.remove({ size: 'large' }, function (err) { if (err) return handleError(err); // removed! });

## 更新

Each `model` has its own `update` method for modifying documents in the database without returning them to your application. See the [API][10] docs for more detail.

_If you want to update a single document in the db and return it to your application, use [findOneAndUpdate][11] instead._

## 还有更多

The [API docs][12] cover many additional methods available like [count][13], [mapReduce][14], [aggregate][15], and [more][16].

## 接下来

现在我们已经介绍了`Models`，让我们来看看[Documents][17].

[1]: http://mongoosejs.com/api.html#model-js
[2]: http://mongoosejs.com/documents.html
[3]: http://www.mongodb.org/display/DOCS/Advanced+Queries
[4]: http://mongoosejs.com/api.html#model_Model.find
[5]: http://mongoosejs.com/api.html#model_Model.findById
[6]: http://mongoosejs.com/api.html#model_Model.findOne
[7]: http://mongoosejs.com/api.html#model_Model.where
[8]: http://mongoosejs.com/queries.html
[9]: http://mongoosejs.com/api.html#query-js
[10]: http://mongoosejs.com/api.html#model_Model.update
[11]: http://mongoosejs.com/api.html#model_Model.findOneAndUpdate
[12]: http://mongoosejs.com/api.html#model_Model
[13]: http://mongoosejs.com/api.html#model_Model.count
[14]: http://mongoosejs.com/api.html#model_Model.mapReduce
[15]: http://mongoosejs.com/api.html#model_Model.aggregate
[16]: http://mongoosejs.com/api.html#model_Model.findOneAndRemove
[17]: http://mongoosejs.com/docs/documents.html


