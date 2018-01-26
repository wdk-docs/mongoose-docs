# 查询

## Query()

**参数**

* [collection] «Object» Mongoose collection

Query constructor used for building queries. You do not need to instantiate a `Query` directly. Instead use Model functions like [`Model.find()`.

**示例**

    const query = MyModel.find();
    query.setOptions({ lean : true });
    query.collection(model.collection);
    query.where('age').gte(21).exec(callback);

    const query = new mongoose.Query();

## Query.prototype.use$geoWithin

Flag to opt out of using `$geoWithin`.

    mongoose.Query.use$geoWithin = false;

MongoDB 2.4 deprecated the use of `$within`, replacing it with `$geoWithin`. Mongoose uses `$geoWithin` by default (which is 100% backward compatible with $within). If you are running an older version of MongoDB, set this flag to `false` so your `within()` queries continue to work.

## Query.prototype.toConstructor()

**返回**

* «Query» subclass-of-Query

Converts this query to a customized, reusable query constructor with all arguments and options retained.

**示例**

    var query = Movie.find({ tags: 'adventure' }).read('primaryPreferred');

    var Adventure = query.toConstructor();

    Adventure().exec(callback)

    Adventure().where({ name: /^Life/ }).exec(callback);

    Adventure.prototype.startsWith = function (prefix) {
      this.where({ name: new RegExp('^' + prefix) })
      return this;
    }
    Object.defineProperty(Adventure.prototype, 'highlyRated', {
      get: function () {
        this.where({ rating: { $gt: 4.5 }});
        return this;
      }
    })
    Adventure().highlyRated.startsWith('Life').exec(callback)

New in 3.7.3

## Query.prototype.$where()

**参数**

* js «String,Function» javascript string or function

**返回**

Specifies a javascript function or expression to pass to MongoDBs query system.

**示例**

    query.$where('this.comments.length === 10 || this.name.length === 5')

    query.$where(function () {
      return this.comments.length === 10 || this.name.length === 5;
    })

**注释**

Only use `$where` when you have a condition that cannot be met using other MongoDB operators like `$lt`. **Be sure to read about all of [its caveats before using.**

## Query.prototype.where()

**参数**

**返回**

Specifies a `path` for use with chaining.

**示例**

    User.find({age: {$gte: 21, $lte: 65}}, callback);

    User.where('age').gte(21).lte(65);

    User.find().where({ name: 'vonderful' })

    User
    .where('age').gte(21).lte(65)
    .where('name', /^vonderful/i)
    .where('friends').slice(10)
    .exec(callback)

## Query.prototype.equals()

**参数**

**返回**

Specifies the complementary comparison value for paths specified with `where()`

**示例**

    User.where('age').equals(49);

    User.where('age', 49);

## Query.prototype.or()

**参数**

* array «Array» array of conditions

**返回**

Specifies arguments for an `$or` condition.

**示例**

    query.or([{ color: 'red' }, { status: 'emergency' }])

## Query.prototype.nor()

**参数**

* array «Array» array of conditions

**返回**

Specifies arguments for a `$nor` condition.

**示例**

    query.nor([{ color: 'green' }, { status: 'ok' }])

## Query.prototype.and()

**参数**

* array «Array» array of conditions

**返回**

Specifies arguments for a `$and` condition.

**示例**

    query.and([{ color: 'green' }, { status: 'ok' }])

## Query.prototype.gt()

**参数**

Specifies a $gt query condition.

When called with one argument, the most recent path passed to `where()` is used.

**示例**

    Thing.find().where('age').gt(21)

    Thing.find().gt('age', 21)

## Query.prototype.gte()

**参数**

Specifies a $gte query condition.

When called with one argument, the most recent path passed to `where()` is used.

## Query.prototype.lt()

**参数**

Specifies a $lt query condition.

When called with one argument, the most recent path passed to `where()` is used.

## Query.prototype.lte()

**参数**

Specifies a $lte query condition.

When called with one argument, the most recent path passed to `where()` is used.

## Query.prototype.ne()

**参数**

Specifies a $ne query condition.

When called with one argument, the most recent path passed to `where()` is used.

## Query.prototype.in()

**参数**

Specifies an $in query condition.

When called with one argument, the most recent path passed to `where()` is used.

## Query.prototype.nin()

**参数**

Specifies an $nin query condition.

When called with one argument, the most recent path passed to `where()` is used.

## Query.prototype.all()

**参数**

Specifies an $all query condition.

When called with one argument, the most recent path passed to `where()` is used.

## Query.prototype.size()

**参数**

Specifies a $size query condition.

When called with one argument, the most recent path passed to `where()` is used.

**示例**

    MyModel.where('tags').size(0).exec(function (err, docs) {
      if (err) return handleError(err);

      assert(Array.isArray(docs));
      console.log('documents with 0 tags', docs);
    })

## Query.prototype.regex()

**参数**

Specifies a $regex query condition.

When called with one argument, the most recent path passed to `where()` is used.

## Query.prototype.maxDistance()

**参数**

Specifies a $maxDistance query condition.

When called with one argument, the most recent path passed to `where()` is used.

## Query.prototype.mod()

**参数**

* val «Array» must be of length 2, first element is `divisor`, 2nd element is `remainder`.

**返回**

Specifies a `$mod` condition, filters documents for documents whose `path` property is a number that is equal to `remainder` modulo `divisor`.

**示例**

    Product.find().mod('inventory', [2, 1]);
    Product.find().where('inventory').mod([2, 1]);

    Product.find().where('inventory').mod(2, 1);

## Query.prototype.exists()

**参数**

**返回**

Specifies an `$exists` condition

**示例**

    Thing.where('name').exists()
    Thing.where('name').exists(true)
    Thing.find().exists('name')

    Thing.where('name').exists(false);
    Thing.find().exists('name', false);

## Query.prototype.elemMatch()

**参数**

* criteria «Object,Function»

**返回**

Specifies an `$elemMatch` condition

**示例**

    query.elemMatch('comment', { author: 'autobot', votes: {$gte: 5}})

    query.where('comment').elemMatch({ author: 'autobot', votes: {$gte: 5}})

    query.elemMatch('comment', function (elem) {
      elem.where('author').equals('autobot');
      elem.where('votes').gte(5);
    })

    query.where('comment').elemMatch(function (elem) {
      elem.where({ author: 'autobot' });
      elem.where('votes').gte(5);
    })

## Query.prototype.within()

**返回**

Defines a `$within` or `$geoWithin` argument for geo-spatial queries.

**示例**

    query.where(path).within().box()
    query.where(path).within().circle()
    query.where(path).within().geometry()

    query.where('loc').within({ center: [50,50], radius: 10, unique: true, spherical: true });
    query.where('loc').within({ box: [[40.73, -73.9], [40.7, -73.988]] });
    query.where('loc').within({ polygon: [[],[],[],[]] });

    query.where('loc').within([], [], [])
    query.where('loc').within([], [])
    query.where('loc').within({ type: 'LineString', coordinates: [...] });

**MUST** be used after `where()`.

**注释**

As of Mongoose 3.7, `$geoWithin` is always used for queries. To change this behavior, see [Query.use$geoWithin.

**注释**

In Mongoose 3.7, `within` changed from a getter to a function. If you need the old syntax, use [this.

## Query.prototype.slice()

**参数**

* val «Number» number/range of elements to slice

**返回**

Specifies a $slice projection for an array.

**示例**

    query.slice('comments', 5)
    query.slice('comments', -5)
    query.slice('comments', [10, 5])
    query.where('comments').slice(5)
    query.where('comments').slice([-10, 5])

## Query.prototype.limit()

**参数**

Specifies the maximum number of documents the query will return.

**示例**

    query.limit(20)

**注释**

Cannot be used with `distinct()`

## Query.prototype.skip()

**参数**

Specifies the number of documents to skip.

**示例**

    query.skip(100).limit(20)

**注释**

Cannot be used with `distinct()`

## Query.prototype.maxScan()

**参数**

Specifies the maxScan option.

**示例**

    query.maxScan(100)

**注释**

Cannot be used with `distinct()`

## Query.prototype.batchSize()

**参数**

Specifies the batchSize option.

**示例**

    query.batchSize(100)

**注释**

Cannot be used with `distinct()`

**参数**

Specifies the `comment` option.

**示例**

    query.comment('login query')

**注释**

Cannot be used with `distinct()`

## Query.prototype.snapshot()

**返回**

Specifies this query as a `snapshot` query.

**示例**

    query.snapshot()
    query.snapshot(true)
    query.snapshot(false)

**注释**

Cannot be used with `distinct()`

## Query.prototype.hint()

**参数**

* val «Object» a hint object

**返回**

Sets query hints.

**示例**

    query.hint({ indexA: 1, indexB: -1})

**注释**

Cannot be used with `distinct()`

## Query.prototype.select()

**参数**

**返回**

Specifies which document fields to include or exclude (also known as the query "projection")

When using string syntax, prefixing a path with `-` will flag that path as excluded. When a path does not have the `-` prefix, it is included. Lastly, if a path is prefixed with `+`, it forces inclusion of the path, which is useful for paths excluded at the [schema level.

A projection _must_ be either inclusive or exclusive. In other words, you must either list the fields to include (which excludes all others), or list the fields to exclude (which implies all other fields are included). The [`_id` field is the only exception because MongoDB includes it by default.

**示例**

    query.select('a b');

    query.select('-c -d');

    query.select({ a: 1, b: 1 });
    query.select({ c: 0, d: 0 });

    query.select('+path')

## Query.prototype.slaveOk()

**参数**

* v «Boolean» defaults to true

**返回**

_DEPRECATED_ Sets the slaveOk option.

**Deprecated** in MongoDB 2.2 in favor of [read preferences.

**示例**

    query.slaveOk()
    query.slaveOk(true)
    query.slaveOk(false)

## Query.prototype.read()

**参数**

* [tags] «Array» optional tags for this query

**返回**

Determines the MongoDB nodes from which to read.

**喜好**

    primary - (default) Read from primary only. Operations will produce an error if primary is unavailable. Cannot be combined with tags.
    secondary            Read from secondary if available, otherwise error.
    primaryPreferred     Read from primary if available, otherwise a secondary.
    secondaryPreferred   Read from a secondary if available, otherwise read from the primary.
    nearest              All operations read from among the nearest candidates, but unlike other modes, this option will include both the primary and all secondaries in the random selection.

**别名**

    p   primary
    pp  primaryPreferred
    s   secondary
    sp  secondaryPreferred
    n   nearest

**示例**

    new Query().read('primary')
    new Query().read('p')

    new Query().read('primaryPreferred')
    new Query().read('pp')

    new Query().read('secondary')
    new Query().read('s')

    new Query().read('secondaryPreferred')
    new Query().read('sp')

    new Query().read('nearest')
    new Query().read('n')

    new Query().read('s', [{ dc:'sf', s: 1 },{ dc:'ma', s: 2 }])

Read more about how to use read preferrences [here and [here.

## Query.prototype.merge()

**参数**

**返回**

Merges another Query or conditions object into this one.

When a Query is passed, conditions, field selection and options are merged.

New in 3.7.0

## Query.prototype.setOptions()

**参数**

Sets query options. Some options only make sense for certain operations.

**选项**

The following options are only for `find()`: - [tailable \- [sort \- [limit \- [skip \- [maxscan \- [batchSize \- [comment \- [snapshot \- [readPreference \- [hint

The following options are only for `update()`, `updateOne()`, `updateMany()`, `replaceOne()`, `findOneAndUpdate()`, and `findByIdAndUpdate()`: - [upsert \- [writeConcern

The following options are only for `find()`, `findOne()`, `findById()`, `findOneAndUpdate()`, `findByIdAndUpdate()`, and `geoSearch()`: - [lean

The following options are for all operations

## Query.prototype.getQuery()

**返回**

* «Object» current query conditions

Returns the current query conditions as a JSON object.

**示例**

    var query = new Query();
    query.find({ a: 1 }).where('b').gt(2);
    query.getQuery();

## Query.prototype.getUpdate()

**返回**

* «Object» current update operations

Returns the current update operations as a JSON object.

**示例**

    var query = new Query();
    query.update({}, { $set: { a: 5 } });
    query.getUpdate();

## Query.prototype.lean()

**参数**

* bool «Boolean,Object» defaults to true

**返回**

Sets the lean option.

Documents returned from queries with the `lean` option enabled are plain javascript objects, not [MongooseDocuments. They have no `save` method, getters/setters or other Mongoose magic applied.

**示例**

    new Query().lean()
    new Query().lean(true)
    new Query().lean(false)

    Model.find().lean().exec(function (err, docs) {
      docs[0] instanceof mongoose.Document
    });

This is a [great option in high-performance read-only scenarios, especially when combined with [stream.

## Query.prototype.error()

**参数**

* err «Error,null» if set, `exec()` will fail fast before sending the query to MongoDB

Gets/sets the error flag on this query. If this flag is not null or undefined, the `exec()` promise will reject without executing.

**示例**

    Query().error();
    Query().error(null);
    Query().error(new Error('test'));
    Schema.pre('find', function() {
      if (!this.getQuery().userId) {
        this.error(new Error('Not allowed to query without setting userId'));
      }
    });

Note that query casting runs **after** hooks, so cast errors will override custom errors.

**示例**

    var TestSchema = new Schema({ num: Number });
    var TestModel = db.model('Test', TestSchema);
    TestModel.find({ num: 'not a number' }).error(new Error('woops')).exec(function(error) {

    });

## Query.prototype.mongooseOptions()

**参数**

* options «Object» if specified, overwrites the current options

Getter/setter around the current mongoose-specific options for this query (populate, lean, etc.)

## Query.prototype.find()

**参数**

**返回**

Finds documents.

When no `callback` is passed, the query is not executed. When the query is executed, the result will be an array of documents.

**示例**

    query.find({ name: 'Los Pollos Hermanos' }).find(callback)

## Query.prototype.merge()

**参数**

**返回**

Merges another Query or conditions object into this one.

When a Query is passed, conditions, field selection and options are merged.

## Query.prototype.collation()

**参数**

**返回**

Adds a collation to this op (MongoDB 3.4 and up)

## Query.prototype.findOne()

**参数**

* [callback] «Function» optional params are (error, document)

**返回**

Declares the query a findOne operation. When executed, the first found document is passed to the callback.

Passing a `callback` executes the query. The result of the query is a single document.

* _Note:_ `conditions` is optional, and if `conditions` is null or undefined, mongoose will send an empty `findOne` command to MongoDB, which will return an arbitrary document. If you're querying by `_id`, use `Model.findById()` instead.

This function triggers the following middleware.

**示例**

    var query  = Kitten.where({ color: 'white' });
    query.findOne(function (err, kitten) {
      if (err) return handleError(err);
      if (kitten) {

      }
    });

## Query.prototype.count()

**参数**

* [callback] «Function» optional params are (error, count)

**返回**

Specifying this query as a `count` query.

Passing a `callback` executes the query.

This function triggers the following middleware.

**示例**

    var countQuery = model.where({ 'color': 'black' }).count();

    query.count({ color: 'black' }).count(callback)

    query.count({ color: 'black' }, callback)

    query.where('color', 'black').count(function (err, count) {
      if (err) return handleError(err);
      console.log('there are %d kittens', count);
    })

## Query.prototype.distinct()

**参数**

* [callback] «Function» optional params are (error, arr)

**返回**

Declares or executes a distict() operation.

Passing a `callback` executes the query.

This function does not trigger any middleware.

**示例**

    distinct(field, conditions, callback)
    distinct(field, conditions)
    distinct(field, callback)
    distinct(field)
    distinct(callback)
    distinct()

## Query.prototype.sort()

**参数**

**返回**

Sets the sort order

If an object is passed, values allowed are `asc`, `desc`, `ascending`, `descending`, `1`, and `-1`.

If a string is passed, it must be a space delimited list of path names. The sort order of each path is ascending unless the path name is prefixed with `-` which will be treated as descending.

**示例**

    query.sort({ field: 'asc', test: -1 });

    query.sort('field -test');

**注释**

Cannot be used with `distinct()`

## Query.prototype.remove()

**参数**

* [callback] «Function» optional params are (error, writeOpResult)

**返回**

Declare and/or execute this query as a remove() operation.

This function does not trigger any middleware

**示例**

    Model.remove({ artist: 'Anne Murray' }, callback)

**注释**

The operation is only executed when a callback is passed. To force execution without a callback, you must first call `remove()` and then execute it by using the `exec()` method.

    var query = Model.find().remove({ name: 'Anne Murray' })

    query.remove({ name: 'Anne Murray' }, callback)
    query.remove({ name: 'Anne Murray' }).remove(callback)

    query.exec()

    query.remove(conds, fn);
    query.remove(conds)
    query.remove(fn)
    query.remove()

## Query.prototype.deleteOne()

**参数**

* [callback] «Function» optional params are (error, writeOpResult)

**返回**

Declare and/or execute this query as a `deleteOne()` operation. Works like remove, except it deletes at most one document regardless of the `single` option.

This function does not trigger any middleware.

**示例**

    Character.deleteOne({ name: 'Eddard Stark' }, callback)
    Character.deleteOne({ name: 'Eddard Stark' }).then(next)

## Query.prototype.deleteMany()

**参数**

* [callback] «Function» optional params are (error, writeOpResult)

**返回**

Declare and/or execute this query as a `deleteMany()` operation. Works like remove, except it deletes _every_ document that matches `criteria` in the collection, regardless of the value of `single`.

This function does not trigger any middleware

**示例**

    Character.deleteMany({ name: /Stark/, age: { $gte: 18 } }, callback)
    Character.deleteMany({ name: /Stark/, age: { $gte: 18 } }).then(next)

## Query.prototype.findOneAndUpdate()

**参数**

* [callback] «Function» optional params are (error, doc), _unless_ `rawResult` is used, in which case params are (error, writeOpResult)

**返回**

Issues a mongodb [findAndModify update command.

Finds a matching document, updates it according to the `update` arg, passing any `options`, and returns the found document (if any) to the callback. The query executes immediately if `callback` is passed.

This function triggers the following middleware.

**选项**

* `new`: bool - if true, return the modified document rather than the original. defaults to false (changed in 4.0)
* `upsert`: bool - creates the object if it doesn't exist. defaults to false.
* `fields`: {Object|String} - Field selection. Equivalent to `.select(fields).findOneAndUpdate()`
* `sort`: if multiple docs are found by the conditions, sets the sort order to choose which doc to update
* `maxTimeMS`: puts a time limit on the query - requires mongodb >= 2.6.0
* `runValidators`: if true, runs [update validators on this command. Update validators validate the update operation against the model's schema.
* `setDefaultsOnInsert`: if this and `upsert` are true, mongoose will apply the [defaults specified in the model's schema if a new document is created. This option only works on MongoDB >= 2.4 because it relies on [MongoDB's `$setOnInsert` operator.
* `rawResult`: if true, returns the [raw result from the MongoDB driver
* `context` (string) if set to 'query' and `runValidators` is on, `this` will refer to the query in custom validator functions that update validation runs. Does nothing if `runValidators` is false.

**回调签名**

    function(error, doc) {

    }

**示例**

    query.findOneAndUpdate(conditions, update, options, callback)
    query.findOneAndUpdate(conditions, update, options)
    query.findOneAndUpdate(conditions, update, callback)
    query.findOneAndUpdate(conditions, update)
    query.findOneAndUpdate(update, callback)
    query.findOneAndUpdate(update)
    query.findOneAndUpdate(callback)
    query.findOneAndUpdate()

## Query.prototype.findOneAndRemove()

**参数**

* [callback] «Function» optional params are (error, document)

**返回**

Issues a mongodb [findAndModify remove command.

Finds a matching document, removes it, passing the found document (if any) to the callback. Executes immediately if `callback` is passed.

This function triggers the following middleware.

**选项**

* `sort`: if multiple docs are found by the conditions, sets the sort order to choose which doc to update
* `maxTimeMS`: puts a time limit on the query - requires mongodb >= 2.6.0
* `rawResult`: if true, resolves to the [raw result from the MongoDB driver

**回调签名**

    function(error, doc) {

    }

**示例**s

    A.where().findOneAndRemove(conditions, options, callback)
    A.where().findOneAndRemove(conditions, options)
    A.where().findOneAndRemove(conditions, callback)
    A.where().findOneAndRemove(conditions)
    A.where().findOneAndRemove(callback)
    A.where().findOneAndRemove()

## Query.prototype.update()

**参数**

* [callback] «Function» optional, params are (error, writeOpResult)

**返回**

Declare and/or execute this query as an update() operation.

_All paths passed that are not $atomic operations will become $set ops._

This function triggers the following middleware.

**示例**

    Model.where({ _id: id }).update({ title: 'words' })

    Model.where({ _id: id }).update({ $set: { title: 'words' }})

**选项**

* `safe` (boolean) safe mode (defaults to value set in schema (true))
* `upsert` (boolean) whether to create the doc if it doesn't match (false)
* `multi` (boolean) whether multiple documents should be updated (false)
* `runValidators`: if true, runs [update validators on this command. Update validators validate the update operation against the model's schema.
* `setDefaultsOnInsert`: if this and `upsert` are true, mongoose will apply the [defaults specified in the model's schema if a new document is created. This option only works on MongoDB >= 2.4 because it relies on [MongoDB's `$setOnInsert` operator.
* `strict` (boolean) overrides the `strict` option for this update
* `overwrite` (boolean) disables update-only mode, allowing you to overwrite the doc (false)
* `context` (string) if set to 'query' and `runValidators` is on, `this` will refer to the query in custom validator functions that update validation runs. Does nothing if `runValidators` is false.

**注释**

Passing an empty object `{}` as the doc will result in a no-op unless the `overwrite` option is passed. Without the `overwrite` option set, the update operation will be ignored and the callback executed without sending the command to MongoDB so as to prevent accidently overwritting documents in the collection.

**注释**

The operation is only executed when a callback is passed. To force execution without a callback, we must first call update() and then execute it by using the `exec()` method.

    var q = Model.where({ _id: id });
    q.update({ $set: { name: 'bob' }}).update(); // not executed

    q.update({ $set: { name: 'bob' }}).exec(); // executed

    // keys that are not $atomic ops become $set.
    // this executes the same command as the previous example.
    q.update({ name: 'bob' }).exec();

    // overwriting with empty docs
    var q = Model.where({ _id: id }).setOptions({ overwrite: true })
    q.update({ }, callback); // executes

    // multi update with overwrite to empty doc
    var q = Model.where({ _id: id });
    q.setOptions({ multi: true, overwrite: true })
    q.update({ });
    q.update(callback); // executed

    // multi updates
    Model.where()
         .update({ name: /^match/ }, { $set: { arr: [] }}, { multi: true }, callback)

    // more multi updates
    Model.where()
         .setOptions({ multi: true })
         .update({ $set: { arr: [] }}, callback)

    // single update by default
    Model.where({ email: '[address@example.com' })
         .update({ $inc: { counter: 1 }}, callback)

API summary

    update(criteria, doc, options, cb)
    update(criteria, doc, options)
    update(criteria, doc, cb)
    update(criteria, doc)
    update(doc, cb)
    update(doc)
    update(cb)
    update(true)
    update()

## Query.prototype.updateMany()

**参数**

* [callback] «Function» optional params are (error, writeOpResult)

**返回**

Declare and/or execute this query as an updateMany() operation. Same as `update()`, except MongoDB will update _all_ documents that match `criteria` (as opposed to just the first one) regardless of the value of the `multi` option.

**Note** updateMany will _not_ fire update middleware. Use `pre('updateMany')` and `post('updateMany')` instead.

This function triggers the following middleware.

## Query.prototype.updateOne()

**参数**

* [callback] «Function» params are (error, writeOpResult)

**返回**

Declare and/or execute this query as an updateOne() operation. Same as `update()`, except MongoDB will update _only_ the first document that matches `criteria` regardless of the value of the `multi` option.

**Note** updateOne will _not_ fire update middleware. Use `pre('updateOne')` and `post('updateOne')` instead.

This function triggers the following middleware.

## Query.prototype.replaceOne()

**参数**

* [callback] «Function» optional params are (error, writeOpResult)

**返回**

Declare and/or execute this query as a replaceOne() operation. Same as `update()`, except MongoDB will replace the existing document and will not accept any atomic operators (`$set`, etc.)

**Note** replaceOne will _not_ fire update middleware. Use `pre('replaceOne')` and `post('replaceOne')` instead.

This function triggers the following middleware.

## Query.prototype.exec()

**参数**

* [callback] «Function» optional params depend on the function being called

**返回**

Executes the query

**示例**

    var promise = query.exec();
    var promise = query.exec('update');

    query.exec(callback);
    query.exec('find', callback);

## Query.prototype.then()

**参数**

**返回**

Executes the query returning a `Promise` which will be resolved with either the doc(s) or rejected with the error.

## Query.prototype.catch()

**参数**

**返回**

Executes the query returning a `Promise` which will be resolved with either the doc(s) or rejected with the error. Like `.then()`, but only takes a rejection handler.

## Query.prototype.populate()

**参数**

* [options] «Object» Options for the population query (sort, etc)

**返回**

Specifies paths which should be populated with other documents.

**示例**

    Kitten.findOne().populate('owner').exec(function (err, kitten) {
      console.log(kitten.owner.name)
    })

    Kitten.find().populate({
        path: 'owner'
      , select: 'name'
      , match: { color: 'black' }
      , options: { sort: { name: -1 }}
    }).exec(function (err, kittens) {
      console.log(kittens[0].owner.name)
    })

    Kitten.find().populate('owner', 'name', null, {sort: { name: -1 }}).exec(function (err, kittens) {
      console.log(kittens[0].owner.name)
    })

Paths are populated after the query executes and a response is received. A separate query is then executed for each path specified for population. After a response for each query has also been returned, the results are passed to the callback.

## Query.prototype.cast()

**参数**

**返回**

Casts this query to the schema of `model`

**注释**

If `obj` is present, it is cast instead of this query.

## Query.prototype.cursor()

**参数**

**返回**

Returns a wrapper around a [mongodb driver cursor. A QueryCursor exposes a [Streams3-compatible interface, as well as a `.next()` function.

The `.cursor()` function triggers pre find hooks, but **not** post find hooks.

**示例**

    Thing.
      find({ name: /^hello/ }).
      cursor().
      on('data', function(doc) { console.log(doc); }).
      on('end', function() { console.log('Done!'); });

    var cursor = Thing.find({ name: /^hello/ }).cursor();
    cursor.next(function(error, doc) {
      console.log(doc);
    });

    co(function*() {
      const cursor = Thing.find({ name: /^hello/ }).cursor();
      for (let doc = yield cursor.next(); doc != null; doc = yield cursor.next()) {
        console.log(doc);
      }
    });

**选项**

* `transform`: optional function which accepts a mongoose document. The return value of the function will be emitted on `data` and returned by `.next()`.
* * *

## Query.prototype.maxscan()

_DEPRECATED_ Alias of `maxScan`

## Query.prototype.tailable()

**参数**

* [opts.tailableRetryInterval] «Number» if cursor is exhausted, wait this many milliseconds before retrying

Sets the tailable option (for use with capped collections).

**示例**

    query.tailable()
    query.tailable(true)
    query.tailable(false)

**注释**

Cannot be used with `distinct()`

## Query.prototype.intersects()

**参数**

**返回**

Declares an intersects query for `geometry()`.

**示例**

    query.where('path').intersects().geometry({
        type: 'LineString'
      , coordinates: [[180.0, 11.0], [180, 9.0]]
    })

    query.where('path').intersects({
        type: 'LineString'
      , coordinates: [[180.0, 11.0], [180, 9.0]]
    })

**注释**

**MUST** be used after `where()`.

**注释**

In Mongoose 3.7, `intersects` changed from a getter to a function. If you need the old syntax, use [this.

## Query.prototype.geometry()

**参数**

* object «Object» Must contain a `type` property which is a String and a `coordinates` property which is an Array. See the examples.

**返回**

Specifies a `$geometry` condition

**示例**

    var polyA = [[[ 10, 20 ], [ 10, 40 ], [ 30, 40 ], [ 30, 20 ]]]
    query.where('loc').within().geometry({ type: 'Polygon', coordinates: polyA })

    var polyB = [[ 0, 0 ], [ 1, 1 ]]
    query.where('loc').within().geometry({ type: 'LineString', coordinates: polyB })

    var polyC = [ 0, 0 ]
    query.where('loc').within().geometry({ type: 'Point', coordinates: polyC })

    query.where('loc').intersects().geometry({ type: 'Point', coordinates: polyC })

The argument is assigned to the most recent path passed to `where()`.

**注释**

`geometry()` **must** come after either `intersects()` or `within()`.

The `object` argument must contain `type` and `coordinates` properties. - type {String} - coordinates {Array}
* * *

## Query.prototype.near()

**参数**

**返回**

Specifies a `$near` or `$nearSphere` condition

These operators return documents sorted by distance.

**示例**

    query.where('loc').near({ center: [10, 10] });
    query.where('loc').near({ center: [10, 10], maxDistance: 5 });
    query.where('loc').near({ center: [10, 10], maxDistance: 5, spherical: true });
    query.near('loc', { center: [10, 10], maxDistance: 5 });

## Query.prototype.nearSphere()

_DEPRECATED_ Specifies a `$nearSphere` condition

**示例**

    query.where('loc').nearSphere({ center: [10, 10], maxDistance: 5 });

**Deprecated.** Use `query.near()` instead with the `spherical` option set to `true`.

**示例**

    query.where('loc').near({ center: [10, 10], spherical: true });

## Query.prototype.polygon()

**参数**

* [coordinatePairs...] «Array,Object»

**返回**

Specifies a $polygon condition

**示例**

    query.where('loc').within().polygon([10,20], [13, 25], [7,15])
    query.polygon('loc', [10,20], [13, 25], [7,15])

## Query.prototype.box()

**参数**

* Upper «[Array]» Right Coords

**返回**

Specifies a $box condition

**示例**

    var lowerLeft = [40.73083, -73.99756]
    var upperRight= [40.741404,  -73.988135]

    query.where('loc').within().box(lowerLeft, upperRight)
    query.box({ ll : lowerLeft, ur : upperRight })

## Query.prototype.circle()

**参数**

**返回**

Specifies a $center or $centerSphere condition.

**示例**

    var area = { center: [50, 50], radius: 10, unique: true }
    query.where('loc').within().circle(area)

    query.circle('loc', area);

    var area = { center: [50, 50], radius: 10, unique: true, spherical: true }
    query.where('loc').within().circle(area)

    query.circle('loc', area);

New in 3.7.0

## Query.prototype.center()

## Query.prototype.centerSphere()

**参数**

**返回**

_DEPRECATED_ Specifies a $centerSphere condition

**Deprecated.** Use [circle instead.

**示例**

    var area = { center: [50, 50], radius: 10 };
    query.where('loc').within().centerSphere(area);

## Query.prototype.selected()

**返回**

Determines if field selection has been made.

## Query.prototype.selectedInclusively()

**返回**

Determines if inclusive field selection has been made.

    query.selectedInclusively()
    query.select('name')
    query.selectedInclusively()

## Query.prototype.selectedExclusively()

**返回**

Determines if exclusive field selection has been made.

    query.selectedExclusively()
    query.select('-name')
    query.selectedExclusively()
    query.selectedInclusively()

* * *
* * *