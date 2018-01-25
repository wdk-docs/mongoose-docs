### [Query()][134]

##### Parameters

* [collection] «Object» Mongoose collection

Query constructor used for building queries. You do not need to instantiate a `Query` directly. Instead use Model functions like [`Model.find()`][135].

#### Example:

    const query = MyModel.find();
    query.setOptions({ lean : true });
    query.collection(model.collection);
    query.where('age').gte(21).exec(callback);

    const query = new mongoose.Query();

* * *

### [Query.prototype.use$geoWithin][136]

Flag to opt out of using `$geoWithin`.

    mongoose.Query.use$geoWithin = false;

MongoDB 2.4 deprecated the use of `$within`, replacing it with `$geoWithin`. Mongoose uses `$geoWithin` by default (which is 100% backward compatible with $within). If you are running an older version of MongoDB, set this flag to `false` so your `within()` queries continue to work.

* * *

### [Query.prototype.toConstructor()][137]

##### Returns:

* «Query» subclass-of-Query

Converts this query to a customized, reusable query constructor with all arguments and options retained.

#### Example

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

* * *

### [Query.prototype.$where()][138]

##### Parameters

* js «String,Function» javascript string or function

##### Returns:

Specifies a javascript function or expression to pass to MongoDBs query system.

#### Example

    query.$where('this.comments.length === 10 || this.name.length === 5')

    query.$where(function () {
      return this.comments.length === 10 || this.name.length === 5;
    })

#### NOTE:

Only use `$where` when you have a condition that cannot be met using other MongoDB operators like `$lt`. **Be sure to read about all of [its caveats][139] before using.**

* * *

### [Query.prototype.where()][140]

##### Parameters

##### Returns:

Specifies a `path` for use with chaining.

#### Example

    User.find({age: {$gte: 21, $lte: 65}}, callback);

    User.where('age').gte(21).lte(65);

    User.find().where({ name: 'vonderful' })

    User
    .where('age').gte(21).lte(65)
    .where('name', /^vonderful/i)
    .where('friends').slice(10)
    .exec(callback)

* * *

### [Query.prototype.equals()][141]

##### Parameters

##### Returns:

Specifies the complementary comparison value for paths specified with `where()`

#### Example

    User.where('age').equals(49);

    User.where('age', 49);

* * *

### [Query.prototype.or()][142]

##### Parameters

* array «Array» array of conditions

##### Returns:

Specifies arguments for an `$or` condition.

#### Example

    query.or([{ color: 'red' }, { status: 'emergency' }])

* * *

### [Query.prototype.nor()][143]

##### Parameters

* array «Array» array of conditions

##### Returns:

Specifies arguments for a `$nor` condition.

#### Example

    query.nor([{ color: 'green' }, { status: 'ok' }])

* * *

### [Query.prototype.and()][144]

##### Parameters

* array «Array» array of conditions

##### Returns:

Specifies arguments for a `$and` condition.

#### Example

    query.and([{ color: 'green' }, { status: 'ok' }])

* * *

### [Query.prototype.gt()][145]

##### Parameters

Specifies a $gt query condition.

When called with one argument, the most recent path passed to `where()` is used.

#### Example

    Thing.find().where('age').gt(21)

    Thing.find().gt('age', 21)

* * *

### [Query.prototype.gte()][146]

##### Parameters

Specifies a $gte query condition.

When called with one argument, the most recent path passed to `where()` is used.

* * *

### [Query.prototype.lt()][147]

##### Parameters

Specifies a $lt query condition.

When called with one argument, the most recent path passed to `where()` is used.

* * *

### [Query.prototype.lte()][148]

##### Parameters

Specifies a $lte query condition.

When called with one argument, the most recent path passed to `where()` is used.

* * *

### [Query.prototype.ne()][149]

##### Parameters

Specifies a $ne query condition.

When called with one argument, the most recent path passed to `where()` is used.

* * *

### [Query.prototype.in()][150]

##### Parameters

Specifies an $in query condition.

When called with one argument, the most recent path passed to `where()` is used.

* * *

### [Query.prototype.nin()][151]

##### Parameters

Specifies an $nin query condition.

When called with one argument, the most recent path passed to `where()` is used.

* * *

### [Query.prototype.all()][152]

##### Parameters

Specifies an $all query condition.

When called with one argument, the most recent path passed to `where()` is used.

* * *

### [Query.prototype.size()][153]

##### Parameters

Specifies a $size query condition.

When called with one argument, the most recent path passed to `where()` is used.

#### Example

    MyModel.where('tags').size(0).exec(function (err, docs) {
      if (err) return handleError(err);

      assert(Array.isArray(docs));
      console.log('documents with 0 tags', docs);
    })

* * *

### [Query.prototype.regex()][154]

##### Parameters

Specifies a $regex query condition.

When called with one argument, the most recent path passed to `where()` is used.

* * *

### [Query.prototype.maxDistance()][155]

##### Parameters

Specifies a $maxDistance query condition.

When called with one argument, the most recent path passed to `where()` is used.

* * *

### [Query.prototype.mod()][156]

##### Parameters

* val «Array» must be of length 2, first element is `divisor`, 2nd element is `remainder`.

##### Returns:

Specifies a `$mod` condition, filters documents for documents whose `path` property is a number that is equal to `remainder` modulo `divisor`.

#### Example

    Product.find().mod('inventory', [2, 1]);
    Product.find().where('inventory').mod([2, 1]);

    Product.find().where('inventory').mod(2, 1);

* * *

### [Query.prototype.exists()][157]

##### Parameters

##### Returns:

Specifies an `$exists` condition

#### Example

    Thing.where('name').exists()
    Thing.where('name').exists(true)
    Thing.find().exists('name')

    Thing.where('name').exists(false);
    Thing.find().exists('name', false);

* * *

### [Query.prototype.elemMatch()][158]

##### Parameters

* criteria «Object,Function»

##### Returns:

Specifies an `$elemMatch` condition

#### Example

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

* * *

### [Query.prototype.within()][159]

##### Returns:

Defines a `$within` or `$geoWithin` argument for geo-spatial queries.

#### Example

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

#### NOTE:

As of Mongoose 3.7, `$geoWithin` is always used for queries. To change this behavior, see [Query.use$geoWithin][160].

#### NOTE:

In Mongoose 3.7, `within` changed from a getter to a function. If you need the old syntax, use [this][161].

* * *

### [Query.prototype.slice()][162]

##### Parameters

* val «Number» number/range of elements to slice

##### Returns:

Specifies a $slice projection for an array.

#### Example

    query.slice('comments', 5)
    query.slice('comments', -5)
    query.slice('comments', [10, 5])
    query.where('comments').slice(5)
    query.where('comments').slice([-10, 5])

* * *

### [Query.prototype.limit()][163]

##### Parameters

Specifies the maximum number of documents the query will return.

#### Example

    query.limit(20)

#### Note

Cannot be used with `distinct()`

* * *

### [Query.prototype.skip()][164]

##### Parameters

Specifies the number of documents to skip.

#### Example

    query.skip(100).limit(20)

#### Note

Cannot be used with `distinct()`

* * *

### [Query.prototype.maxScan()][165]

##### Parameters

Specifies the maxScan option.

#### Example

    query.maxScan(100)

#### Note

Cannot be used with `distinct()`

* * *

### [Query.prototype.batchSize()][166]

##### Parameters

Specifies the batchSize option.

#### Example

    query.batchSize(100)

#### Note

Cannot be used with `distinct()`

* * *

##### Parameters

Specifies the `comment` option.

#### Example

    query.comment('login query')

#### Note

Cannot be used with `distinct()`

* * *

### [Query.prototype.snapshot()][167]

##### Returns:

Specifies this query as a `snapshot` query.

#### Example

    query.snapshot()
    query.snapshot(true)
    query.snapshot(false)

#### Note

Cannot be used with `distinct()`

* * *

### [Query.prototype.hint()][168]

##### Parameters

* val «Object» a hint object

##### Returns:

Sets query hints.

#### Example

    query.hint({ indexA: 1, indexB: -1})

#### Note

Cannot be used with `distinct()`

* * *

### [Query.prototype.select()][169]

##### Parameters

##### Returns:

Specifies which document fields to include or exclude (also known as the query "projection")

When using string syntax, prefixing a path with `-` will flag that path as excluded. When a path does not have the `-` prefix, it is included. Lastly, if a path is prefixed with `+`, it forces inclusion of the path, which is useful for paths excluded at the [schema level][170].

A projection _must_ be either inclusive or exclusive. In other words, you must either list the fields to include (which excludes all others), or list the fields to exclude (which implies all other fields are included). The [`_id` field is the only exception because MongoDB includes it by default][171].

#### Example

    query.select('a b');

    query.select('-c -d');

    query.select({ a: 1, b: 1 });
    query.select({ c: 0, d: 0 });

    query.select('+path')

* * *

### [Query.prototype.slaveOk()][172]

##### Parameters

* v «Boolean» defaults to true

##### Returns:

_DEPRECATED_ Sets the slaveOk option.

**Deprecated** in MongoDB 2.2 in favor of [read preferences][173].

#### Example:

    query.slaveOk()
    query.slaveOk(true)
    query.slaveOk(false)

* * *

### [Query.prototype.read()][173]

##### Parameters

* [tags] «Array» optional tags for this query

##### Returns:

Determines the MongoDB nodes from which to read.

#### Preferences:

    primary - (default) Read from primary only. Operations will produce an error if primary is unavailable. Cannot be combined with tags.
    secondary            Read from secondary if available, otherwise error.
    primaryPreferred     Read from primary if available, otherwise a secondary.
    secondaryPreferred   Read from a secondary if available, otherwise read from the primary.
    nearest              All operations read from among the nearest candidates, but unlike other modes, this option will include both the primary and all secondaries in the random selection.

Aliases

    p   primary
    pp  primaryPreferred
    s   secondary
    sp  secondaryPreferred
    n   nearest

#### Example:

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

Read more about how to use read preferrences [here][174] and [here][175].

* * *

### [Query.prototype.merge()][176]

##### Parameters

##### Returns:

Merges another Query or conditions object into this one.

When a Query is passed, conditions, field selection and options are merged.

New in 3.7.0

* * *

### [Query.prototype.setOptions()][177]

##### Parameters

Sets query options. Some options only make sense for certain operations.

#### Options:

The following options are only for `find()`: - [tailable][178] \- [sort][179] \- [limit][180] \- [skip][181] \- [maxscan][182] \- [batchSize][183] \- [comment][184] \- [snapshot][185] \- [readPreference][174] \- [hint][186]

The following options are only for `update()`, `updateOne()`, `updateMany()`, `replaceOne()`, `findOneAndUpdate()`, and `findByIdAndUpdate()`: - [upsert][187] \- [writeConcern][187]

The following options are only for `find()`, `findOne()`, `findById()`, `findOneAndUpdate()`, `findByIdAndUpdate()`, and `geoSearch()`: - [lean][188]

## The following options are for all operations

* * *

### [Query.prototype.getQuery()][189]

##### Returns:

* «Object» current query conditions

Returns the current query conditions as a JSON object.

#### Example:

    var query = new Query();
    query.find({ a: 1 }).where('b').gt(2);
    query.getQuery();

* * *

### [Query.prototype.getUpdate()][190]

##### Returns:

* «Object» current update operations

Returns the current update operations as a JSON object.

#### Example:

    var query = new Query();
    query.update({}, { $set: { a: 5 } });
    query.getUpdate();

* * *

### [Query.prototype.lean()][191]

##### Parameters

* bool «Boolean,Object» defaults to true

##### Returns:

Sets the lean option.

Documents returned from queries with the `lean` option enabled are plain javascript objects, not [MongooseDocuments][192]. They have no `save` method, getters/setters or other Mongoose magic applied.

#### Example:

    new Query().lean()
    new Query().lean(true)
    new Query().lean(false)

    Model.find().lean().exec(function (err, docs) {
      docs[0] instanceof mongoose.Document
    });

This is a [great][193] option in high-performance read-only scenarios, especially when combined with [stream][194].

* * *

### [Query.prototype.error()][195]

##### Parameters

* err «Error,null» if set, `exec()` will fail fast before sending the query to MongoDB

Gets/sets the error flag on this query. If this flag is not null or undefined, the `exec()` promise will reject without executing.

#### Example:

    Query().error();
    Query().error(null);
    Query().error(new Error('test'));
    Schema.pre('find', function() {
      if (!this.getQuery().userId) {
        this.error(new Error('Not allowed to query without setting userId'));
      }
    });

Note that query casting runs **after** hooks, so cast errors will override custom errors.

#### Example:

    var TestSchema = new Schema({ num: Number });
    var TestModel = db.model('Test', TestSchema);
    TestModel.find({ num: 'not a number' }).error(new Error('woops')).exec(function(error) {

    });

* * *

### [Query.prototype.mongooseOptions()][196]

##### Parameters

* options «Object» if specified, overwrites the current options

Getter/setter around the current mongoose-specific options for this query (populate, lean, etc.)

* * *

### [Query.prototype.find()][197]

##### Parameters

##### Returns:

Finds documents.

When no `callback` is passed, the query is not executed. When the query is executed, the result will be an array of documents.

#### Example

    query.find({ name: 'Los Pollos Hermanos' }).find(callback)

* * *

### [Query.prototype.merge()][176]

##### Parameters

##### Returns:

Merges another Query or conditions object into this one.

When a Query is passed, conditions, field selection and options are merged.

* * *

### [Query.prototype.collation()][198]

##### Parameters

##### Returns:

Adds a collation to this op (MongoDB 3.4 and up)

* * *

### [Query.prototype.findOne()][199]

##### Parameters

* [callback] «Function» optional params are (error, document)

##### Returns:

Declares the query a findOne operation. When executed, the first found document is passed to the callback.

Passing a `callback` executes the query. The result of the query is a single document.

* _Note:_ `conditions` is optional, and if `conditions` is null or undefined, mongoose will send an empty `findOne` command to MongoDB, which will return an arbitrary document. If you're querying by `_id`, use `Model.findById()` instead.

This function triggers the following middleware.

#### Example

    var query  = Kitten.where({ color: 'white' });
    query.findOne(function (err, kitten) {
      if (err) return handleError(err);
      if (kitten) {

      }
    });

* * *

### [Query.prototype.count()][200]

##### Parameters

* [callback] «Function» optional params are (error, count)

##### Returns:

Specifying this query as a `count` query.

Passing a `callback` executes the query.

This function triggers the following middleware.

#### Example:

    var countQuery = model.where({ 'color': 'black' }).count();

    query.count({ color: 'black' }).count(callback)

    query.count({ color: 'black' }, callback)

    query.where('color', 'black').count(function (err, count) {
      if (err) return handleError(err);
      console.log('there are %d kittens', count);
    })

* * *

### [Query.prototype.distinct()][201]

##### Parameters

* [callback] «Function» optional params are (error, arr)

##### Returns:

Declares or executes a distict() operation.

Passing a `callback` executes the query.

This function does not trigger any middleware.

#### Example

    distinct(field, conditions, callback)
    distinct(field, conditions)
    distinct(field, callback)
    distinct(field)
    distinct(callback)
    distinct()

* * *

### [Query.prototype.sort()][202]

##### Parameters

##### Returns:

Sets the sort order

If an object is passed, values allowed are `asc`, `desc`, `ascending`, `descending`, `1`, and `-1`.

If a string is passed, it must be a space delimited list of path names. The sort order of each path is ascending unless the path name is prefixed with `-` which will be treated as descending.

#### Example

    query.sort({ field: 'asc', test: -1 });

    query.sort('field -test');

#### Note

Cannot be used with `distinct()`

* * *

### [Query.prototype.remove()][203]

##### Parameters

* [callback] «Function» optional params are (error, writeOpResult)

##### Returns:

Declare and/or execute this query as a remove() operation.

This function does not trigger any middleware

#### Example

    Model.remove({ artist: 'Anne Murray' }, callback)

#### Note

The operation is only executed when a callback is passed. To force execution without a callback, you must first call `remove()` and then execute it by using the `exec()` method.

    var query = Model.find().remove({ name: 'Anne Murray' })

    query.remove({ name: 'Anne Murray' }, callback)
    query.remove({ name: 'Anne Murray' }).remove(callback)

    query.exec()

    query.remove(conds, fn);
    query.remove(conds)
    query.remove(fn)
    query.remove()

* * *

### [Query.prototype.deleteOne()][204]

##### Parameters

* [callback] «Function» optional params are (error, writeOpResult)

##### Returns:

Declare and/or execute this query as a `deleteOne()` operation. Works like remove, except it deletes at most one document regardless of the `single` option.

This function does not trigger any middleware.

#### Example

    Character.deleteOne({ name: 'Eddard Stark' }, callback)
    Character.deleteOne({ name: 'Eddard Stark' }).then(next)

* * *

### [Query.prototype.deleteMany()][205]

##### Parameters

* [callback] «Function» optional params are (error, writeOpResult)

##### Returns:

Declare and/or execute this query as a `deleteMany()` operation. Works like remove, except it deletes _every_ document that matches `criteria` in the collection, regardless of the value of `single`.

This function does not trigger any middleware

#### Example

    Character.deleteMany({ name: /Stark/, age: { $gte: 18 } }, callback)
    Character.deleteMany({ name: /Stark/, age: { $gte: 18 } }).then(next)

* * *

### [Query.prototype.findOneAndUpdate()][206]

##### Parameters

* [callback] «Function» optional params are (error, doc), _unless_ `rawResult` is used, in which case params are (error, writeOpResult)

##### Returns:

Issues a mongodb [findAndModify][207] update command.

Finds a matching document, updates it according to the `update` arg, passing any `options`, and returns the found document (if any) to the callback. The query executes immediately if `callback` is passed.

This function triggers the following middleware.

#### Available options

* `new`: bool - if true, return the modified document rather than the original. defaults to false (changed in 4.0)
* `upsert`: bool - creates the object if it doesn't exist. defaults to false.
* `fields`: {Object|String} - Field selection. Equivalent to `.select(fields).findOneAndUpdate()`
* `sort`: if multiple docs are found by the conditions, sets the sort order to choose which doc to update
* `maxTimeMS`: puts a time limit on the query - requires mongodb >= 2.6.0
* `runValidators`: if true, runs [update validators][107] on this command. Update validators validate the update operation against the model's schema.
* `setDefaultsOnInsert`: if this and `upsert` are true, mongoose will apply the [defaults][108] specified in the model's schema if a new document is created. This option only works on MongoDB >= 2.4 because it relies on [MongoDB's `$setOnInsert` operator][109].
* `rawResult`: if true, returns the [raw result from the MongoDB driver][110]
* `context` (string) if set to 'query' and `runValidators` is on, `this` will refer to the query in custom validator functions that update validation runs. Does nothing if `runValidators` is false.

#### Callback Signature

    function(error, doc) {

    }

#### Examples

    query.findOneAndUpdate(conditions, update, options, callback)
    query.findOneAndUpdate(conditions, update, options)
    query.findOneAndUpdate(conditions, update, callback)
    query.findOneAndUpdate(conditions, update)
    query.findOneAndUpdate(update, callback)
    query.findOneAndUpdate(update)
    query.findOneAndUpdate(callback)
    query.findOneAndUpdate()

* * *

### [Query.prototype.findOneAndRemove()][208]

##### Parameters

* [callback] «Function» optional params are (error, document)

##### Returns:

Issues a mongodb [findAndModify][207] remove command.

Finds a matching document, removes it, passing the found document (if any) to the callback. Executes immediately if `callback` is passed.

This function triggers the following middleware.

#### Available options

* `sort`: if multiple docs are found by the conditions, sets the sort order to choose which doc to update
* `maxTimeMS`: puts a time limit on the query - requires mongodb >= 2.6.0
* `rawResult`: if true, resolves to the [raw result from the MongoDB driver][110]

#### Callback Signature

    function(error, doc) {

    }

#### Examples

    A.where().findOneAndRemove(conditions, options, callback)
    A.where().findOneAndRemove(conditions, options)
    A.where().findOneAndRemove(conditions, callback)
    A.where().findOneAndRemove(conditions)
    A.where().findOneAndRemove(callback)
    A.where().findOneAndRemove()

* * *

### [Query.prototype.update()][209]

##### Parameters

* [callback] «Function» optional, params are (error, writeOpResult)

##### Returns:

Declare and/or execute this query as an update() operation.

_All paths passed that are not $atomic operations will become $set ops._

This function triggers the following middleware.

#### Example

    Model.where({ _id: id }).update({ title: 'words' })

    Model.where({ _id: id }).update({ $set: { title: 'words' }})

#### Valid options:

* `safe` (boolean) safe mode (defaults to value set in schema (true))
* `upsert` (boolean) whether to create the doc if it doesn't match (false)
* `multi` (boolean) whether multiple documents should be updated (false)
* `runValidators`: if true, runs [update validators][107] on this command. Update validators validate the update operation against the model's schema.
* `setDefaultsOnInsert`: if this and `upsert` are true, mongoose will apply the [defaults][108] specified in the model's schema if a new document is created. This option only works on MongoDB >= 2.4 because it relies on [MongoDB's `$setOnInsert` operator][109].
* `strict` (boolean) overrides the `strict` option for this update
* `overwrite` (boolean) disables update-only mode, allowing you to overwrite the doc (false)
* `context` (string) if set to 'query' and `runValidators` is on, `this` will refer to the query in custom validator functions that update validation runs. Does nothing if `runValidators` is false.

#### Note

Passing an empty object `{}` as the doc will result in a no-op unless the `overwrite` option is passed. Without the `overwrite` option set, the update operation will be ignored and the callback executed without sending the command to MongoDB so as to prevent accidently overwritting documents in the collection.

#### Note

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
    Model.where({ email: '[address@example.com][210]' })
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

* * *

### [Query.prototype.updateMany()][211]

##### Parameters

* [callback] «Function» optional params are (error, writeOpResult)

##### Returns:

Declare and/or execute this query as an updateMany() operation. Same as `update()`, except MongoDB will update _all_ documents that match `criteria` (as opposed to just the first one) regardless of the value of the `multi` option.

**Note** updateMany will _not_ fire update middleware. Use `pre('updateMany')` and `post('updateMany')` instead.

This function triggers the following middleware.

* * *

### [Query.prototype.updateOne()][212]

##### Parameters

* [callback] «Function» params are (error, writeOpResult)

##### Returns:

Declare and/or execute this query as an updateOne() operation. Same as `update()`, except MongoDB will update _only_ the first document that matches `criteria` regardless of the value of the `multi` option.

**Note** updateOne will _not_ fire update middleware. Use `pre('updateOne')` and `post('updateOne')` instead.

This function triggers the following middleware.

* * *

### [Query.prototype.replaceOne()][213]

##### Parameters

* [callback] «Function» optional params are (error, writeOpResult)

##### Returns:

Declare and/or execute this query as a replaceOne() operation. Same as `update()`, except MongoDB will replace the existing document and will not accept any atomic operators (`$set`, etc.)

**Note** replaceOne will _not_ fire update middleware. Use `pre('replaceOne')` and `post('replaceOne')` instead.

This function triggers the following middleware.

* * *

### [Query.prototype.exec()][214]

##### Parameters

* [callback] «Function» optional params depend on the function being called

##### Returns:

Executes the query

#### Examples:

    var promise = query.exec();
    var promise = query.exec('update');

    query.exec(callback);
    query.exec('find', callback);

* * *

### [Query.prototype.then()][215]

##### Parameters

##### Returns:

Executes the query returning a `Promise` which will be resolved with either the doc(s) or rejected with the error.

* * *

### [Query.prototype.catch()][216]

##### Parameters

##### Returns:

Executes the query returning a `Promise` which will be resolved with either the doc(s) or rejected with the error. Like `.then()`, but only takes a rejection handler.

* * *

### [Query.prototype.populate()][217]

##### Parameters

* [options] «Object» Options for the population query (sort, etc)

##### Returns:

Specifies paths which should be populated with other documents.

#### Example:

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

* * *

### [Query.prototype.cast()][218]

##### Parameters

##### Returns:

Casts this query to the schema of `model`

#### Note

If `obj` is present, it is cast instead of this query.

* * *

### [Query.prototype.cursor()][219]

##### Parameters

##### Returns:

Returns a wrapper around a [mongodb driver cursor][220]. A QueryCursor exposes a [Streams3][221]-compatible interface, as well as a `.next()` function.

The `.cursor()` function triggers pre find hooks, but **not** post find hooks.

#### Example

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

#### Valid options

* `transform`: optional function which accepts a mongoose document. The return value of the function will be emitted on `data` and returned by `.next()`.
* * *

### [Query.prototype.maxscan()][222]

_DEPRECATED_ Alias of `maxScan`

* * *

### [Query.prototype.tailable()][223]

##### Parameters

* [opts.tailableRetryInterval] «Number» if cursor is exhausted, wait this many milliseconds before retrying

Sets the tailable option (for use with capped collections).

#### Example

    query.tailable()
    query.tailable(true)
    query.tailable(false)

#### Note

Cannot be used with `distinct()`

* * *

### [Query.prototype.intersects()][224]

##### Parameters

##### Returns:

Declares an intersects query for `geometry()`.

#### Example

    query.where('path').intersects().geometry({
        type: 'LineString'
      , coordinates: [[180.0, 11.0], [180, 9.0]]
    })

    query.where('path').intersects({
        type: 'LineString'
      , coordinates: [[180.0, 11.0], [180, 9.0]]
    })

#### NOTE:

**MUST** be used after `where()`.

#### NOTE:

In Mongoose 3.7, `intersects` changed from a getter to a function. If you need the old syntax, use [this][161].

* * *

### [Query.prototype.geometry()][225]

##### Parameters

* object «Object» Must contain a `type` property which is a String and a `coordinates` property which is an Array. See the examples.

##### Returns:

Specifies a `$geometry` condition

#### Example

    var polyA = [[[ 10, 20 ], [ 10, 40 ], [ 30, 40 ], [ 30, 20 ]]]
    query.where('loc').within().geometry({ type: 'Polygon', coordinates: polyA })

    var polyB = [[ 0, 0 ], [ 1, 1 ]]
    query.where('loc').within().geometry({ type: 'LineString', coordinates: polyB })

    var polyC = [ 0, 0 ]
    query.where('loc').within().geometry({ type: 'Point', coordinates: polyC })

    query.where('loc').intersects().geometry({ type: 'Point', coordinates: polyC })

The argument is assigned to the most recent path passed to `where()`.

#### NOTE:

`geometry()` **must** come after either `intersects()` or `within()`.

The `object` argument must contain `type` and `coordinates` properties. - type {String} - coordinates {Array}
* * *

### [Query.prototype.near()][226]

##### Parameters

##### Returns:

Specifies a `$near` or `$nearSphere` condition

These operators return documents sorted by distance.

#### Example

    query.where('loc').near({ center: [10, 10] });
    query.where('loc').near({ center: [10, 10], maxDistance: 5 });
    query.where('loc').near({ center: [10, 10], maxDistance: 5, spherical: true });
    query.near('loc', { center: [10, 10], maxDistance: 5 });

* * *

### [Query.prototype.nearSphere()][227]

_DEPRECATED_ Specifies a `$nearSphere` condition

#### Example

    query.where('loc').nearSphere({ center: [10, 10], maxDistance: 5 });

**Deprecated.** Use `query.near()` instead with the `spherical` option set to `true`.

#### Example

    query.where('loc').near({ center: [10, 10], spherical: true });

* * *

### [Query.prototype.polygon()][228]

##### Parameters

* [coordinatePairs...] «Array,Object»

##### Returns:

Specifies a $polygon condition

#### Example

    query.where('loc').within().polygon([10,20], [13, 25], [7,15])
    query.polygon('loc', [10,20], [13, 25], [7,15])

* * *

### [Query.prototype.box()][229]

##### Parameters

* Upper «[Array]» Right Coords

##### Returns:

Specifies a $box condition

#### Example

    var lowerLeft = [40.73083, -73.99756]
    var upperRight= [40.741404,  -73.988135]

    query.where('loc').within().box(lowerLeft, upperRight)
    query.box({ ll : lowerLeft, ur : upperRight })

* * *

### [Query.prototype.circle()][230]

##### Parameters

##### Returns:

Specifies a $center or $centerSphere condition.

#### Example

    var area = { center: [50, 50], radius: 10, unique: true }
    query.where('loc').within().circle(area)

    query.circle('loc', area);

    var area = { center: [50, 50], radius: 10, unique: true, spherical: true }
    query.where('loc').within().circle(area)

    query.circle('loc', area);

New in 3.7.0

* * *

### [Query.prototype.center()][231]

* * *

### [Query.prototype.centerSphere()][232]

##### Parameters

##### Returns:

_DEPRECATED_ Specifies a $centerSphere condition

**Deprecated.** Use [circle][230] instead.

#### Example

    var area = { center: [50, 50], radius: 10 };
    query.where('loc').within().centerSphere(area);

* * *

### [Query.prototype.selected()][233]

##### Returns:

Determines if field selection has been made.

* * *

### [Query.prototype.selectedInclusively()][234]

##### Returns:

Determines if inclusive field selection has been made.

    query.selectedInclusively()
    query.select('name')
    query.selectedInclusively()

* * *

### [Query.prototype.selectedExclusively()][235]

##### Returns:

Determines if exclusive field selection has been made.

    query.selectedExclusively()
    query.select('-name')
    query.selectedExclusively()
    query.selectedInclusively()

* * *
* * *