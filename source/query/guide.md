# 查询

[Source](http://mongoosejs.com/docs/queries.html "Permalink to Mongoose v5.0.1: Queries")

Documents can be retrieved through several static helper methods of [models][1].

Any [model][2] method which [involves][3] [specifying][4] [query][5] [conditions][6] can be executed two ways:

When a `callback` function:

* is passed, the operation will be executed immediately with the results passed to the callback.
* is not passed, an instance of [Query][7] is returned, which provides a special query builder interface.

A [Query][7] has a `.then()` function, and thus can be used as a promise.

When executing a query with a `callback` function, you specify your query as a JSON document. The JSON document's syntax is the same as the [MongoDB shell][8].


    var Person = mongoose.model('Person', yourSchema);


    Person.findOne({ 'name.last': 'Ghost' }, 'name occupation', function (err, person) {
      if (err) return handleError(err);

      console.log('%s %s is a %s.', person.name.first, person.name.last,
        person.occupation);
    });


Here we see that the query was executed immediately and the results passed to our callback. All callbacks in Mongoose use the pattern: `callback(error, result)`. If an error occurs executing the query, the `error` parameter will contain an error document, and `result` will be null. If the query is successful, the `error` parameter will be null, and the `result` will be populated with the results of the query.

Anywhere a callback is passed to a query in Mongoose, the callback follows the pattern `callback(error, results)`. What `results` is depends on the operation: For `findOne()` it is a [potentially-null single document][9], `find()` a [list of documents][3], `count()` [the number of documents][5], `update()` the [number of documents affected][6], etc. The [API docs for Models][10] provide more detail on what is passed to the callbacks.

Now let's look at what happens when no `callback` is passed:


    var query = Person.findOne({ 'name.last': 'Ghost' });


    query.select('name occupation');


    query.exec(function (err, person) {
      if (err) return handleError(err);

      console.log('%s %s is a %s.', person.name.first, person.name.last,
        person.occupation);
    });


In the above code, the `query` variable is of type [Query][7]. A `Query` enables you to build up a query using chaining syntax, rather than specifying a JSON object. The below 2 examples are equivalent.


    Person.
      find({
        occupation: /host/,
        'name.last': 'Ghost',
        age: { $gt: 17, $lt: 66 },
        likes: { $in: ['vaporizing', 'talking'] }
      }).
      limit(10).
      sort({ occupation: -1 }).
      select({ name: 1, occupation: 1 }).
      exec(callback);


    Person.
      find({ occupation: /host/ }).
      where('name.last').equals('Ghost').
      where('age').gt(17).lt(66).
      where('likes').in(['vaporizing', 'talking']).
      limit(10).
      sort('-occupation').
      select('name occupation').
      exec(callback);


A full list of [Query helper functions can be found in the API docs][11].

## [参考其他文件][12]

There are no joins in MongoDB but sometimes we still want references to documents in other collections. This is where [population][13] comes in. Read more about how to include documents from other collections in your query results [here][14].

## [流][15]

You can [stream][16] query results from MongoDB. You need to call the [Query#cursor()][17] function to return an instance of [QueryCursor][18].


    var cursor = Person.find({ occupation: /host/ }).cursor();
    cursor.on('data', function(doc) {

    });
    cursor.on('close', function() {

    });


[1]: http://mongoosejs.com/models.html
[2]: http://mongoosejs.com/api.html#model_Model
[3]: http://mongoosejs.com/api.html#model_Model.find
[4]: http://mongoosejs.com/api.html#model_Model.findById
[5]: http://mongoosejs.com/api.html#model_Model.count
[6]: http://mongoosejs.com/api.html#model_Model.update
[7]: http://mongoosejs.com/api.html#query-js
[8]: http://docs.mongodb.org/manual/tutorial/query-documents/
[9]: http://mongoosejs.com/api.html#model_Model.findOne
[10]: http://mongoosejs.com/api.html#model-js
[11]: http://mongoosejs.com/docs/api.html#query-js
[12]: http://mongoosejs.com#refs
[13]: http://mongoosejs.com/populate.html
[14]: http://mongoosejs.com/api.html#query_Query-populate
[15]: http://mongoosejs.com#streaming
[16]: http://nodejs.org/api/stream.html
[17]: http://mongoosejs.com/api.html#query_Query-cursor
[18]: http://mongoosejs.com/api.html#querycursor-js


