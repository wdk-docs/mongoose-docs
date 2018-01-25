
### [Aggregate()][130]

##### Parameters

* [pipeline] «Array» aggregation pipeline as an array of objects

Aggregate constructor used for building aggregation pipelines. Do not instantiate this class directly, use [Model.aggregate()][236] instead.

#### Example:

    const aggregate = Model.aggregate([
      { $project: { a: 1, b: 1 } },
      { $skip: 5 }
    ]);

    Model.
      aggregate({ $match: { age: { $gte: 21 }}}).
      unwind('tags').
      exec(callback);

#### Note:

* The documents returned are plain javascript objects, not mongoose documents (since any shape of document can be returned).
* Mongoose does **not** cast pipeline stages. The below will **not** work unless `_id` is a string in the database

      new Aggregate([{ $match: { _id: '00000000000000000000000a' } }]);
      // Do this instead to cast to an ObjectId
      new Aggregate([{ $match: { _id: mongoose.Types.ObjectId('00000000000000000000000a') } }]);

* * *

### [Aggregate.prototype.model()][237]

##### Parameters

* model «Model» the model to which the aggregate is to be bound

##### Returns:

Binds this aggregate to a model.

* * *

### [Aggregate.prototype.append()][238]

##### Parameters

* ops «Object» operator(s) to append

##### Returns:

Appends new operators to this aggregate pipeline

#### Examples:

    aggregate.append({ $project: { field: 1 }}, { $limit: 2 });

    var pipeline = [{ $match: { daw: 'Logic Audio X' }} ];
    aggregate.append(pipeline);

* * *

### [Aggregate.prototype.addFields()][239]

##### Parameters

* arg «Object» field specification

##### Returns:

Appends a new $addFields operator to this aggregate pipeline. Requires MongoDB v3.4+ to work

#### Examples:

    aggregate.addFields({
        newField: '$b.nested'
      , plusTen: { $add: ['$val', 10]}
      , sub: {
           name: '$a'
        }
    })

    aggregate.addFields({ salary_k: { $divide: [ "$salary", 1000 ] } });

* * *

### [Aggregate.prototype.project()][240]

##### Parameters

* arg «Object,String» field specification

##### Returns:

Appends a new $project operator to this aggregate pipeline.

Mongoose query [selection syntax][169] is also supported.

#### Examples:

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

* * *

### [Aggregate.prototype.group()][241]

##### Parameters

* arg «Object» $group operator contents

##### Returns:

Appends a new custom $group operator to this aggregate pipeline.

#### Examples:

    aggregate.group({ _id: "$department" });

* * *

### [Aggregate.prototype.match()][242]

##### Parameters

* arg «Object» $match operator contents

##### Returns:

Appends a new custom $match operator to this aggregate pipeline.

#### Examples:

    aggregate.match({ department: { $in: [ "sales", "engineering" ] } });

* * *

### [Aggregate.prototype.skip()][243]

##### Parameters

* num «Number» number of records to skip before next stage

##### Returns:

Appends a new $skip operator to this aggregate pipeline.

#### Examples:

    aggregate.skip(10);

* * *

### [Aggregate.prototype.limit()][244]

##### Parameters

* num «Number» maximum number of records to pass to the next stage

##### Returns:

Appends a new $limit operator to this aggregate pipeline.

#### Examples:

    aggregate.limit(10);

* * *

### [Aggregate.prototype.near()][245]

##### Parameters

##### Returns:

Appends a new $geoNear operator to this aggregate pipeline.

#### NOTE:

**MUST** be used as the first operator in the pipeline.

#### Examples:

    aggregate.near({
      near: [40.724, -73.997],
      distanceField: "dist.calculated",
      maxDistance: 0.008,
      query: { type: "public" },
      includeLocs: "dist.location",
      uniqueDocs: true,
      num: 5
    });

* * *

### [Aggregate.prototype.unwind()][246]

##### Parameters

* fields «String» the field(s) to unwind

##### Returns:

Appends new custom $unwind operator(s) to this aggregate pipeline.

Note that the `$unwind` operator requires the path name to start with '$'. Mongoose will prepend '$' if the specified field doesn't start '$'.

#### Examples:

    aggregate.unwind("tags");
    aggregate.unwind("a", "b", "c");

* * *

### [Aggregate.prototype.lookup()][247]

##### Parameters

* options «Object» to $lookup as described in the above link

##### Returns:

Appends new custom $lookup operator(s) to this aggregate pipeline.

#### Examples:

    aggregate.lookup({ from: 'users', localField: 'userId', foreignField: '_id', as: 'users' });

* * *

### [Aggregate.prototype.graphLookup()][248]

##### Parameters

* options «Object» to $graphLookup as described in the above link

##### Returns:

Appends new custom $graphLookup operator(s) to this aggregate pipeline, performing a recursive search on a collection.

Note that graphLookup can only consume at most 100MB of memory, and does not allow disk use even if `{ allowDiskUse: true }` is specified.

#### Examples:

     aggregate.graphLookup({ from: 'courses', startWith: '$prerequisite', connectFromField: 'prerequisite', connectToField: 'name', as: 'prerequisites', maxDepth: 3 })

* * *

### [Aggregate.prototype.sample()][249]

##### Parameters

* size «Number» number of random documents to pick

##### Returns:

Appepnds new custom $sample operator(s) to this aggregate pipeline.

#### Examples:

    aggregate.sample(3);

* * *

### [Aggregate.prototype.sort()][250]

##### Parameters

##### Returns:

Appends a new $sort operator to this aggregate pipeline.

If an object is passed, values allowed are `asc`, `desc`, `ascending`, `descending`, `1`, and `-1`.

If a string is passed, it must be a space delimited list of path names. The sort order of each path is ascending unless the path name is prefixed with `-` which will be treated as descending.

#### Examples:

    aggregate.sort({ field: 'asc', test: -1 });
    aggregate.sort('field -test');

* * *

### [Aggregate.prototype.read()][251]

##### Parameters

* [tags] «Array» optional tags for this query

Sets the readPreference option for the aggregation query.

#### Example:

    Model.aggregate(..).read('primaryPreferred').exec(callback)

* * *

### [Aggregate.prototype.explain()][252]

##### Parameters

##### Returns:

Execute the aggregation with explain

#### Example:

    Model.aggregate(..).explain(callback)

* * *

### [Aggregate.prototype.allowDiskUse()][253]

##### Parameters

* [tags] «Array» optional tags for this query

Sets the allowDiskUse option for the aggregation query (ignored for < 2.6.0)

#### Example:

    Model.aggregate(..).allowDiskUse(true).exec(callback)

* * *

### [Aggregate.prototype.option()][254]

##### Parameters

* value «Object» keys to merge into current options

##### Returns:

Lets you set arbitrary options, for middleware or plugins.

#### Example:

    var agg = Model.aggregate(..).option({ allowDiskUse: true });
    agg.options;

* * *

### [Aggregate.prototype.cursor()][255]

##### Parameters

* [options.useMongooseAggCursor] «Boolean» use experimental mongoose-specific aggregation cursor (for `eachAsync()` and other query cursor semantics)

Sets the cursor option option for the aggregation query (ignored for < 2.6.0). Note the different syntax below: .exec() returns a cursor object, and no callback is necessary.

#### Example:

    var cursor = Model.aggregate(..).cursor({ batchSize: 1000 }).exec();
    cursor.each(function(error, doc) {

    });

* * *

### [Aggregate.prototype.addCursorFlag()][256]

##### Parameters

Adds a [cursor flag][257]

#### Example:

    Model.aggregate(..).addCursorFlag('noCursorTimeout', true).exec();

* * *

### [Aggregate.prototype.collation()][258]

##### Parameters

Adds a collation

#### Example:

    Model.aggregate(..).collation({ locale: 'en_US', strength: 1 }).exec();

* * *

### [Aggregate.prototype.facet()][259]

##### Parameters

##### Returns:

Combines multiple aggregation pipelines.

#### Example:

    Model.aggregate(...)
     .facet({
       books: [{ groupBy: '$author' }],
       price: [{ $bucketAuto: { groupBy: '$price', buckets: 2 } }]
     })
     .exec();

* * *

### [Aggregate.prototype.pipeline()][260]

##### Returns:

Returns the current pipeline

#### Example:

    MyModel.aggregate().match({ test: 1 }).pipeline();

* * *

### [Aggregate.prototype.exec()][261]

##### Parameters

##### Returns:

Executes the aggregate pipeline on the currently bound Model.

#### Example:

    aggregate.exec(callback);

    var promise = aggregate.exec();
    promise.then(..);

* * *

### [Aggregate.prototype.then()][262]

##### Parameters

* [reject] «Function» errorCallback

##### Returns:

Provides promise for aggregate.

#### Example:

    Model.aggregate(..).then(successCallback, errorCallback);

* * *
* * *
