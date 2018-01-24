# Mongoose v5.0.1: Query Population

## Populate

[Source](http://mongoosejs.com/docs/populate.html "Permalink to Mongoose v5.0.1: Query Population")


MongoDB has the join-like [$lookup][1] aggregation operator in versions >= 3.2. Mongoose has a more powerful alternative called `populate()`, which lets you reference documents in other collections.

Population is the process of automatically replacing the specified paths in the document with document(s) from other collection(s). We may populate a single document, multiple documents, plain object, multiple plain objects, or all objects returned from a query. Let's look at some examples.


    var mongoose = require('mongoose');
    var Schema = mongoose.Schema;

    var personSchema = Schema({
      _id: Schema.Types.ObjectId,
      name: String,
      age: Number,
      stories: [{ type: Schema.Types.ObjectId, ref: 'Story' }]
    });

    var storySchema = Schema({
      author: { type: Schema.Types.ObjectId, ref: 'Person' },
      title: String,
      fans: [{ type: Schema.Types.ObjectId, ref: 'Person' }]
    });

    var Story = mongoose.model('Story', storySchema);
    var Person = mongoose.model('Person', personSchema);


So far we've created two [Models][2]. Our `Person` model has its `stories` field set to an array of `ObjectId`s. The `ref` option is what tells Mongoose which model to use during population, in our case the `Story` model. All `_id`s we store here must be document `_id`s from the `Story` model.

**Note**: `ObjectId`, `Number`, `String`, and `Buffer` are valid for use as refs. However, you should use `ObjectId` unless you are an advanced user and have a good reason for doing so.

### [Saving refs][3]

Saving refs to other documents works the same way you normally save properties, just assign the `_id` value:


    var author = new Person({
      _id: new mongoose.Types.ObjectId(),
      name: 'Ian Fleming',
      age: 50
    });

    author.save(function (err) {
      if (err) return handleError(err);

      var story1 = new Story({
        title: 'Casino Royale',
        author: author._id
      });

      story1.save(function (err) {
        if (err) return handleError(err);

      });
    });


### [Population][4]

So far we haven't done anything much different. We've merely created a `Person` and a `Story`. Now let's take a look at populating our story's `author` using the query builder:


    Story.
      findOne({ title: 'Casino Royale' }).
      populate('author').
      exec(function (err, story) {
        if (err) return handleError(err);
        console.log('The author is %s', story.author.name);

      });


Populated paths are no longer set to their original `_id` , their value is replaced with the mongoose document returned from the database by performing a separate query before returning the results.

Arrays of refs work the same way. Just call the [populate][5] method on the query and an array of documents will be returned _in place_ of the original `_id`s.

### [Setting Populated Fields][6]

In Mongoose >= 4.0, you can manually populate a field as well.


    Story.findOne({ title: 'Casino Royale' }, function(error, story) {
      if (error) {
        return handleError(error);
      }
      story.author = author;
      console.log(story.author.name);
    });


### [Field Selection][7]

What if we only want a few specific fields returned for the populated documents? This can be accomplished by passing the usual [field name syntax][8] as the second argument to the populate method:


    Story.
      findOne({ title: /casino royale/i }).
      populate('author', 'name').
      exec(function (err, story) {
        if (err) return handleError(err);

        console.log('The author is %s', story.author.name);


        console.log('The authors age is %s', story.author.age);

      });


### [Populating Multiple Paths][9]

What if we wanted to populate multiple paths at the same time?


    Story.
      find(...).
      populate('fans').
      populate('author').
      exec();


If you call `populate()` multiple times with the same path, only the last one will take effect.



    Story.
      find().
      populate({ path: 'fans', select: 'name' }).
      populate({ path: 'fans', select: 'email' });

    Story.find().populate({ path: 'fans', select: 'email' });


### [Query conditions and other options][10]

What if we wanted to populate our fans array based on their age, select just their names, and return at most, any 5 of them?


    Story.
      find(...).
      populate({
        path: 'fans',
        match: { age: { $gte: 21 }},

        select: 'name -_id',
        options: { limit: 5 }
      }).
      exec();


### [Refs to children][11]

We may find however, if we use the `author` object, we are unable to get a list of the stories. This is because no `story` objects were ever 'pushed' onto `author.stories`.

There are two perspectives here. First, you may want the `author` know which stories are theirs. Usually, your schema should resolve one-to-many relationships by having a parent pointer in the 'many' side. But, if you have a good reason to want an array of child pointers, you can `push()` documents onto the array as shown below.


    author.stories.push(story1);
    author.save(callback);


This allows us to perform a `find` and `populate` combo:


    Person.
      findOne({ name: 'Ian Fleming' }).
      populate('stories').
      exec(function (err, person) {
        if (err) return handleError(err);
        console.log(person);
      });


It is debatable that we really want two sets of pointers as they may get out of sync. Instead we could skip populating and directly `find()` the stories we are interested in.


    Story.
      find({ author: author._id }).
      exec(function (err, stories) {
        if (err) return handleError(err);
        console.log('The stories are an array: ', stories);
      });


The documents returned from [query population][5] become fully functional, `remove`able, `save`able documents unless the [lean][12] option is specified. Do not confuse them with [sub docs][13]. Take caution when calling its remove method because you'll be removing it from the database, not just the array.

### [Populating an existing document][14]

If we have an existing mongoose document and want to populate some of its paths, **mongoose >= 3.6** supports the [document#populate()][15] method.

### [Populating multiple existing documents][16]

If we have one or many mongoose documents or even plain objects (_like [mapReduce][17] output_), we may populate them using the [Model.populate()][18] method available in **mongoose >= 3.6**. This is what `document#populate()` and `query#populate()` use to populate documents.

### [Populating across multiple levels][19]

Say you have a user schema which keeps track of the user's friends.


    var userSchema = new Schema({
      name: String,
      friends: [{ type: ObjectId, ref: 'User' }]
    });


Populate lets you get a list of a user's friends, but what if you also wanted a user's friends of friends? Specify the `populate` option to tell mongoose to populate the `friends` array of all the user's friends:


    User.
      findOne({ name: 'Val' }).
      populate({
        path: 'friends',

        populate: { path: 'friends' }
      });


### [Populating across Databases][20]

Let's say you have a schema representing events, and a schema representing conversations. Each event has a corresponding conversation thread.


    var eventSchema = new Schema({
      name: String,


      conversation: ObjectId
    });
    var conversationSchema = new Schema({
      numMessages: Number
    });


Also, suppose that events and conversations are stored in separate MongoDB instances.


    var db1 = mongoose.createConnection('localhost:27000/db1');
    var db2 = mongoose.createConnection('localhost:27001/db2');

    var Event = db1.model('Event', eventSchema);
    var Conversation = db2.model('Conversation', conversationSchema);


In this situation, you will **not** be able to `populate()` normally. The `conversation` field will always be null, because `populate()` doesn't know which model to use. However, [you can specify the model explicitly][21].


    Event.
      find().
      populate({ path: 'conversation', model: Conversation }).
      exec(function(error, docs) {  });


This is known as a "cross-database populate," because it enables you to populate across MongoDB databases and even across MongoDB instances.

### [Dynamic References][22]

Mongoose can also populate from multiple collections at the same time. Let's say you have a user schema that has an array of "connections" - a user can be connected to either other users or an organization.


    var userSchema = new Schema({
      name: String,
      connections: [{
        kind: String,
        item: { type: ObjectId, refPath: 'connections.kind' }
      }]
    });

    var organizationSchema = new Schema({ name: String, kind: String });

    var User = mongoose.model('User', userSchema);
    var Organization = mongoose.model('Organization', organizationSchema);


The `refPath` property above means that mongoose will look at the `connections.kind` path to determine which model to use for `populate()`. In other words, the `refPath` property enables you to make the `ref` property dynamic.

















    User.
      findOne({ name: 'Axl Rose' }).
      populate('connections.item').
      exec(function(error, doc) {


      });


### [Populate Virtuals][23]

_New in 4.5.0_

So far you've only populated based on the `_id` field. However, that's sometimes not the right choice. In particular, [arrays that grow without bound are a MongoDB anti-pattern][24]. Using mongoose virtuals, you can define more sophisticated relationships between documents.


    var PersonSchema = new Schema({
      name: String,
      band: String
    });

    var BandSchema = new Schema({
      name: String
    });
    BandSchema.virtual('members', {
      ref: 'Person',
      localField: 'name',
      foreignField: 'band',


      justOne: false
    });

    var Person = mongoose.model('Person', PersonSchema);
    var Band = mongoose.model('Band', BandSchema);


    Band.find({}).populate('members').exec(function(error, bands) {

    });


Keep in mind that virtuals are _not_ included in `toJSON()` output by

default. If you want populate virtuals to show up when using functions that rely on `JSON.stringify()`, like Express' [`res.json()` function][25], set the `virtuals: true` option on your schema's `toJSON` options.


    var BandSchema = new Schema({
      name: String
    }, { toJSON: { virtuals: true } });


If you're using populate projections, make sure `foreignField` is included in the projection.


    Band.
      find({}).
      populate({ path: 'members', select: 'name' }).
      exec(function(error, bands) {

      });

    Band.
      find({}).
      populate({ path: 'members', select: 'name band' }).
      exec(function(error, bands) {

      });


### Next Up

Now that we've covered `populate()`, let's take a look at [discriminators][26].

[1]: https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/
[2]: http://mongoosejs.com/models.html
[3]: http://mongoosejs.com#saving-refs
[4]: http://mongoosejs.com#population
[5]: http://mongoosejs.com/api.html#query_Query-populate
[6]: http://mongoosejs.com#setting-populated-fields
[7]: http://mongoosejs.com#field-selection
[8]: http://mongoosejs.com/api.html#query_Query-select
[9]: http://mongoosejs.com#populating-multiple-paths
[10]: http://mongoosejs.com#query-conditions
[11]: http://mongoosejs.com#refs-to-children
[12]: http://mongoosejs.com/api.html#query_Query-lean
[13]: http://mongoosejs.com/subdocs.html
[14]: http://mongoosejs.com#populate_an_existing_mongoose_document
[15]: http://mongoosejs.com/api.html#document_Document-populate
[16]: http://mongoosejs.com#populate_multiple_documents
[17]: http://mongoosejs.com/api.html#model_Model.mapReduce
[18]: http://mongoosejs.com/api.html#model_Model.populate
[19]: http://mongoosejs.com#deep-populate
[20]: http://mongoosejs.com#cross-db-populate
[21]: http://mongoosejs.com/docs/api.html#model_Model.populate
[22]: http://mongoosejs.com#dynamic-ref
[23]: http://mongoosejs.com#populate-virtuals
[24]: https://docs.mongodb.com/manual/tutorial/model-referenced-one-to-many-relationships-between-documents/
[25]: http://expressjs.com/en/4x/api.html#res.json
[26]: http://mongoosejs.com/docs/discriminators.html


