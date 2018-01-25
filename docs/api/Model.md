
# 模型

## Model()

**参数**

* doc «Object» values with which to create the document

Model constructor

Provides the interface to MongoDB collections as well as creates document instances.

## Model.prototype.db

Connection the model uses.

## Model.prototype.collection

Collection the model uses.

## Model.prototype.modelName

## Model.prototype.$where

Additional properties to attach to the query when calling `save()` and `isNew` is false.

## Model.prototype.baseModelName

If this is a discriminator model, `baseModelName` is the name of the base model.

## Model.prototype.save()

**参数**

* [fn] «Function» optional callback

**返回**

Saves this document.

**示例**


    product.sold = Date.now();
    product.save(function (err, product) {
      if (err) ..
    })

The callback will receive three parameters

1. `err` if an error occurred
2. `product` which is the saved `product`

As an extra measure of flow control, save will return a Promise.

**示例**


    product.save().then(function(product) {
       ...
    });

## Model.prototype.increment()

Signal that we desire an increment of this documents version.

**示例**


    Model.findById(id, function (err, doc) {
      doc.increment();
      doc.save(function (err) { .. })
    })

## Model.prototype.remove()

**参数**

* [fn] «function(err,product)» optional callback

**返回**

Removes this document from the db.

**示例**


    product.remove(function (err, product) {
      if (err) return handleError(err);
      Product.findById(product._id, function (err, product) {
        console.log(product)
      })
    })

As an extra measure of flow control, remove will return a Promise (bound to `fn` if passed) so it could be chained, or hooked to recive errors

**示例**


    product.remove().then(function (product) {
       ...
    }).catch(function (err) {
       assert.ok(err)
    })

## Model.prototype.model()

**参数**

* name «String» model name

Returns another Model instance.

**示例**


    var doc = new Tank;
    doc.model('User').findById(id, callback);

## Model.discriminator()

**参数**

* schema «Schema» discriminator model schema

Adds a discriminator type.

**示例**


    function BaseSchema() {
      Schema.apply(this, arguments);

      this.add({
        name: String,
        createdAt: Date
      });
    }
    util.inherits(BaseSchema, Schema);

    var PersonSchema = new BaseSchema();
    var BossSchema = new BaseSchema({ department: String });

    var Person = mongoose.model('Person', PersonSchema);
    var Boss = Person.discriminator('Boss', BossSchema);

## Model.init()

**参数**

Performs any async initialization of this model against MongoDB. Currently, this function is only responsible for building [indexes, unless [`autoIndex` is turned off.

This function is called automatically, so you don't need to call it. This function is also idempotent, so you may call it to get back a promise that will resolve when your indexes are finished building as an alternative to `MyModel.on('index')`

**示例**


    var eventSchema = new Schema({ thing: { type: 'string', unique: true }})


    var Event = mongoose.model('Event', eventSchema);

    Event.init().then(function(Event) {


      console.log('Indexes are done building!');
    });

## Model.ensureIndexes()

**参数**

* [cb] «Function» optional callback

**返回**

Sends `createIndex` commands to mongo for each index declared in the schema. The `createIndex` commands are sent in series.

**示例**


    Event.ensureIndexes(function (err) {
      if (err) return handleError(err);
    });

After completion, an `index` event is emitted on this `Model` passing an error if one occurred.

**示例**


    var eventSchema = new Schema({ thing: { type: 'string', unique: true }})
    var Event = mongoose.model('Event', eventSchema);

    Event.on('index', function (err) {
      if (err) console.error(err);
    })

_NOTE: It is not recommended that you run this in production. Index creation may impact database performance depending on your load. Use with caution._

## Model.createIndexes()

**参数**

* [cb] «Function» optional callback

**返回**

Similar to `ensureIndexes()`, except for it uses the [`createIndex` function. The `ensureIndex()` function checks to see if an index with that name already exists, and, if not, does not attempt to create the index. `createIndex()` bypasses this check.

## Model.prototype.schema

## Model.prototype.base

Base Mongoose instance the model uses.

## Model.prototype.discriminators

Registered discriminators for this model.

## Model.translateAliases()

**参数**

* raw «Object» fields/conditions that may contain aliased keys

**返回**

* «Object» the translated 'pure' fields/conditions

Translate any aliases fields/conditions so the final query or document object is pure

**示例**


    Character
      .find(Character.translateAliases({
        '名': 'Eddard Stark'
      })
      .exec(function(err, characters) {})

**注释**

Only translate arguments of object type anything else is returned raw

## Model.remove()

**参数**

**返回**

Removes all documents that match `conditions` from the collection. To remove just the first document that matches `conditions`, set the `single` option to true.

**示例**


    Character.remove({ name: 'Eddard Stark' }, function (err) {});

**注释**

This method sends a remove command directly to MongoDB, no Mongoose documents are involved. Because no Mongoose documents are involved, _no middleware (hooks) are executed_.

## Model.deleteOne()

**参数**

**返回**

Deletes the first document that matches `conditions` from the collection. Behaves like `remove()`, but deletes at most one document regardless of the `single` option.

**示例**


    Character.deleteOne({ name: 'Eddard Stark' }, function (err) {});

**注释**

Like `Model.remove()`, this function does **not** trigger `pre('remove')` or `post('remove')` hooks.
* * *

## Model.deleteMany()

**参数**

**返回**

Deletes all of the documents that match `conditions` from the collection. Behaves like `remove()`, but deletes all documents that match `conditions` regardless of the `single` option.

**示例**


    Character.deleteMany({ name: /Stark/, age: { $gte: 18 } }, function (err) {});

**注释**

Like `Model.remove()`, this function does **not** trigger `pre('remove')` or `post('remove')` hooks.
* * *

## Model.find()

**参数**

**返回**

Finds documents

The `conditions` are cast to their respective SchemaTypes before the command is sent.

**示例**


    MyModel.find({ name: 'john', age: { $gte: 18 }});


    MyModel.find({ name: 'john', age: { $gte: 18 }}, function (err, docs) {});


    MyModel.find({ name: /john/i }, 'name friends', function (err, docs) { })


    MyModel.find({ name: /john/i }, null, { skip: 10 })


    MyModel.find({ name: /john/i }, null, { skip: 10 }, function (err, docs) {});


    var query = MyModel.find({ name: /john/i }, null, { skip: 10 })
    query.exec(function (err, docs) {});


    var query = MyModel.find({ name: /john/i }, null, { skip: 10 });
    var promise = query.exec();
    promise.addBack(function (err, docs) {});

## Model.findById()

**参数**

**返回**

Finds a single document by its _id field. `findById(id)` is almost* equivalent to `findOne({ _id: id })`. If you want to query by a document's `_id`, use `findById()` instead of `findOne()`.

The `id` is cast based on the Schema before sending the command.

This function triggers the following middleware.

* Except for how it treats `undefined`. If you use `findOne()`, you'll see that `findOne(undefined)` and `findOne({ _id: undefined })` are equivalent to `findOne({})` and return arbitrary documents. However, mongoose translates `findById(undefined)` into `findOne({ _id: null })`.

**示例**


    Adventure.findById(id, function (err, adventure) {});


    Adventure.findById(id).exec(callback);


    Adventure.findById(id, 'name length', function (err, adventure) {});


    Adventure.findById(id, 'name length').exec(callback);


    Adventure.findById(id, '-length').exec(function (err, adventure) {});


    Adventure.findById(id, 'name', { lean: true }, function (err, doc) {});


    Adventure.findById(id, 'name').lean().exec(function (err, doc) {});

## Model.findOne()

**参数**

**返回**

Finds one document.

The `conditions` are cast to their respective SchemaTypes before the command is sent.

_Note:_ `conditions` is optional, and if `conditions` is null or undefined, mongoose will send an empty `findOne` command to MongoDB, which will return an arbitrary document. If you're querying by `_id`, use `findById()` instead.

**示例**


    Adventure.findOne({ type: 'iphone' }, function (err, adventure) {});


    Adventure.findOne({ type: 'iphone' }).exec(function (err, adventure) {});


    Adventure.findOne({ type: 'iphone' }, 'name', function (err, adventure) {});


    Adventure.findOne({ type: 'iphone' }, 'name').exec(function (err, adventure) {});


    Adventure.findOne({ type: 'iphone' }, 'name', { lean: true }, callback);


    Adventure.findOne({ type: 'iphone' }, 'name', { lean: true }).exec(callback);


    Adventure.findOne({ type: 'iphone' }).select('name').lean().exec(callback);

## Model.count()

**参数**

**返回**

Counts number of matching documents in a database collection.

**示例**


    Adventure.count({ type: 'jungle' }, function (err, count) {
      if (err) ..
      console.log('there are %d jungle adventures', count);
    });

## Model.distinct()

**参数**

**返回**

Creates a Query for a `distinct` operation.

Passing a `callback` immediately executes the query.

**示例**


    Link.distinct('url', { clicks: {$gt: 100}}, function (err, result) {
      if (err) return handleError(err);

      assert(Array.isArray(result));
      console.log('unique urls with more than 100 clicks', result);
    })

    var query = Link.distinct('url');
    query.exec(callback);

## Model.where()

**参数**

* [val] «Object» optional value

**返回**

Creates a Query, applies the passed conditions, and returns the Query.

For example, instead of writing:


    User.find({age: {$gte: 21, $lte: 65}}, callback);

we can instead write:


    User.where('age').gte(21).lte(65).exec(callback);

Since the Query class also supports `where` you can continue chaining


    User
    .where('age').gte(21).lte(65)
    .where('name', /^b/i)
    ... etc

## Model.prototype.$where()

**参数**

* argument «String,Function» is a javascript string or anonymous function

**返回**

Creates a `Query` and specifies a `$where` condition.

Sometimes you need to query for things in mongodb using a JavaScript expression. You can do so via `find({ $where: javascript })`, or you can use the mongoose shortcut method $where via a Query chain or from your mongoose Model.


    Blog.$where('this.username.indexOf("val") !== -1').exec(function (err, docs) {});

## Model.findOneAndUpdate()

**参数**

**返回**

Issues a mongodb findAndModify update command.

Finds a matching document, updates it according to the `update` arg, passing any `options`, and returns the found document (if any) to the callback. The query executes immediately if `callback` is passed else a Query object is returned.

**选项**

* `new`: bool - if true, return the modified document rather than the original. defaults to false (changed in 4.0)
* `upsert`: bool - creates the object if it doesn't exist. defaults to false.
* `fields`: {Object|String} - Field selection. Equivalent to `.select(fields).findOneAndUpdate()`
* `maxTimeMS`: puts a time limit on the query - requires mongodb >= 2.6.0
* `sort`: if multiple docs are found by the conditions, sets the sort order to choose which doc to update
* `runValidators`: if true, runs [update validators on this command. Update validators validate the update operation against the model's schema.
* `setDefaultsOnInsert`: if this and `upsert` are true, mongoose will apply the [defaults specified in the model's schema if a new document is created. This option only works on MongoDB >= 2.4 because it relies on [MongoDB's `$setOnInsert` operator.
* `rawResult`: if true, returns the [raw result from the MongoDB driver
* `strict`: overwrites the schema's [strict mode option for this update

**示例**


    A.findOneAndUpdate(conditions, update, options, callback)
    A.findOneAndUpdate(conditions, update, options)
    A.findOneAndUpdate(conditions, update, callback)
    A.findOneAndUpdate(conditions, update)
    A.findOneAndUpdate()

**注释**

All top level update keys which are not `atomic` operation names are treated as set operations:

**示例**


    var query = { name: 'borne' };
    Model.findOneAndUpdate(query, { name: 'jason bourne' }, options, callback)


    Model.findOneAndUpdate(query, { $set: { name: 'jason bourne' }}, options, callback)

This helps prevent accidentally overwriting your document with `{ name: 'jason bourne' }`.

**注释**

Values are cast to their appropriate types when using the findAndModify helpers. However, the below are not executed by default.

* defaults. Use the `setDefaultsOnInsert` option to override.

`findAndModify` helpers support limited validation. You can enable these by setting the `runValidators` options, respectively.

If you need full-fledged validation, use the traditional approach of first retrieving the document.


    Model.findById(id, function (err, doc) {
      if (err) ..
      doc.name = 'jason bourne';
      doc.save(callback);
    });

## Model.findByIdAndUpdate()

**参数**

**返回**

Issues a mongodb findAndModify update command by a document's _id field. `findByIdAndUpdate(id, ...)` is equivalent to `findOneAndUpdate({ _id: id }, ...)`.

Finds a matching document, updates it according to the `update` arg, passing any `options`, and returns the found document (if any) to the callback. The query executes immediately if `callback` is passed else a Query object is returned.

This function triggers the following middleware.

**选项**

* `new`: bool - true to return the modified document rather than the original. defaults to false
* `upsert`: bool - creates the object if it doesn't exist. defaults to false.
* `runValidators`: if true, runs [update validators on this command. Update validators validate the update operation against the model's schema.
* `setDefaultsOnInsert`: if this and `upsert` are true, mongoose will apply the [defaults specified in the model's schema if a new document is created. This option only works on MongoDB >= 2.4 because it relies on [MongoDB's `$setOnInsert` operator.
* `sort`: if multiple docs are found by the conditions, sets the sort order to choose which doc to update
* `select`: sets the document fields to return
* `rawResult`: if true, returns the [raw result from the MongoDB driver
* `strict`: overwrites the schema's [strict mode option for this update

**示例**


    A.findByIdAndUpdate(id, update, options, callback)
    A.findByIdAndUpdate(id, update, options)
    A.findByIdAndUpdate(id, update, callback)
    A.findByIdAndUpdate(id, update)
    A.findByIdAndUpdate()

**注释**

All top level update keys which are not `atomic` operation names are treated as set operations:

**示例**


    Model.findByIdAndUpdate(id, { name: 'jason bourne' }, options, callback)


    Model.findByIdAndUpdate(id, { $set: { name: 'jason bourne' }}, options, callback)

This helps prevent accidentally overwriting your document with `{ name: 'jason bourne' }`.

**注释**

Values are cast to their appropriate types when using the findAndModify helpers. However, the below are not executed by default.

* defaults. Use the `setDefaultsOnInsert` option to override.

`findAndModify` helpers support limited validation. You can enable these by setting the `runValidators` options, respectively.

If you need full-fledged validation, use the traditional approach of first retrieving the document.


    Model.findById(id, function (err, doc) {
      if (err) ..
      doc.name = 'jason bourne';
      doc.save(callback);
    });

## Model.findOneAndRemove()

**参数**

**返回**

Issue a mongodb findAndModify remove command.

Finds a matching document, removes it, passing the found document (if any) to the callback.

Executes immediately if `callback` is passed else a Query object is returned.

This function triggers the following middleware.

**选项**

* `sort`: if multiple docs are found by the conditions, sets the sort order to choose which doc to update
* `maxTimeMS`: puts a time limit on the query - requires mongodb >= 2.6.0
* `select`: sets the document fields to return
* `rawResult`: if true, returns the [raw result from the MongoDB driver
* `strict`: overwrites the schema's [strict mode option for this update

**示例**


    A.findOneAndRemove(conditions, options, callback)
    A.findOneAndRemove(conditions, options)
    A.findOneAndRemove(conditions, callback)
    A.findOneAndRemove(conditions)
    A.findOneAndRemove()

Values are cast to their appropriate types when using the findAndModify helpers. However, the below are not executed by default.

* defaults. Use the `setDefaultsOnInsert` option to override.

`findAndModify` helpers support limited validation. You can enable these by setting the `runValidators` options, respectively.

If you need full-fledged validation, use the traditional approach of first retrieving the document.


    Model.findById(id, function (err, doc) {
      if (err) ..
      doc.name = 'jason bourne';
      doc.save(callback);
    });

## Model.findByIdAndRemove()

**参数**

**返回**

Issue a mongodb findAndModify remove command by a document's _id field. `findByIdAndRemove(id, ...)` is equivalent to `findOneAndRemove({ _id: id }, ...)`.

Finds a matching document, removes it, passing the found document (if any) to the callback.

Executes immediately if `callback` is passed, else a `Query` object is returned.

This function triggers the following middleware.

**选项**

* `sort`: if multiple docs are found by the conditions, sets the sort order to choose which doc to update
* `select`: sets the document fields to return
* `rawResult`: if true, returns the [raw result from the MongoDB driver
* `strict`: overwrites the schema's [strict mode option for this update

**示例**


    A.findByIdAndRemove(id, options, callback)
    A.findByIdAndRemove(id, options)
    A.findByIdAndRemove(id, callback)
    A.findByIdAndRemove(id)
    A.findByIdAndRemove()

## Model.create()

**参数**

* [callback] «Function» callback

**返回**

Shortcut for saving one or more documents to the database. `MyModel.create(docs)` does `new MyModel(doc).save()` for every doc in docs.

This function triggers the following middleware.

**示例**


    Candy.create({ type: 'jelly bean' }, { type: 'snickers' }, function (err, jellybean, snickers) {
      if (err)
    });


    var array = [{ type: 'jelly bean' }, { type: 'snickers' }];
    Candy.create(array, function (err, candies) {
      if (err)

      var jellybean = candies[0];
      var snickers = candies[1];

    });


    var promise = Candy.create({ type: 'jawbreaker' });
    promise.then(function (jawbreaker) {

    })

## Model.watch()

**参数**

**返回**

* «ChangeStream» mongoose-specific change stream wrapper

_Requires a replica set running MongoDB >= 3.6.0._ Watches the underlying collection for changes using [MongoDB change streams.

This function does **not** trigger any middleware. In particular, it does **not** trigger aggregate middleware.

**示例**


    const doc = await Person.create({ name: 'Ned Stark' });
    Person.watch().on('change', change => console.log(change));





    await doc.remove();

## Model.insertMany()

**参数**

* [callback] «Function» callback

**返回**

Shortcut for validating an array of documents and inserting them into MongoDB if they're all valid. This function is faster than `.create()` because it only sends one operation to the server, rather than one for each document.

Mongoose always validates each document **before** sending `insertMany` to MongoDB. So if one document has a validation error, no documents will be saved, unless you set [the `ordered` option to false.

This function does **not** trigger save middleware.

This function triggers the following middleware.

**示例**


    var arr = [{ name: 'Star Wars' }, { name: 'The Empire Strikes Back' }];
    Movies.insertMany(arr, function(error, docs) {});

## Model.bulkWrite()

**参数**

* [callback] «Function» callback `function(error, bulkWriteOpResult) {}`

**返回**

* «Promise» resolves to a `BulkWriteOpResult` if the operation succeeds

Sends multiple `insertOne`, `updateOne`, `updateMany`, `replaceOne`, `deleteOne`, and/or `deleteMany` operations to the MongoDB server in one command. This is faster than sending multiple independent operations (like) if you use `create()`) because with `bulkWrite()` there is only one round trip to MongoDB.

Mongoose will perform casting on all operations you provide.

This function does **not** trigger any middleware, not `save()` nor `update()`. If you need to trigger `save()` middleware for every document use [`create()` instead.

**示例**


    Character.bulkWrite([
      {
        insertOne: {
          document: {
            name: 'Eddard Stark',
            title: 'Warden of the North'
          }
        }
      },
      {
        updateOne: {
          filter: { name: 'Eddard Stark' },



          update: { title: 'Hand of the King' }
        }
      },
      {
        deleteOne: {
          {
            filter: { name: 'Eddard Stark' }
          }
        }
      }
    ]).then(handleResult);

## Model.hydrate()

**参数**

**返回**

* «Model» document instance

Shortcut for creating a new Document from existing raw data, pre-saved in the DB. The document returned has no paths marked as modified initially.

**示例**


    var mongooseCandy = Candy.hydrate({ _id: '54108337212ffb6d459f854c', type: 'jelly bean' });

## Model.update()

**参数**

**返回**

Updates one document in the database without returning it.

This function triggers the following middleware.

**示例**


    MyModel.update({ age: { $gt: 18 } }, { oldEnough: true }, fn);
    MyModel.update({ name: 'Tobi' }, { ferret: true }, { multi: true }, function (err, raw) {
      if (err) return handleError(err);
      console.log('The raw response from Mongo was ', raw);
    });

**有效的选项**

* `safe` (boolean) safe mode (defaults to value set in schema (true))
* `upsert` (boolean) whether to create the doc if it doesn't match (false)
* `multi` (boolean) whether multiple documents should be updated (false)
* `runValidators`: if true, runs [update validators on this command. Update validators validate the update operation against the model's schema.
* `setDefaultsOnInsert`: if this and `upsert` are true, mongoose will apply the [defaults specified in the model's schema if a new document is created. This option only works on MongoDB >= 2.4 because it relies on [MongoDB's `$setOnInsert` operator.
* `strict` (boolean) overrides the `strict` option for this update
* `overwrite` (boolean) disables update-only mode, allowing you to overwrite the doc (false)

All `update` values are cast to their appropriate SchemaTypes before being sent.

The `callback` function receives `(err, rawResponse)`.

* `err` is the error if any occurred
* `rawResponse` is the full response from Mongo

**注释**

All top level keys which are not `atomic` operation names are treated as set operations:

**示例**


    var query = { name: 'borne' };
    Model.update(query, { name: 'jason bourne' }, options, callback)


    Model.update(query, { $set: { name: 'jason bourne' }}, options, callback)


This helps prevent accidentally overwriting all documents in your collection with `{ name: 'jason bourne' }`.

**注释**

Be careful to not use an existing model instance for the update clause (this won't work and can cause weird behavior like infinite loops). Also, ensure that the update clause does not have an _id property, which causes Mongo to return a "Mod on _id not allowed" error.

**注释**

To update documents without waiting for a response from MongoDB, do not pass a `callback`, then call `exec` on the returned [Query:


    Comment.update({ _id: id }, { $set: { text: 'changed' }}).exec();

**注释**

Although values are casted to their appropriate types when using update, the following are _not_ applied:

* defaults
* setters
* validators
* middleware

If you need those features, use the traditional approach of first retrieving the document.


    Model.findOne({ name: 'borne' }, function (err, doc) {
      if (err) ..
      doc.name = 'jason bourne';
      doc.save(callback);
    })

## Model.updateMany()

**参数**

**返回**

Same as `update()`, except MongoDB will update _all_ documents that match `criteria` (as opposed to just the first one) regardless of the value of the `multi` option.

**Note** updateMany will _not_ fire update middleware. Use `pre('updateMany')` and `post('updateMany')` instead.

This function triggers the following middleware.

## Model.updateOne()

**参数**

**返回**

Same as `update()`, except MongoDB will update _only_ the first document that matches `criteria` regardless of the value of the `multi` option.

This function triggers the following middleware.

## Model.replaceOne()

**参数**

**返回**

Same as `update()`, except MongoDB replace the existing document with the given document (no atomic operators like `$set`).

This function triggers the following middleware.

## Model.mapReduce()

**参数**

* [callback] «Function» optional callback

**返回**

Executes a mapReduce command.

`o` is an object specifying all mapReduce options as well as the map and reduce functions. All options are delegated to the driver implementation. See [node-mongodb-native mapReduce() documentation for more detail about options.

This function does not trigger any middleware.

**示例**


    var o = {};
    o.map = function () { emit(this.name, 1) }
    o.reduce = function (k, vals) { return vals.length }
    User.mapReduce(o, function (err, results) {
      console.log(results)
    })

**其他选项**

* `query` {Object} query filter object.
* `sort` {Object} sort input objects using this key
* `limit` {Number} max number of documents
* `keeptemp` {Boolean, default:false} keep temporary data
* `finalize` {Function} finalize function
* `scope` {Object} scope variables exposed to map/reduce/finalize during execution
* `jsMode` {Boolean, default:false} it is possible to make the execution stay in JS. Provided in MongoDB > 2.0.X
* `verbose` {Boolean, default:false} provide statistics on job execution time.
* `readPreference` {String}
* `out*` {Object, default: {inline:1}} sets the output target for the map reduce job.

**输出选项**

* `{inline:1}` the results are returned in an array
* `{replace: 'collectionName'}` add the results to collectionName: the results replace the collection
* `{reduce: 'collectionName'}` add the results to collectionName: if dups are detected, uses the reducer / finalize functions
* `{merge: 'collectionName'}` add the results to collectionName: if dups exist the new docs overwrite the old

If `options.out` is set to `replace`, `merge`, or `reduce`, a Model instance is returned that can be used for further querying. Queries run against this model are all executed with the `lean` option; meaning only the js object is returned and no Mongoose magic is applied (getters, setters, etc).

**示例**


    var o = {};
    o.map = function () { emit(this.name, 1) }
    o.reduce = function (k, vals) { return vals.length }
    o.out = { replace: 'createdCollectionNameForResults' }
    o.verbose = true;

    User.mapReduce(o, function (err, model, stats) {
      console.log('map reduce took %d ms', stats.processtime)
      model.find().where('value').gt(10).exec(function (err, docs) {
        console.log(docs);
      });
    })



    o.resolveToObject = true;
    var promise = User.mapReduce(o);
    promise.then(function (res) {
      var model = res.model;
      var stats = res.stats;
      console.log('map reduce took %d ms', stats.processtime)
      return model.find().where('value').gt(10).exec();
    }).then(function (docs) {
       console.log(docs);
    }).then(null, handleError).end()

## Model.aggregate()

**参数**

**返回**

Performs [aggregations on the models collection.

If a `callback` is passed, the `aggregate` is executed and a `Promise` is returned. If a callback is not passed, the `aggregate` itself is returned.

This function does not trigger any middleware.

**示例**


    Users.aggregate(
      { $group: { _id: null, maxBalance: { $max: '$balance' }}},
      { $project: { _id: 0, maxBalance: 1 }},
      function (err, res) {
        if (err) return handleError(err);
        console.log(res);
      });


    Users.aggregate()
      .group({ _id: null, maxBalance: { $max: '$balance' } })
      .select('-id maxBalance')
      .exec(function (err, res) {
        if (err) return handleError(err);
        console.log(res);
    });

**注释**

* Arguments are not cast to the model's schema because `$project` operators allow redefining the "shape" of the documents at any stage of the pipeline, which may leave documents in an incompatible format.
* The documents returned are plain javascript objects, not mongoose documents (since any shape of document can be returned).
* Requires MongoDB >= 2.1
* * *

## Model.geoSearch()

**参数**

* [callback] «Function» optional callback

**返回**

Implements `$geoSearch` functionality for Mongoose

This function does not trigger any middleware

**示例**


    var options = { near: [10, 10], maxDistance: 5 };
    Locations.geoSearch({ type : "house" }, options, function(err, res) {
      console.log(res);
    });

**选项**

* `near` {Array} x,y point to search for
* `maxDistance` {Number} the maximum distance from the point near that a result can be
* `limit` {Number} The maximum number of results to return
* `lean` {Boolean} return the raw object instead of the Mongoose Model
* * *

## Model.populate()

**参数**

* [callback(err,doc)] «Function» Optional callback, executed upon completion. Receives `err` and the `doc(s)`.

**返回**

Populates document references.

**可用的选项**

* path: space delimited path(s) to populate
* select: optional fields to select
* match: optional query conditions to match
* model: optional name of the model to use for population
* options: optional query options like sort, limit, etc

**示例**


    User.findById(id, function (err, user) {
      var opts = [
          { path: 'company', match: { x: 1 }, select: 'name' }
        , { path: 'notes', options: { limit: 10 }, model: 'override' }
      ]

      User.populate(user, opts, function (err, user) {
        console.log(user);
      });
    });


    User.find(match, function (err, users) {
      var opts = [{ path: 'company', match: { x: 1 }, select: 'name' }]

      var promise = User.populate(users, opts);
      promise.then(console.log).end();
    })










    var user = { name: 'Indiana Jones', weapon: 389 }
    Weapon.populate(user, { path: 'weapon', model: 'Weapon' }, function (err, user) {
      console.log(user.weapon.name)
    })


    var users = [{ name: 'Indiana Jones', weapon: 389 }]
    users.push({ name: 'Batman', weapon: 8921 })
    Weapon.populate(users, { path: 'weapon' }, function (err, users) {
      users.forEach(function (user) {
        console.log('%s uses a %s', users.name, user.weapon.name)


      });
    });



* * *
* * *