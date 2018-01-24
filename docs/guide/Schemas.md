# 模式

[Source](http://mongoosejs.com/docs/guide.html "Permalink to Mongoose v5.0.1: Schemas")

If you haven't yet done so, please take a minute to read the [quickstart][1] to get an idea of how Mongoose works. If you are migrating from 4.x to 5.x please take a moment to read the [migration guide][2].

## 定义你的模式

Everything in Mongoose starts with a Schema. Each schema maps to a MongoDB collection and defines the shape of the documents within that collection.

```js
    var mongoose = require('mongoose');
    var Schema = mongoose.Schema;

    var blogSchema = new Schema({
    title:  String,
    author: String,
    body:   String,
    comments: [{ body: String, date: Date }],
    date: { type: Date, default: Date.now },
    hidden: Boolean,
    meta: {
        votes: Number,
        favs:  Number
    }
    });
```

If you want to add additional keys later, use the [Schema#add][4] method.

Each key in our code `blogSchema` defines a property in our documents which will be cast to its associated [SchemaType][5]. For example, we've defined a property `title` which will be cast to the [String][6] SchemaType and property `date` which will be cast to a `Date` SchemaType. Keys may also be assigned nested objects containing further key/type definitions like the `meta` property above.

The permitted SchemaTypes are:

* String
* Number
* Date
* Buffer
* Boolean
* Mixed
* ObjectId
* Array

Read more about [SchemaTypes here][7].

Schemas not only define the structure of your document and casting of properties, they also define document [instance methods][8], [static Model methods][9], [compound indexes][10], and document lifecycle hooks called [middleware][11]

## 创建一个模式

To use our schema definition, we need to convert our `blogSchema` into a [Model][13] we can work with. To do so, we pass it into `mongoose.model(modelName, schema)`:

```js
    var Blog = mongoose.model('Blog', blogSchema);
```

## 实例方法

Instances of `Models` are [documents][14]. Documents have many of their own [built-in instance methods][15]. We may also define our own custom document instance methods too.

      var animalSchema = new Schema({ name: String, type: String });

      animalSchema.methods.findSimilarTypes = function(cb) {
        return this.model('Animal').find({ type: this.type }, cb);
      };

Now all of our `animal` instances have a `findSimilarTypes` method available to them.

      var Animal = mongoose.model('Animal', animalSchema);
      var dog = new Animal({ type: 'dog' });

      dog.findSimilarTypes(function(err, dogs) {
        console.log(dogs);
      });

* Overwriting a default mongoose document method may lead to unpredictable results. See [this][16] for more details.
* Do **not** declare methods using ES6 arrow functions (`=>`). Arrow functions [explicitly prevent binding `this`][17], so your method will **not** have access to the document and the above examples will not work.

## 静态方法

Adding static methods to a `Model` is simple as well. Continuing with our `animalSchema`:

      animalSchema.statics.findByName = function(name, cb) {
        return this.find({ name: new RegExp(name, 'i') }, cb);
      };

      var Animal = mongoose.model('Animal', animalSchema);
      Animal.findByName('fido', function(err, animals) {
        console.log(animals);
      });

_Do **not** declare statics using ES6 arrow functions (`=>`). Arrow functions [explicitly prevent binding `this`][17], so the above examples will not work because of the value of `this`._

## 查询助手

You can also add query helper functions, which are like instance methods but for mongoose queries. Query helper methods let you extend mongoose's [chainable query builder API][19].

      animalSchema.query.byName = function(name) {
        return this.find({ name: new RegExp(name, 'i') });
      };

      var Animal = mongoose.model('Animal', animalSchema);
      Animal.find().byName('fido').exec(function(err, animals) {
        console.log(animals);
      });

## 索引

MongoDB supports [secondary indexes][20]. With mongoose, we define these indexes within our `Schema` [at][21] [the][22] [path][23] [level][24] or the `schema` level. Defining indexes at the schema level is necessary when creating [compound indexes][25].

      var animalSchema = new Schema({
        name: String,
        type: String,
        tags: { type: [String], index: true }
      });

      animalSchema.index({ name: 1, type: -1 });

When your application starts up, Mongoose automatically calls [`createIndex`][26] for each defined index in your schema. Mongoose will call `createIndex` for each index sequentially, and emit an 'index' event on the model when all the `createIndex` calls succeeded or when there was an error. While nice for development, it is recommended this behavior be disabled in production since index creation can cause a [significant performance impact][27]. Disable the behavior by setting the `autoIndex` option of your schema to `false`, or globally on the connection by setting the option `autoIndex` to `false`.

      mongoose.connect('mongodb://user:pass@localhost:port/database', { autoIndex: false });

      mongoose.createConnection('mongodb://user:pass@localhost:port/database', { autoIndex: false });

      animalSchema.set('autoIndex', false);

      new Schema({..}, { autoIndex: false });

Mongoose will emit an `index` event on the model when indexes are done building or an error occurred.

      animalSchema.index({ _id: 1 }, { sparse: true });
      var Animal = mongoose.model('Animal', animalSchema);

      Animal.on('index', function(error) {

        console.log(error.message);
      });

See also the [Model#ensureIndexes][28] method.

## 虚拟字段

[Virtuals][30] are document properties that you can get and set but that do not get persisted to MongoDB. The getters are useful for formatting or combining fields, while setters are useful for de-composing a single value into multiple values for storage.

      var personSchema = new Schema({
        name: {
          first: String,
          last: String
        }
      });

      var Person = mongoose.model('Person', personSchema);

      var axl = new Person({
        name: { first: 'Axl', last: 'Rose' }
      });

Suppose you want to print out the person's full name. You could do it yourself:

    console.log(axl.name.first + ' ' + axl.name.last);

But concatenating the first and last name every time can get cumbersome. And what if you want to do some extra processing on the name, like [removing diacritics?][31]. A [virtual property getter][32] lets you

define a `fullName` property that won't get persisted to MongoDB.

    personSchema.virtual('fullName').get(function () {
      return this.name.first + ' ' + this.name.last;
    });

Now, mongoose will call your getter function every time you access the `fullName` property:

    console.log(axl.fullName);

If you use `toJSON()` or `toObject()` (or use `JSON.stringify()` on a mongoose document) mongoose will _not_ include virtuals by default. Pass `{ virtuals: true }` to either [toObject()][33] or `toJSON()`.

You can also add a custom setter to your virtual that will let you set both first name and last name via the `fullName` virtual.

    personSchema.virtual('fullName').
      get(function() { return this.name.first + ' ' + this.name.last; }).
      set(function(v) {
        this.name.first = v.substr(0, v.indexOf(' '));
        this.name.last = v.substr(v.indexOf(' ') + 1);
      });

    axl.fullName = 'William Rose';

Virtual property setters are applied before other validation. So the example above would still work even if the `first` and `last` name fields were required.

Only non-virtual properties work as part of queries and for field selection. Since virtuals are not stored in MongoDB, you can't query with them.

### 别名

Aliases are a particular type of virtual where the getter and setter seamlessly get and set another property. This is handy for saving network bandwidth, so you can convert a short property name stored in the database into a longer name for code readability.

    var personSchema = new Schema({
      n: {
        type: String,

        alias: 'name'
      }
    });

    var person = new Person({ name: 'Val' });
    console.log(person);
    console.log(person.toObject({ virtuals: true }));
    console.log(person.name);

    person.name = 'Not Val';
    console.log(person);

## 选项

Schemas have a few configurable options which can be passed to the constructor or `set` directly:

    new Schema({..}, options);

    var schema = new Schema({..});
    schema.set(option, value);

Valid options:

### autoIndex

At application startup, Mongoose sends a [`createIndex` command][26] for each index declared in your `Schema`. As of Mongoose v3, indexes are created in the `background` by default. If you wish to disable the auto-creation feature and manually handle when indexes are created, set your `Schema`s `autoIndex` option to `false` and use the [ensureIndexes][28] method on your model.

    var schema = new Schema({..}, { autoIndex: false });
    var Clock = mongoose.model('Clock', schema);
    Clock.ensureIndexes(callback);

### bufferCommands

By default, mongoose buffers commands when the connection goes down until the driver manages to reconnect. To disable buffering, set `bufferCommands` to false.

    var schema = new Schema({..}, { bufferCommands: false });

The schema `bufferCommands` option overrides the global `bufferCommands` option.

    mongoose.set('bufferCommands', true);

    var schema = new Schema({..}, { bufferCommands: false });

### capped

Mongoose supports MongoDBs [capped][39] collections. To specify the underlying MongoDB collection be `capped`, set the `capped` option to the maximum size of the collection in [bytes][40].

    new Schema({..}, { capped: 1024 });

The `capped` option may also be set to an object if you want to pass additional options like [max][41] or [autoIndexId][42]. In this case you must explicitly pass the `size` option, which is required.

    new Schema({..}, { capped: { size: 1024, max: 1000, autoIndexId: true } });

### collection

Mongoose by default produces a collection name by passing the model name to the [utils.toCollectionName][44] method. This method pluralizes the name. Set this option if you need a different name for your collection.

    var dataSchema = new Schema({..}, { collection: 'data' });

### id

Mongoose assigns each of your schemas an `id` virtual getter by default which returns the documents `_id` field cast to a string, or in the case of ObjectIds, its hexString. If you don't want an `id` getter added to your schema, you may disable it passing this option at schema construction time.

    var schema = new Schema({ name: String });
    var Page = mongoose.model('Page', schema);
    var p = new Page({ name: 'mongodb.org' });
    console.log(p.id);

    var schema = new Schema({ name: String }, { id: false });
    var Page = mongoose.model('Page', schema);
    var p = new Page({ name: 'mongodb.org' });
    console.log(p.id);

### _id

Mongoose assigns each of your schemas an `_id` field by default if one is not passed into the [Schema][47] constructor. The type assigned is an [ObjectId][48] to coincide with MongoDB's default behavior. If you don't want an `_id` added to your schema at all, you may disable it using this option.

You can **only** use this option on sub-documents. Mongoose can't save a document without knowing its id, so you will get an error if you try to save a document without an `_id`.

    var schema = new Schema({ name: String });
    var Page = mongoose.model('Page', schema);
    var p = new Page({ name: 'mongodb.org' });
    console.log(p);

    var childSchema = new Schema({ name: String }, { _id: false });
    var parentSchema = new Schema({ children: [childSchema] });

    var Model = mongoose.model('Model', parentSchema);

    Model.create({ children: [{ name: 'Luke' }] }, function(error, doc) {

    });

### minimize

Mongoose will, by default, "minimize" schemas by removing empty objects.

    var schema = new Schema({ name: String, inventory: {} });
    var Character = mongoose.model('Character', schema);

    var frodo = new Character({ name: 'Frodo', inventory: { ringOfPower: 1 }});
    Character.findOne({ name: 'Frodo' }, function(err, character) {
      console.log(character);
    });

    var sam = new Character({ name: 'Sam', inventory: {}});
    Character.findOne({ name: 'Sam' }, function(err, character) {
      console.log(character);
    });

This behavior can be overridden by setting `minimize` option to `false`. It will then store empty objects.

    var schema = new Schema({ name: String, inventory: {} }, { minimize: false });
    var Character = mongoose.model('Character', schema);

    var sam = new Character({ name: 'Sam', inventory: {}});
    Character.findOne({ name: 'Sam' }, function(err, character) {
      console.log(character);
    });

### read

Allows setting [query#read][51] options at the schema level, providing us a way to apply default [ReadPreferences][52] to all queries derived from a model.

    var schema = new Schema({..}, { read: 'primary' });
    var schema = new Schema({..}, { read: 'primaryPreferred' });
    var schema = new Schema({..}, { read: 'secondary' });
    var schema = new Schema({..}, { read: 'secondaryPreferred' });
    var schema = new Schema({..}, { read: 'nearest' });

The alias of each pref is also permitted so instead of having to type out 'secondaryPreferred' and getting the spelling wrong, we can simply pass 'sp'.

The read option also allows us to specify _tag sets_. These tell the [driver][53] from which members of the replica-set it should attempt to read. Read more about tag sets [here][54] and [here][55].

_NOTE: you may also specify the driver read pref [strategy][56] option when connecting:_

    var options = { replset: { strategy: 'ping' }};
    mongoose.connect(uri, options);

    var schema = new Schema({..}, { read: ['nearest', { disk: 'ssd' }] });
    mongoose.model('JellyBean', schema);

### shardKey

The `shardKey` option is used when we have a [sharded MongoDB architecture][58]. Each sharded collection is given a shard key which must be present in all insert/update operations. We just need to set this schema option to the same shard key and we'll be all set.

    new Schema({ .. }, { shardKey: { tag: 1, name: 1 }})

_Note that Mongoose does not send the `shardcollection` command for you. You must configure your shards yourself._

### strict

The strict option, (enabled by default), ensures that values passed to our model constructor that were not specified in our schema do not get saved to the db.

    var thingSchema = new Schema({..})
    var Thing = mongoose.model('Thing', thingSchema);
    var thing = new Thing({ iAmNotInTheSchema: true });
    thing.save();

    var thingSchema = new Schema({..}, { strict: false });
    var thing = new Thing({ iAmNotInTheSchema: true });
    thing.save();

This also affects the use of `doc.set()` to set a property value.

    var thingSchema = new Schema({..})
    var Thing = mongoose.model('Thing', thingSchema);
    var thing = new Thing;
    thing.set('iAmNotInTheSchema', true);
    thing.save();

This value can be overridden at the model instance level by passing a second boolean argument:

    var Thing = mongoose.model('Thing');
    var thing = new Thing(doc, true);
    var thing = new Thing(doc, false);

The `strict` option may also be set to `"throw"` which will cause errors to be produced instead of dropping the bad data.

_NOTE: Any key/val set on the instance that does not exist in your schema is always ignored, regardless of schema option._

    var thingSchema = new Schema({..})
    var Thing = mongoose.model('Thing', thingSchema);
    var thing = new Thing;
    thing.iAmNotInTheSchema = true;
    thing.save();

### toJSON

Exactly the same as the [toObject][60] option but only applies when the documents `toJSON` method is called.

    var schema = new Schema({ name: String });
    schema.path('name').get(function (v) {
      return v + ' is my name';
    });
    schema.set('toJSON', { getters: true, virtuals: false });
    var M = mongoose.model('Person', schema);
    var m = new M({ name: 'Max Headroom' });
    console.log(m.toObject());
    console.log(m.toJSON());

    console.log(JSON.stringify(m));

To see all available `toJSON/toObject` options, read [this][61].

### toObject

Documents have a [toObject][61] method which converts the mongoose document into a plain javascript object. This method accepts a few options. Instead of applying these options on a per-document basis we may declare the options here and have it applied to all of this schemas documents by default.

To have all virtuals show up in your `console.log` output, set the `toObject` option to `{ getters: true }`:

    var schema = new Schema({ name: String });
    schema.path('name').get(function (v) {
      return v + ' is my name';
    });
    schema.set('toObject', { getters: true });
    var M = mongoose.model('Person', schema);
    var m = new M({ name: 'Max Headroom' });
    console.log(m);

To see all available `toObject` options, read [this][61].

### typeKey

By default, if you have an object with key 'type' in your schema, mongoose will interpret it as a type declaration.

    var schema = new Schema({ loc: { type: String, coordinates: [Number] } });

However, for applications like [geoJSON][63], the 'type' property is important. If you want to control which key mongoose uses to find type declarations, set the 'typeKey' schema option.

    var schema = new Schema({

      loc: { type: String, coordinates: [Number] },

      name: { $type: String }
    }, { typeKey: '$type' });

### validateBeforeSave

By default, documents are automatically validated before they are saved to the database. This is to prevent saving an invalid document. If you want to handle validation manually, and be able to save objects which don't pass validation, you can set `validateBeforeSave` to false.

    var schema = new Schema({ name: String });
    schema.set('validateBeforeSave', false);
    schema.path('name').validate(function (value) {
        return v != null;
    });
    var M = mongoose.model('Person', schema);
    var m = new M({ name: null });
    m.validate(function(err) {
        console.log(err);
    });
    m.save();

### versionKey

The `versionKey` is a property set on each document when first created by Mongoose. This keys value contains the internal [revision][66] of the document. The `versionKey` option is a string that represents the path to use for versioning. The default is `__v`. If this conflicts with your application you can configure as such:

    var schema = new Schema({ name: 'string' });
    var Thing = mongoose.model('Thing', schema);
    var thing = new Thing({ name: 'mongoose v3' });
    thing.save();

    new Schema({..}, { versionKey: '_somethingElse' })
    var Thing = mongoose.model('Thing', schema);
    var thing = new Thing({ name: 'mongoose v3' });
    thing.save();

Document versioning can also be disabled by setting the `versionKey` to `false`. _DO NOT disable versioning unless you [know what you are doing][66]._

    new Schema({..}, { versionKey: false });
    var Thing = mongoose.model('Thing', schema);
    var thing = new Thing({ name: 'no versioning please' });
    thing.save();

### collation

Sets a default [collation][68] for every query and aggregation. [Here's a beginner-friendly overview of collations][69].

    var schema = new Schema({
      name: String
    }, { collation: { locale: 'en_US', strength: 1 } });

    var MyModel = db.model('MyModel', schema);

    MyModel.create([{ name: 'val' }, { name: 'Val' }]).
      then(function() {
        return MyModel.find({ name: 'val' });
      }).
      then(function(docs) {

      });

### skipVersioning

`skipVersioning` allows excluding paths from versioning (i.e., the internal revision will not be incremented even if these paths are updated). DO NOT do this unless you know what you're doing. For sub-documents, include this on the parent document using the fully qualified path.

    new Schema({..}, { skipVersioning: { dontVersionMe: true } });
    thing.dontVersionMe.push('hey');
    thing.save();

### timestamps

If set `timestamps`, mongoose assigns `createdAt` and `updatedAt` fields to your schema, the type assigned is [Date][72].

By default, the name of two fields are `createdAt` and `updatedAt`, customize the field name by setting `timestamps.createdAt` and `timestamps.updatedAt`.

    var thingSchema = new Schema({..}, { timestamps: { createdAt: 'created_at' } });
    var Thing = mongoose.model('Thing', thingSchema);
    var thing = new Thing();
    thing.save();

### useNestedStrict

In mongoose 4, `update()` and `findOneAndUpdate()` only check the top-level schema's strict mode setting.

    var childSchema = new Schema({}, { strict: false });
    var parentSchema = new Schema({ child: childSchema }, { strict: 'throw' });
    var Parent = mongoose.model('Parent', parentSchema);
    Parent.update({}, { 'child.name': 'Luke Skywalker' }, function(error) {

    });

    var update = { 'child.name': 'Luke Skywalker' };
    var opts = { strict: false };
    Parent.update({}, update, opts, function(error) {

    });

If you set `useNestedStrict` to true, mongoose will use the child schema's `strict` option for casting updates.

    var childSchema = new Schema({}, { strict: false });
    var parentSchema = new Schema({ child: childSchema },
      { strict: 'throw', useNestedStrict: true });
    var Parent = mongoose.model('Parent', parentSchema);
    Parent.update({}, { 'child.name': 'Luke Skywalker' }, function(error) {

    });

## 可插拔

Schema也是[可插拔][75]，它允许我们将可重用的特性打包成可与社区共享或只在您的项目之间共享的插件。

## 接下来

现在我们已经介绍了模式，我们来看看[模式类型][76].

[1]: http://mongoosejs.com/index.html
[2]: https://github.com/Automattic/mongoose/blob/master/migrating_to_5.md
[4]: http://mongoosejs.com/api.html#schema_Schema-add
[5]: http://mongoosejs.com/api.html#schematype_SchemaType
[6]: http://mongoosejs.com/api.html#schema-string-js
[7]: http://mongoosejs.com/schematypes.html
[11]: http://mongoosejs.com/middleware.html
[13]: http://mongoosejs.com/models.html
[14]: http://mongoosejs.com/documents.html
[15]: http://mongoosejs.com/api.html#document-js
[16]: http://mongoosejs.com/api.html#schema_Schema.reserved
[17]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#No_binding_of_this
[18]: http://mongoosejs.com#query-helpers
[19]: http://mongoosejs.com/queries.html
[20]: http://docs.mongodb.org/manual/indexes/
[21]: http://mongoosejs.com/api.html#schematype_SchemaType-index
[22]: http://mongoosejs.com/api.html#schematype_SchemaType-unique
[23]: http://mongoosejs.com/api.html#schematype_SchemaType-sparse
[24]: http://mongoosejs.com/api.html#schema_date_SchemaDate-expires
[25]: http://www.mongodb.org/display/DOCS/Indexes#Indexes-CompoundKeys
[26]: https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex
[27]: http://docs.mongodb.org/manual/core/indexes/#index-creation-operations
[28]: http://mongoosejs.com/api.html#model_Model.ensureIndexes
[30]: http://mongoosejs.com/api.html#schema_Schema-virtual
[31]: https://www.npmjs.com/package/diacritics
[32]: http://mongoosejs.com/api.html#virtualtype_VirtualType-get
[33]: http://mongoosejs.com/api.html#document_Document-toObject
[39]: http://www.mongodb.org/display/DOCS/Capped+Collections
[40]: http://www.mongodb.org/display/DOCS/Capped+Collections#CappedCollections-size.
[41]: http://www.mongodb.org/display/DOCS/Capped+Collections#CappedCollections-max
[42]: http://www.mongodb.org/display/DOCS/Capped+Collections#CappedCollections-autoIndexId
[44]: http://mongoosejs.com/api.html#utils_exports.toCollectionName
[47]: http://mongoosejs.com/docs/api.html#schema-js
[48]: http://mongoosejs.com/docs/api.html#schema_Schema.Types
[51]: http://mongoosejs.com/docs/api.html#query_Query-read
[52]: http://docs.mongodb.org/manual/applications/replication/#replica-set-read-preference
[53]: https://github.com/mongodb/node-mongodb-native/
[54]: http://docs.mongodb.org/manual/applications/replication/#tag-sets
[55]: http://mongodb.github.com/node-mongodb-native/driver-articles/anintroductionto1_1and2_2.html#read-preferences
[56]: http://mongodb.github.com/node-mongodb-native/api-generated/replset.html?highlight=strategy
[58]: http://www.mongodb.org/display/DOCS/Sharding+Introduction
[61]: http://mongoosejs.com/docs/api.html#document_Document-toObject
[63]: http://docs.mongodb.org/manual/reference/geojson/
[66]: http://aaronheckmann.tumblr.com/post/48943525537/mongoose-v3-part-1-versioning
[68]: https://docs.mongodb.com/manual/reference/collation/
[69]: http://thecodebarbarian.com/a-nodejs-perspective-on-mongodb-34-collations
[72]: http://mongoosejs.com/docs/api.html#schema-date-js
[75]: http://mongoosejs.com/plugins.html
[76]: http://mongoosejs.com/docs/schematypes.html

