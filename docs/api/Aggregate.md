
# 聚合

## Aggregate()

**参数**

* [pipeline] «Array» aggregation pipeline as an array of objects

Aggregate constructor used for building aggregation pipelines. Do not instantiate this class directly, use [Model.aggregate() instead.

**示例**

    const aggregate = Model.aggregate([
      { $project: { a: 1, b: 1 } },
      { $skip: 5 }
    ]);

    Model.
      aggregate({ $match: { age: { $gte: 21 }}}).
      unwind('tags').
      exec(callback);

**注释**

* The documents returned are plain javascript objects, not mongoose documents (since any shape of document can be returned).
* Mongoose does **not** cast pipeline stages. The below will **not** work unless `_id` is a string in the database

      new Aggregate([{ $match: { _id: '00000000000000000000000a' } }]);
      // Do this instead to cast to an ObjectId
      new Aggregate([{ $match: { _id: mongoose.Types.ObjectId('00000000000000000000000a') } }]);

## Aggregate.prototype.model()

**参数**

* model «Model» the model to which the aggregate is to be bound

**返回**

Binds this aggregate to a model.

## Aggregate.prototype.append()

**参数**

* ops «Object» operator(s) to append

**返回**

Appends new operators to this aggregate pipeline

**示例**

    aggregate.append({ $project: { field: 1 }}, { $limit: 2 });

    var pipeline = [{ $match: { daw: 'Logic Audio X' }} ];
    aggregate.append(pipeline);

## Aggregate.prototype.addFields()

**参数**

* arg «Object» field specification

**返回**

Appends a new $addFields operator to this aggregate pipeline. Requires MongoDB v3.4+ to work

**示例**

    aggregate.addFields({
        newField: '$b.nested'
      , plusTen: { $add: ['$val', 10]}
      , sub: {
           name: '$a'
        }
    })

    aggregate.addFields({ salary_k: { $divide: [ "$salary", 1000 ] } });

## Aggregate.prototype.project()

**参数**

* arg «Object,String» field specification

**返回**

Appends a new $project operator to this aggregate pipeline.

Mongoose query [selection syntax is also supported.

**示例**

    aggregate.project("a b -_id");

    aggregate.project({a: 1, b: 1, _id: 0});

    aggregate.project({
        newField: '$b.nested'
      , plusTen: { $add: ['$val', 10]}
      , sub: {
           name: '$a'
        }
    })

    aggregate.project({ salary_k: { $divide: [ "$salary", 1000 ] } });

## Aggregate.prototype.group()

**参数**

* arg «Object» $group operator contents

**返回**

Appends a new custom $group operator to this aggregate pipeline.

**示例**

    aggregate.group({ _id: "$department" });

## Aggregate.prototype.match()

**参数**

* arg «Object» $match operator contents

**返回**

Appends a new custom $match operator to this aggregate pipeline.

**示例**

    aggregate.match({ department: { $in: [ "sales", "engineering" ] } });

## Aggregate.prototype.skip()

**参数**

* num «Number» number of records to skip before next stage

**返回**

Appends a new $skip operator to this aggregate pipeline.

**示例**

    aggregate.skip(10);

## Aggregate.prototype.limit()

**参数**

* num «Number» maximum number of records to pass to the next stage

**返回**

Appends a new $limit operator to this aggregate pipeline.

**示例**

    aggregate.limit(10);

## Aggregate.prototype.near()

**参数**

**返回**

Appends a new $geoNear operator to this aggregate pipeline.

**注释**

**MUST** be used as the first operator in the pipeline.

**示例**

    aggregate.near({
      near: [40.724, -73.997],
      distanceField: "dist.calculated",
      maxDistance: 0.008,
      query: { type: "public" },
      includeLocs: "dist.location",
      uniqueDocs: true,
      num: 5
    });

## Aggregate.prototype.unwind()

**参数**

* fields «String» the field(s) to unwind

**返回**

Appends new custom $unwind operator(s) to this aggregate pipeline.

Note that the `$unwind` operator requires the path name to start with '$'. Mongoose will prepend '$' if the specified field doesn't start '$'.

**示例**

    aggregate.unwind("tags");
    aggregate.unwind("a", "b", "c");

## Aggregate.prototype.lookup()

**参数**

* options «Object» to $lookup as described in the above link

**返回**

Appends new custom $lookup operator(s) to this aggregate pipeline.

**示例**

    aggregate.lookup({ from: 'users', localField: 'userId', foreignField: '_id', as: 'users' });

## Aggregate.prototype.graphLookup()

**参数**

* options «Object» to $graphLookup as described in the above link

**返回**

Appends new custom $graphLookup operator(s) to this aggregate pipeline, performing a recursive search on a collection.

Note that graphLookup can only consume at most 100MB of memory, and does not allow disk use even if `{ allowDiskUse: true }` is specified.

**示例**

     aggregate.graphLookup({ from: 'courses', startWith: '$prerequisite', connectFromField: 'prerequisite', connectToField: 'name', as: 'prerequisites', maxDepth: 3 })

## Aggregate.prototype.sample()

**参数**

* size «Number» number of random documents to pick

**返回**

Appepnds new custom $sample operator(s) to this aggregate pipeline.

**示例**

    aggregate.sample(3);

## Aggregate.prototype.sort()

**参数**

**返回**

Appends a new $sort operator to this aggregate pipeline.

If an object is passed, values allowed are `asc`, `desc`, `ascending`, `descending`, `1`, and `-1`.

If a string is passed, it must be a space delimited list of path names. The sort order of each path is ascending unless the path name is prefixed with `-` which will be treated as descending.

**示例**

    aggregate.sort({ field: 'asc', test: -1 });
    aggregate.sort('field -test');

## Aggregate.prototype.read()

**参数**

* [tags] «Array» optional tags for this query

Sets the readPreference option for the aggregation query.

**示例**

    Model.aggregate(..).read('primaryPreferred').exec(callback)

## Aggregate.prototype.explain()

**参数**

**返回**

Execute the aggregation with explain

**示例**

    Model.aggregate(..).explain(callback)

## Aggregate.prototype.allowDiskUse()

**参数**

* [tags] «Array» optional tags for this query

Sets the allowDiskUse option for the aggregation query (ignored for < 2.6.0)

**示例**

    Model.aggregate(..).allowDiskUse(true).exec(callback)

## Aggregate.prototype.option()

**参数**

* value «Object» keys to merge into current options

**返回**

Lets you set arbitrary options, for middleware or plugins.

**示例**

    var agg = Model.aggregate(..).option({ allowDiskUse: true });
    agg.options;

## Aggregate.prototype.cursor()

**参数**

* [options.useMongooseAggCursor] «Boolean» use experimental mongoose-specific aggregation cursor (for `eachAsync()` and other query cursor semantics)

Sets the cursor option option for the aggregation query (ignored for < 2.6.0). Note the different syntax below: .exec() returns a cursor object, and no callback is necessary.

**示例**

    var cursor = Model.aggregate(..).cursor({ batchSize: 1000 }).exec();
    cursor.each(function(error, doc) {

    });

## Aggregate.prototype.addCursorFlag()

**参数**

Adds a [cursor flag

**示例**

    Model.aggregate(..).addCursorFlag('noCursorTimeout', true).exec();

## Aggregate.prototype.collation()

**参数**

Adds a collation

**示例**

    Model.aggregate(..).collation({ locale: 'en_US', strength: 1 }).exec();

## Aggregate.prototype.facet()

**参数**

**返回**

Combines multiple aggregation pipelines.

**示例**

    Model.aggregate(...)
     .facet({
       books: [{ groupBy: '$author' }],
       price: [{ $bucketAuto: { groupBy: '$price', buckets: 2 } }]
     })
     .exec();

## Aggregate.prototype.pipeline()

**返回**

Returns the current pipeline

**示例**

    MyModel.aggregate().match({ test: 1 }).pipeline();

## Aggregate.prototype.exec()

**参数**

**返回**

Executes the aggregate pipeline on the currently bound Model.

**示例**

    aggregate.exec(callback);

    var promise = aggregate.exec();
    promise.then(..);

## Aggregate.prototype.then()

**参数**

* [reject] «Function» errorCallback

**返回**

Provides promise for aggregate.

**示例**

    Model.aggregate(..).then(successCallback, errorCallback);

* * *
* * *
