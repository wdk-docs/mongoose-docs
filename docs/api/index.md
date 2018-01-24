# Mongoose v5.0.1: API docs

[Source](http://mongoosejs.com/docs/api.html "Permalink to Mongoose v5.0.1: API docs")

* * *
* * *

### [Schema()][1]

##### Parameters

Schema constructor.

#### Example:


    var child = new Schema({ name: String });
    var schema = new Schema({ name: String, age: Number, children: [child] });
    var Tree = mongoose.model('Tree', schema);


    new Schema({ name: String }, { _id: false, autoIndex: false })

#### Options:

#### Note:

_When nesting schemas, (`children` in the example above), always declare the child schema first before passing it into its parent._

* * *

### [Schema.prototype.childSchemas][2]

Array of child schemas (from document arrays and single nested subdocs) and their corresponding compiled models. Each element of the array is an object with 2 properties: `schema` and `model`.

This property is typically only useful for plugin authors and advanced users. You do not need to interact with this property at all to use mongoose.

* * *

### [Schema.prototype.obj][3]

The original object passed to the schema constructor

#### Example:


    var schema = new Schema({ a: String }).add({ b: String });
    schema.obj;

* * *

### [Schema.prototype.clone()][4]

##### Returns:

* «Schema» the cloned schema

Returns a deep copy of the schema

* * *

### [Schema.prototype.add()][5]

##### Parameters

Adds key path / schema type pairs to this schema.

#### Example:


    var ToySchema = new Schema;
    ToySchema.add({ name: 'string', color: 'string', price: 'number' });

* * *

### [Schema.reserved][6]

Reserved document keys.

Keys in this object are names that are rejected in schema declarations b/c they conflict with mongoose functionality. Using these key name will throw an error.


    on, emit, _events, db, get, set, init, isNew, errors, schema, options, modelName, collection, _pres, _posts, toObject

_NOTE:_ Use of these terms as method names is permitted, but play at your own risk, as they may be existing mongoose document methods you are stomping on.


    var schema = new Schema(..);
     schema.methods.init = function () {}

* * *

### [Schema.prototype.path()][7]

##### Parameters

Gets/sets schema paths.

Sets a path (if arity 2) Gets a path (if arity 1)

#### Example


    schema.path('name')
    schema.path('name', Number)

* * *

### [Schema.prototype.eachPath()][8]

##### Parameters

* fn «Function» callback function

##### Returns:

Iterates the schemas paths similar to Array#forEach.

The callback is passed the pathname and schemaType as arguments on each iteration.

* * *

### [Schema.prototype.requiredPaths()][9]

##### Parameters

* invalidate «Boolean» refresh the cache

##### Returns:

Returns an Array of path strings that are required by this schema.

* * *

### [Schema.prototype.pathType()][10]

##### Parameters

##### Returns:

Returns the pathType of `path` for this schema.

Given a path, returns whether it is a real, virtual, nested, or ad-hoc/undefined path.

* * *

### [Schema.prototype.queue()][11]

##### Parameters

* args «Array» arguments to pass to the method

Adds a method call to the queue.

* * *

### [Schema.prototype.pre()][12]

##### Parameters

Defines a pre hook for the document.

#### Example


    var toySchema = new Schema(..);

    toySchema.pre('save', function (next) {
      if (!this.created) this.created = new Date;
      next();
    })

    toySchema.pre('validate', function (next) {
      if (this.name !== 'Woody') this.name = 'Woody';
      next();
    })

* * *

### [Schema.prototype.post()][13]

##### Parameters

Defines a post hook for the document


    var schema = new Schema(..);
    schema.post('save', function (doc) {
      console.log('this fired after a document was saved');
    });

    schema.post('find', function(docs) {
      console.log('this fired after you run a find query');
    });

    var Model = mongoose.model('Model', schema);

    var m = new Model(..);
    m.save(function(err) {
      console.log('this fires after the `post` hook');
    });

    m.find(function(err, docs) {
      console.log('this fires after the post find hook');
    });

* * *

### [Schema.prototype.plugin()][14]

##### Parameters

Registers a plugin for this schema.

* * *

### [Schema.prototype.method()][15]

##### Parameters

Adds an instance method to documents constructed from Models compiled from this schema.

#### Example


    var schema = kittySchema = new Schema(..);

    schema.method('meow', function () {
      console.log('meeeeeoooooooooooow');
    })

    var Kitty = mongoose.model('Kitty', schema);

    var fizz = new Kitty;
    fizz.meow();

If a hash of name/fn pairs is passed as the only argument, each name/fn pair will be added as methods.


    schema.method({
        purr: function () {}
      , scratch: function () {}
    });


    fizz.purr();
    fizz.scratch();

* * *

### [Schema.prototype.static()][16]

##### Parameters

Adds static "class" methods to Models compiled from this schema.

#### Example


    var schema = new Schema(..);
    schema.static('findByName', function (name, callback) {
      return this.find({ name: name }, callback);
    });

    var Drink = mongoose.model('Drink', schema);
    Drink.findByName('sanpellegrino', function (err, drinks) {

    });

If a hash of name/fn pairs is passed as the only argument, each name/fn pair will be added as statics.

* * *

### [Schema.prototype.index()][17]

##### Parameters

* [options.expires=null] «String» Mongoose-specific syntactic sugar, uses [ms][18] to convert `expires` option into seconds for the `expireAfterSeconds` in the above link.

Defines an index (most likely compound) for this schema.

#### Example


    schema.index({ first: 1, last: -1 })

* * *

### [Schema.prototype.set()][19]

##### Parameters

* [value] «Object» if not passed, the current option value is returned

Sets/gets a schema option.

#### Example


    schema.set('strict');
    schema.set('strict', false);
    schema.set('strict');

* * *

### [Schema.prototype.get()][20]

##### Parameters

* key «String» option name
* * *

### [indexTypes][21]

* * *

### [Schema.prototype.indexes()][22]

Compiles indexes from fields and schema-level indexes

* * *

### [Schema.prototype.virtual()][23]

##### Parameters

##### Returns:

Creates a virtual type with the given name.

* * *

### [Schema.prototype.virtualpath()][24]

##### Parameters

##### Returns:

Returns the virtual type with the given `name`.

* * *

### [Schema.prototype.remove()][25]

##### Parameters

Removes the given `path` (or [`paths`]).

* * *

### [Schema.prototype.loadClass()][26]

##### Parameters

Loads an ES6 class into a schema. Maps setters + getters, static methods, and instance methods to schema virtuals, statics, and methods.
* * *

### [Schema.Types][27]

The various built-in Mongoose Schema Types.

#### Example:


    var mongoose = require('mongoose');
    var ObjectId = mongoose.Schema.Types.ObjectId;

#### Types:

Using this exposed access to the `Mixed` SchemaType, we can use them in our schema.


    var Mixed = mongoose.Schema.Types.Mixed;
    new mongoose.Schema({ _user: Mixed })

* * *
* * *

### [Connection()][28]

##### Parameters

* base «Mongoose» a mongoose instance

Connection constructor

For practical reasons, a Connection equals a Db.

* * *

### [Connection.prototype.readyState][29]

Connection ready state

* 0 = disconnected
* 1 = connected
* 2 = connecting
* 3 = disconnecting

Each state change emits its associated event name.

#### Example


    conn.on('connected', callback);
    conn.on('disconnected', callback);

* * *

### [Connection.prototype.collections][30]

A hash of the collections associated with this connection

* * *

### [Connection.prototype.db][31]

The mongodb.Db instance, set when the connection is opened

* * *

### [Connection.prototype.config][32]

A hash of the global options that are associated with this connection

* * *

### [Connection.prototype.createCollection()][33]

##### Parameters

##### Returns:

* * *

### [Connection.prototype.dropCollection()][34]

##### Parameters

##### Returns:

Helper for `dropCollection()`. Will delete the given collection, including all documents and indexes.

* * *

### [Connection.prototype.dropDatabase()][35]

##### Parameters

##### Returns:

Helper for `dropDatabase()`. Deletes the given database, including all collections, documents, and indexes.

* * *

### [Connection.prototype.close()][36]

##### Parameters

* [callback] «Function» optional

##### Returns:

* * *

### [Connection.prototype.collection()][37]

##### Parameters

* [options] «Object» optional collection options

##### Returns:

* «Collection» collection instance

Retrieves a collection, creating it if not cached.

Not typically needed by applications. Just talk to your collection through your model.

* * *

### [Connection.prototype.model()][38]

##### Parameters

* [collection] «String» name of mongodb collection (optional) if not given it will be induced from model name

##### Returns:

* «Model» The compiled model

Defines or retrieves a model.


    var mongoose = require('mongoose');
    var db = mongoose.createConnection(..);
    db.model('Venue', new Schema(..));
    var Ticket = db.model('Ticket', new Schema(..));
    var Venue = db.model('Venue');

_When no `collection` argument is passed, Mongoose produces a collection name by passing the model `name` to the [utils.toCollectionName][39] method. This method pluralizes the name. If you don't like this behavior, either pass a collection name or set your schemas collection name option._

#### Example:


    var schema = new Schema({ name: String }, { collection: 'actor' });



    schema.set('collection', 'actor');



    var collectionName = 'actor'
    var M = conn.model('Actor', schema, collectionName)

* * *

### [Connection.prototype.modelNames()][40]

##### Returns:

Returns an array of model names created on this connection.

* * *
* * *

### [Document.prototype.schema][41]

* * *

### [Document.prototype.isNew][42]

Boolean flag specifying if the document is new.

* * *

### [Document.prototype.id][43]

The string version of this documents _id.

#### Note:

This getter exists on all documents by default. The getter can be disabled by setting the `id` [option][44] of its `Schema` to false at construction time.


    new Schema({ name: String }, { id: false });

* * *

### [Document.prototype.errors][45]

Hash containing current validation errors.

* * *

### [Document.prototype.init()][46]

##### Parameters

* doc «Object» document returned by mongo

Initializes the document without setters or marking anything modified.

Called internally after a document is returned from mongodb.

* * *

### [Document.prototype.update()][47]

##### Parameters

##### Returns:

Sends an update command with this document `_id` as the query selector.

#### Example:


    weirdCar.update({$inc: {wheels:1}}, { w: 1 }, callback);

#### Valid options:

* * *

### Document.prototype.$set()

##### Parameters

* [options] «Object» optionally specify options that modify the behavior of the set

Alias for `set()`, used internally to avoid conflicts

* * *

### [Document.prototype.set()][48]

##### Parameters

* [options] «Object» optionally specify options that modify the behavior of the set

Sets the value of a path, or many paths.

#### Example:


    doc.set(path, value)


    doc.set({
        path  : value
      , path2 : {
           path  : value
        }
    })


    doc.set(path, value, Number)


    doc.set(path, value, String)


    doc.set(path, value, { strict: false });

* * *

### [Document.prototype.get()][49]

##### Parameters

* [type] «Schema,String,Number,Buffer,*» optionally specify a type for on-the-fly attributes

Returns the value of a path.

#### Example


    doc.get('age')


    doc.get('age', String)

* * *

### [Document.prototype.markModified()][50]

##### Parameters

* [scope] «Document» the scope to run validators with

Marks the path as having pending changes to write to the db.

_Very helpful when using [Mixed][51] types._

#### Example:


    doc.mixed.type = 'changed';
    doc.markModified('mixed.type');
    doc.save()

* * *

### [Document.prototype.unmarkModified()][52]

##### Parameters

* path «String» the path to unmark modified

Clears the modified state on the specified path.

#### Example:


    doc.foo = 'bar';
    doc.unmarkModified('foo');
    doc.save()

* * *

### Document.prototype.$ignore()

##### Parameters

* path «String» the path to ignore

Don't run validation on this path or persist changes to this path.

#### Example:


    doc.foo = null;
    doc.$ignore('foo');
    doc.save()

* * *

### [Document.prototype.modifiedPaths()][53]

##### Returns:

Returns the list of paths that have been modified.

* * *

### [Document.prototype.isModified()][54]

##### Parameters

* [path] «String» optional

##### Returns:

Returns true if this document was modified, else false.

If `path` is given, checks if a path or any full path containing `path` as part of its path chain has been modified.

#### Example


    doc.set('documents.0.title', 'changed');
    doc.isModified()
    doc.isModified('documents')
    doc.isModified('documents.0.title')
    doc.isModified('documents otherProp')
    doc.isDirectModified('documents')

* * *

### Document.prototype.$isDefault()

##### Parameters

##### Returns:

Checks if a path is set to its default.

#### Example


    MyModel = mongoose.model('test', { name: { type: String, default: 'Val '} });
    var m = new MyModel();
    m.$isDefault('name');

* * *

### Document.prototype.$isDeleted()

##### Parameters

* [val] «Boolean» optional, overrides whether mongoose thinks the doc is deleted

##### Returns:

* «Boolean» whether mongoose thinks this doc is deleted.

Getter/setter, determines whether the document was removed or not.

#### Example:


    product.remove(function (err, product) {
      product.isDeleted();
      product.remove();

      product.isDeleted(false);
      product.isDeleted();
      product.remove();
    })

* * *

### [Document.prototype.isDirectModified()][55]

##### Parameters

##### Returns:

Returns true if `path` was directly set and modified, else false.

#### Example


    doc.set('documents.0.title', 'changed');
    doc.isDirectModified('documents.0.title')
    doc.isDirectModified('documents')

* * *

### [Document.prototype.isInit()][56]

##### Parameters

##### Returns:

Checks if `path` was initialized.

* * *

### [Document.prototype.isSelected()][57]

##### Parameters

##### Returns:

Checks if `path` was selected in the source query which initialized this document.

#### Example


    Thing.findOne().select('name').exec(function (err, doc) {
       doc.isSelected('name')
       doc.isSelected('age')
    })

* * *

### [Document.prototype.isDirectSelected()][58]

##### Parameters

##### Returns:

Checks if `path` was explicitly selected. If no projection, always returns true.

#### Example


    Thing.findOne().select('nested.name').exec(function (err, doc) {
       doc.isDirectSelected('nested.name')
       doc.isDirectSelected('nested.otherName')
       doc.isDirectSelected('nested')
    })

* * *

### [Document.prototype.validate()][59]

##### Parameters

* callback «Function» optional callback called after validation completes, passing an error if one occurred

##### Returns:

Executes registered validation rules for this document.

#### Note:

This method is called `pre` save and if a validation rule is violated, [save][60] is aborted and the error is returned to your `callback`.

#### Example:


    doc.validate(function (err) {
      if (err) handleError(err);
      else
    });

* * *

### [Document.prototype.validateSync()][61]

##### Parameters

* pathsToValidate «Array,string» only validate the given paths

##### Returns:

* «MongooseError,undefined» MongooseError if there are errors during validation, or undefined if there is no error.

Executes registered validation rules (skipping asynchronous validators) for this document.

#### Note:

This method is useful if you need synchronous validation.

#### Example:


    var err = doc.validateSync();
    if ( err ){
      handleError( err );
    } else {

    }

* * *

### [Document.prototype.invalidate()][62]

##### Parameters

* [kind] «String» optional `kind` property for the error

##### Returns:

* «ValidationError» the current ValidationError, with all currently invalidated paths

Marks a path as invalid, causing validation to fail.

The `errorMsg` argument will become the message of the `ValidationError`.

The `value` argument (if passed) will be available through the `ValidationError.value` property.


    doc.invalidate('size', 'must be less than 20', 14);

    doc.validate(function (err) {
      console.log(err)

      { message: 'Validation failed',
        name: 'ValidationError',
        errors:
         { size:
            { message: 'must be less than 20',
              name: 'ValidatorError',
              path: 'size',
              type: 'user defined',
              value: 14 } } }
    })

* * *

### Document.prototype.$markValid()

##### Parameters

* path «String» the field to mark as valid

Marks a path as valid, removing existing validation errors.

* * *

### [Document.prototype.save()][63]

##### Parameters

* [fn] «Function» optional callback

##### Returns:

Saves this document.

#### Example:


    product.sold = Date.now();
    product.save(function (err, product, numAffected) {
      if (err) ..
    })

The callback will receive three parameters

1. `err` if an error occurred
2. `product` which is the saved `product`
3. `numAffected` will be 1 when the document was successfully persisted to MongoDB, otherwise 0. Unless you tweak mongoose's internals, you don't need to worry about checking this parameter for errors - checking `err` is sufficient to make sure your document was properly saved.

As an extra measure of flow control, save will return a Promise.

#### Example:


    product.save().then(function(product) {
       ...
    });

* * *

### [Document.prototype.toObject()][64]

##### Parameters

##### Returns:

Converts this document into a plain javascript object, ready for storage in MongoDB.

Buffers are converted to instances of [mongodb.Binary][65] for proper storage.

#### Options:

* `getters` apply all getters (path and virtual getters)
* `virtuals` apply virtual getters (can override `getters` option)
* `minimize` remove empty objects (defaults to true)
* `transform` a transform function to apply to the resulting document before returning
* `depopulate` depopulate any populated paths, replacing them with their original refs (defaults to false)
* `versionKey` whether to include the version key (defaults to true)

#### Getters/Virtuals

Example of only applying path getters


    doc.toObject({ getters: true, virtuals: false })

Example of only applying virtual getters


    doc.toObject({ virtuals: true })

Example of applying both path and virtual getters


    doc.toObject({ getters: true })

To apply these options to every document of your schema by default, set your [schemas][1] `toObject` option to the same argument.


    schema.set('toObject', { virtuals: true })

#### Transform

We may need to perform a transformation of the resulting object based on some criteria, say to remove some sensitive information or return a custom object. In this case we set the optional `transform` function.

Transform functions receive three arguments


    function (doc, ret, options) {}

* `doc` The mongoose document which is being converted
* `ret` The plain object representation which has been converted
* `options` The options in use (either schema options or the options passed inline)

#### Example


    if (!schema.options.toObject) schema.options.toObject = {};
    schema.options.toObject.transform = function (doc, ret, options) {

      delete ret._id;
      return ret;
    }


    doc.toObject();


    doc.toObject();

With transformations we can do a lot more than remove properties. We can even return completely new customized objects:


    if (!schema.options.toObject) schema.options.toObject = {};
    schema.options.toObject.transform = function (doc, ret, options) {
      return { movie: ret.name }
    }


    doc.toObject();


    doc.toObject();

_Note: if a transform function returns `undefined`, the return value will be ignored._

Transformations may also be applied inline, overridding any transform set in the options:


    function xform (doc, ret, options) {
      return { inline: ret.name, custom: true }
    }


    doc.toObject({ transform: xform });

If you want to skip transformations, use `transform: false`:


    if (!schema.options.toObject) schema.options.toObject = {};
    schema.options.toObject.hide = '_id';
    schema.options.toObject.transform = function (doc, ret, options) {
      if (options.hide) {
        options.hide.split(' ').forEach(function (prop) {
          delete ret[prop];
        });
      }
      return ret;
    }

    var doc = new Doc({ _id: 'anId', secret: 47, name: 'Wreck-it Ralph' });
    doc.toObject();
    doc.toObject({ hide: 'secret _id', transform: false });
    doc.toObject({ hide: 'secret _id', transform: true });

Transforms are applied _only to the document and are not applied to sub-documents_.

Transforms, like all of these options, are also available for `toJSON`.

See [schema options][66] for some more details.

_During save, no custom options are applied to the document before being sent to the database._

* * *

### [Document.prototype.toJSON()][67]

##### Parameters

##### Returns:

The return value of this method is used in calls to JSON.stringify(doc).

This method accepts the same options as [Document#toObject][64]. To apply the options to every document of your schema by default, set your [schemas][1] `toJSON` option to the same argument.


    schema.set('toJSON', { virtuals: true })

See [schema options][68] for details.

* * *

### [Document.prototype.inspect()][69]

* * *

### [Document.prototype.toString()][70]

* * *

### [Document.prototype.equals()][71]

##### Parameters

* doc «Document» a document to compare

##### Returns:

Returns true if the Document stores the same data as doc.

Documents are considered equal when they have matching `_id`s, unless neither document has an `_id`, in which case this function falls back to using `deepEqual()`.

* * *

### [Document.prototype.populate()][72]

##### Parameters

* [callback] «Function» When passed, population is invoked

##### Returns:

Populates document references, executing the `callback` when complete. If you want to use promises instead, use this function with [`execPopulate()`][73]

#### Example:


    doc
    .populate('company')
    .populate({
      path: 'notes',
      match: /airline/,
      select: 'text',
      model: 'modelName'
      options: opts
    }, function (err, user) {
      assert(doc._id === user._id)
    })


    doc.populate(path)
    doc.populate(options);
    doc.populate(path, callback)
    doc.populate(options, callback);
    doc.populate(callback);
    doc.populate(options).execPopulate()

#### NOTE:

Population does not occur unless a `callback` is passed _or_ you explicitly call `execPopulate()`. Passing the same path a second time will overwrite the previous path options. See [Model.populate()][74] for explaination of options.

* * *

### [Document.prototype.execPopulate()][73]

##### Returns:

* «Promise» promise that resolves to the document when population is done

Explicitly executes population and returns a promise. Useful for ES2015 integration.

#### Example:


    var promise = doc.
      populate('company').
      populate({
        path: 'notes',
        match: /airline/,
        select: 'text',
        model: 'modelName'
        options: opts
      }).
      execPopulate();


    doc.execPopulate().then(resolve, reject);

* * *

### [Document.prototype.populated()][75]

##### Parameters

##### Returns:

* «Array,ObjectId,Number,Buffer,String,undefined»

Gets _id(s) used during population of the given `path`.

#### Example:


    Model.findOne().populate('author').exec(function (err, doc) {
      console.log(doc.author.name)
      console.log(doc.populated('author'))
    })

If the path was not populated, undefined is returned.

* * *

### [Document.prototype.depopulate()][76]

##### Parameters

##### Returns:

Takes a populated field and returns it to its unpopulated state.

#### Example:


    Model.findOne().populate('author').exec(function (err, doc) {
      console.log(doc.author.name);
      console.log(doc.depopulate('author'));
      console.log(doc.author);
    })

If the path was not populated, this is a no-op.

* * *
* * *

### [Model()][77]

##### Parameters

* doc «Object» values with which to create the document

Model constructor

Provides the interface to MongoDB collections as well as creates document instances.

* * *

### [Model.prototype.db][78]

Connection the model uses.

* * *

### [Model.prototype.collection][79]

Collection the model uses.

* * *

### [Model.prototype.modelName][80]

* * *

### [Model.prototype.$where][81]

Additional properties to attach to the query when calling `save()` and `isNew` is false.

* * *

### [Model.prototype.baseModelName][82]

If this is a discriminator model, `baseModelName` is the name of the base model.

* * *

### [Model.prototype.save()][60]

##### Parameters

* [fn] «Function» optional callback

##### Returns:

Saves this document.

#### Example:


    product.sold = Date.now();
    product.save(function (err, product) {
      if (err) ..
    })

The callback will receive three parameters

1. `err` if an error occurred
2. `product` which is the saved `product`

As an extra measure of flow control, save will return a Promise.

#### Example:


    product.save().then(function(product) {
       ...
    });

* * *

### [Model.prototype.increment()][83]

Signal that we desire an increment of this documents version.

#### Example:


    Model.findById(id, function (err, doc) {
      doc.increment();
      doc.save(function (err) { .. })
    })

* * *

### [Model.prototype.remove()][84]

##### Parameters

* [fn] «function(err,product)» optional callback

##### Returns:

Removes this document from the db.

#### Example:


    product.remove(function (err, product) {
      if (err) return handleError(err);
      Product.findById(product._id, function (err, product) {
        console.log(product)
      })
    })

As an extra measure of flow control, remove will return a Promise (bound to `fn` if passed) so it could be chained, or hooked to recive errors

#### Example:


    product.remove().then(function (product) {
       ...
    }).catch(function (err) {
       assert.ok(err)
    })

* * *

### [Model.prototype.model()][85]

##### Parameters

* name «String» model name

Returns another Model instance.

#### Example:


    var doc = new Tank;
    doc.model('User').findById(id, callback);

* * *

### [Model.discriminator()][86]

##### Parameters

* schema «Schema» discriminator model schema

Adds a discriminator type.

#### Example:


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

* * *

### [Model.init()][87]

##### Parameters

Performs any async initialization of this model against MongoDB. Currently, this function is only responsible for building [indexes][88], unless [`autoIndex`][89] is turned off.

This function is called automatically, so you don't need to call it. This function is also idempotent, so you may call it to get back a promise that will resolve when your indexes are finished building as an alternative to `MyModel.on('index')`

#### Example:


    var eventSchema = new Schema({ thing: { type: 'string', unique: true }})


    var Event = mongoose.model('Event', eventSchema);

    Event.init().then(function(Event) {


      console.log('Indexes are done building!');
    });

* * *

### [Model.ensureIndexes()][90]

##### Parameters

* [cb] «Function» optional callback

##### Returns:

Sends `createIndex` commands to mongo for each index declared in the schema. The `createIndex` commands are sent in series.

#### Example:


    Event.ensureIndexes(function (err) {
      if (err) return handleError(err);
    });

After completion, an `index` event is emitted on this `Model` passing an error if one occurred.

#### Example:


    var eventSchema = new Schema({ thing: { type: 'string', unique: true }})
    var Event = mongoose.model('Event', eventSchema);

    Event.on('index', function (err) {
      if (err) console.error(err);
    })

_NOTE: It is not recommended that you run this in production. Index creation may impact database performance depending on your load. Use with caution._

* * *

### [Model.createIndexes()][91]

##### Parameters

* [cb] «Function» optional callback

##### Returns:

Similar to `ensureIndexes()`, except for it uses the [`createIndex`][92] function. The `ensureIndex()` function checks to see if an index with that name already exists, and, if not, does not attempt to create the index. `createIndex()` bypasses this check.

* * *

### [Model.prototype.schema][93]

* * *

### [Model.prototype.base][94]

Base Mongoose instance the model uses.

* * *

### [Model.prototype.discriminators][95]

Registered discriminators for this model.

* * *

### [Model.translateAliases()][96]

##### Parameters

* raw «Object» fields/conditions that may contain aliased keys

##### Returns:

* «Object» the translated 'pure' fields/conditions

Translate any aliases fields/conditions so the final query or document object is pure

#### Example:


    Character
      .find(Character.translateAliases({
        '名': 'Eddard Stark'
      })
      .exec(function(err, characters) {})

#### Note:

Only translate arguments of object type anything else is returned raw

* * *

### [Model.remove()][97]

##### Parameters

##### Returns:

Removes all documents that match `conditions` from the collection. To remove just the first document that matches `conditions`, set the `single` option to true.

#### Example:


    Character.remove({ name: 'Eddard Stark' }, function (err) {});

#### Note:

This method sends a remove command directly to MongoDB, no Mongoose documents are involved. Because no Mongoose documents are involved, _no middleware (hooks) are executed_.

* * *

### [Model.deleteOne()][98]

##### Parameters

##### Returns:

Deletes the first document that matches `conditions` from the collection. Behaves like `remove()`, but deletes at most one document regardless of the `single` option.

#### Example:


    Character.deleteOne({ name: 'Eddard Stark' }, function (err) {});

#### Note:

Like `Model.remove()`, this function does **not** trigger `pre('remove')` or `post('remove')` hooks.
* * *

### [Model.deleteMany()][99]

##### Parameters

##### Returns:

Deletes all of the documents that match `conditions` from the collection. Behaves like `remove()`, but deletes all documents that match `conditions` regardless of the `single` option.

#### Example:


    Character.deleteMany({ name: /Stark/, age: { $gte: 18 } }, function (err) {});

#### Note:

Like `Model.remove()`, this function does **not** trigger `pre('remove')` or `post('remove')` hooks.
* * *

### [Model.find()][100]

##### Parameters

##### Returns:

Finds documents

The `conditions` are cast to their respective SchemaTypes before the command is sent.

#### Examples:


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

* * *

### [Model.findById()][101]

##### Parameters

##### Returns:

Finds a single document by its _id field. `findById(id)` is almost* equivalent to `findOne({ _id: id })`. If you want to query by a document's `_id`, use `findById()` instead of `findOne()`.

The `id` is cast based on the Schema before sending the command.

This function triggers the following middleware.

* Except for how it treats `undefined`. If you use `findOne()`, you'll see that `findOne(undefined)` and `findOne({ _id: undefined })` are equivalent to `findOne({})` and return arbitrary documents. However, mongoose translates `findById(undefined)` into `findOne({ _id: null })`.

#### Example:


    Adventure.findById(id, function (err, adventure) {});


    Adventure.findById(id).exec(callback);


    Adventure.findById(id, 'name length', function (err, adventure) {});


    Adventure.findById(id, 'name length').exec(callback);


    Adventure.findById(id, '-length').exec(function (err, adventure) {});


    Adventure.findById(id, 'name', { lean: true }, function (err, doc) {});


    Adventure.findById(id, 'name').lean().exec(function (err, doc) {});

* * *

### [Model.findOne()][102]

##### Parameters

##### Returns:

Finds one document.

The `conditions` are cast to their respective SchemaTypes before the command is sent.

_Note:_ `conditions` is optional, and if `conditions` is null or undefined, mongoose will send an empty `findOne` command to MongoDB, which will return an arbitrary document. If you're querying by `_id`, use `findById()` instead.

#### Example:


    Adventure.findOne({ type: 'iphone' }, function (err, adventure) {});


    Adventure.findOne({ type: 'iphone' }).exec(function (err, adventure) {});


    Adventure.findOne({ type: 'iphone' }, 'name', function (err, adventure) {});


    Adventure.findOne({ type: 'iphone' }, 'name').exec(function (err, adventure) {});


    Adventure.findOne({ type: 'iphone' }, 'name', { lean: true }, callback);


    Adventure.findOne({ type: 'iphone' }, 'name', { lean: true }).exec(callback);


    Adventure.findOne({ type: 'iphone' }).select('name').lean().exec(callback);

* * *

### [Model.count()][103]

##### Parameters

##### Returns:

Counts number of matching documents in a database collection.

#### Example:


    Adventure.count({ type: 'jungle' }, function (err, count) {
      if (err) ..
      console.log('there are %d jungle adventures', count);
    });

* * *

### [Model.distinct()][104]

##### Parameters

##### Returns:

Creates a Query for a `distinct` operation.

Passing a `callback` immediately executes the query.

#### Example


    Link.distinct('url', { clicks: {$gt: 100}}, function (err, result) {
      if (err) return handleError(err);

      assert(Array.isArray(result));
      console.log('unique urls with more than 100 clicks', result);
    })

    var query = Link.distinct('url');
    query.exec(callback);

* * *

### [Model.where()][105]

##### Parameters

* [val] «Object» optional value

##### Returns:

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

* * *

### [Model.prototype.$where()][81]

##### Parameters

* argument «String,Function» is a javascript string or anonymous function

##### Returns:

Creates a `Query` and specifies a `$where` condition.

Sometimes you need to query for things in mongodb using a JavaScript expression. You can do so via `find({ $where: javascript })`, or you can use the mongoose shortcut method $where via a Query chain or from your mongoose Model.


    Blog.$where('this.username.indexOf("val") !== -1').exec(function (err, docs) {});

* * *

### [Model.findOneAndUpdate()][106]

##### Parameters

##### Returns:

Issues a mongodb findAndModify update command.

Finds a matching document, updates it according to the `update` arg, passing any `options`, and returns the found document (if any) to the callback. The query executes immediately if `callback` is passed else a Query object is returned.

#### Options:

* `new`: bool - if true, return the modified document rather than the original. defaults to false (changed in 4.0)
* `upsert`: bool - creates the object if it doesn't exist. defaults to false.
* `fields`: {Object|String} - Field selection. Equivalent to `.select(fields).findOneAndUpdate()`
* `maxTimeMS`: puts a time limit on the query - requires mongodb >= 2.6.0
* `sort`: if multiple docs are found by the conditions, sets the sort order to choose which doc to update
* `runValidators`: if true, runs [update validators][107] on this command. Update validators validate the update operation against the model's schema.
* `setDefaultsOnInsert`: if this and `upsert` are true, mongoose will apply the [defaults][108] specified in the model's schema if a new document is created. This option only works on MongoDB >= 2.4 because it relies on [MongoDB's `$setOnInsert` operator][109].
* `rawResult`: if true, returns the [raw result from the MongoDB driver][110]
* `strict`: overwrites the schema's [strict mode option][111] for this update

#### Examples:


    A.findOneAndUpdate(conditions, update, options, callback)
    A.findOneAndUpdate(conditions, update, options)
    A.findOneAndUpdate(conditions, update, callback)
    A.findOneAndUpdate(conditions, update)
    A.findOneAndUpdate()

#### Note:

All top level update keys which are not `atomic` operation names are treated as set operations:

#### Example:


    var query = { name: 'borne' };
    Model.findOneAndUpdate(query, { name: 'jason bourne' }, options, callback)


    Model.findOneAndUpdate(query, { $set: { name: 'jason bourne' }}, options, callback)

This helps prevent accidentally overwriting your document with `{ name: 'jason bourne' }`.

#### Note:

Values are cast to their appropriate types when using the findAndModify helpers. However, the below are not executed by default.

* defaults. Use the `setDefaultsOnInsert` option to override.

`findAndModify` helpers support limited validation. You can enable these by setting the `runValidators` options, respectively.

If you need full-fledged validation, use the traditional approach of first retrieving the document.


    Model.findById(id, function (err, doc) {
      if (err) ..
      doc.name = 'jason bourne';
      doc.save(callback);
    });

* * *

### [Model.findByIdAndUpdate()][112]

##### Parameters

##### Returns:

Issues a mongodb findAndModify update command by a document's _id field. `findByIdAndUpdate(id, ...)` is equivalent to `findOneAndUpdate({ _id: id }, ...)`.

Finds a matching document, updates it according to the `update` arg, passing any `options`, and returns the found document (if any) to the callback. The query executes immediately if `callback` is passed else a Query object is returned.

This function triggers the following middleware.

#### Options:

* `new`: bool - true to return the modified document rather than the original. defaults to false
* `upsert`: bool - creates the object if it doesn't exist. defaults to false.
* `runValidators`: if true, runs [update validators][107] on this command. Update validators validate the update operation against the model's schema.
* `setDefaultsOnInsert`: if this and `upsert` are true, mongoose will apply the [defaults][108] specified in the model's schema if a new document is created. This option only works on MongoDB >= 2.4 because it relies on [MongoDB's `$setOnInsert` operator][109].
* `sort`: if multiple docs are found by the conditions, sets the sort order to choose which doc to update
* `select`: sets the document fields to return
* `rawResult`: if true, returns the [raw result from the MongoDB driver][110]
* `strict`: overwrites the schema's [strict mode option][111] for this update

#### Examples:


    A.findByIdAndUpdate(id, update, options, callback)
    A.findByIdAndUpdate(id, update, options)
    A.findByIdAndUpdate(id, update, callback)
    A.findByIdAndUpdate(id, update)
    A.findByIdAndUpdate()

#### Note:

All top level update keys which are not `atomic` operation names are treated as set operations:

#### Example:


    Model.findByIdAndUpdate(id, { name: 'jason bourne' }, options, callback)


    Model.findByIdAndUpdate(id, { $set: { name: 'jason bourne' }}, options, callback)

This helps prevent accidentally overwriting your document with `{ name: 'jason bourne' }`.

#### Note:

Values are cast to their appropriate types when using the findAndModify helpers. However, the below are not executed by default.

* defaults. Use the `setDefaultsOnInsert` option to override.

`findAndModify` helpers support limited validation. You can enable these by setting the `runValidators` options, respectively.

If you need full-fledged validation, use the traditional approach of first retrieving the document.


    Model.findById(id, function (err, doc) {
      if (err) ..
      doc.name = 'jason bourne';
      doc.save(callback);
    });

* * *

### [Model.findOneAndRemove()][113]

##### Parameters

##### Returns:

Issue a mongodb findAndModify remove command.

Finds a matching document, removes it, passing the found document (if any) to the callback.

Executes immediately if `callback` is passed else a Query object is returned.

This function triggers the following middleware.

#### Options:

* `sort`: if multiple docs are found by the conditions, sets the sort order to choose which doc to update
* `maxTimeMS`: puts a time limit on the query - requires mongodb >= 2.6.0
* `select`: sets the document fields to return
* `rawResult`: if true, returns the [raw result from the MongoDB driver][110]
* `strict`: overwrites the schema's [strict mode option][111] for this update

#### Examples:


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

* * *

### [Model.findByIdAndRemove()][114]

##### Parameters

##### Returns:

Issue a mongodb findAndModify remove command by a document's _id field. `findByIdAndRemove(id, ...)` is equivalent to `findOneAndRemove({ _id: id }, ...)`.

Finds a matching document, removes it, passing the found document (if any) to the callback.

Executes immediately if `callback` is passed, else a `Query` object is returned.

This function triggers the following middleware.

#### Options:

* `sort`: if multiple docs are found by the conditions, sets the sort order to choose which doc to update
* `select`: sets the document fields to return
* `rawResult`: if true, returns the [raw result from the MongoDB driver][110]
* `strict`: overwrites the schema's [strict mode option][111] for this update

#### Examples:


    A.findByIdAndRemove(id, options, callback)
    A.findByIdAndRemove(id, options)
    A.findByIdAndRemove(id, callback)
    A.findByIdAndRemove(id)
    A.findByIdAndRemove()

* * *

### [Model.create()][115]

##### Parameters

* [callback] «Function» callback

##### Returns:

Shortcut for saving one or more documents to the database. `MyModel.create(docs)` does `new MyModel(doc).save()` for every doc in docs.

This function triggers the following middleware.

#### Example:


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

* * *

### [Model.watch()][116]

##### Parameters

##### Returns:

* «ChangeStream» mongoose-specific change stream wrapper

_Requires a replica set running MongoDB >= 3.6.0._ Watches the underlying collection for changes using [MongoDB change streams][117].

This function does **not** trigger any middleware. In particular, it does **not** trigger aggregate middleware.

#### Example:


    const doc = await Person.create({ name: 'Ned Stark' });
    Person.watch().on('change', change => console.log(change));





    await doc.remove();

* * *

### [Model.insertMany()][118]

##### Parameters

* [callback] «Function» callback

##### Returns:

Shortcut for validating an array of documents and inserting them into MongoDB if they're all valid. This function is faster than `.create()` because it only sends one operation to the server, rather than one for each document.

Mongoose always validates each document **before** sending `insertMany` to MongoDB. So if one document has a validation error, no documents will be saved, unless you set [the `ordered` option to false][119].

This function does **not** trigger save middleware.

This function triggers the following middleware.

#### Example:


    var arr = [{ name: 'Star Wars' }, { name: 'The Empire Strikes Back' }];
    Movies.insertMany(arr, function(error, docs) {});

* * *

### [Model.bulkWrite()][120]

##### Parameters

* [callback] «Function» callback `function(error, bulkWriteOpResult) {}`

##### Returns:

* «Promise» resolves to a `BulkWriteOpResult` if the operation succeeds

Sends multiple `insertOne`, `updateOne`, `updateMany`, `replaceOne`, `deleteOne`, and/or `deleteMany` operations to the MongoDB server in one command. This is faster than sending multiple independent operations (like) if you use `create()`) because with `bulkWrite()` there is only one round trip to MongoDB.

Mongoose will perform casting on all operations you provide.

This function does **not** trigger any middleware, not `save()` nor `update()`. If you need to trigger `save()` middleware for every document use [`create()`][121] instead.

#### Example:


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

* * *

### [Model.hydrate()][122]

##### Parameters

##### Returns:

* «Model» document instance

Shortcut for creating a new Document from existing raw data, pre-saved in the DB. The document returned has no paths marked as modified initially.

#### Example:


    var mongooseCandy = Candy.hydrate({ _id: '54108337212ffb6d459f854c', type: 'jelly bean' });

* * *

### [Model.update()][123]

##### Parameters

##### Returns:

Updates one document in the database without returning it.

This function triggers the following middleware.

#### Examples:


    MyModel.update({ age: { $gt: 18 } }, { oldEnough: true }, fn);
    MyModel.update({ name: 'Tobi' }, { ferret: true }, { multi: true }, function (err, raw) {
      if (err) return handleError(err);
      console.log('The raw response from Mongo was ', raw);
    });

#### Valid options:

* `safe` (boolean) safe mode (defaults to value set in schema (true))
* `upsert` (boolean) whether to create the doc if it doesn't match (false)
* `multi` (boolean) whether multiple documents should be updated (false)
* `runValidators`: if true, runs [update validators][107] on this command. Update validators validate the update operation against the model's schema.
* `setDefaultsOnInsert`: if this and `upsert` are true, mongoose will apply the [defaults][108] specified in the model's schema if a new document is created. This option only works on MongoDB >= 2.4 because it relies on [MongoDB's `$setOnInsert` operator][109].
* `strict` (boolean) overrides the `strict` option for this update
* `overwrite` (boolean) disables update-only mode, allowing you to overwrite the doc (false)

All `update` values are cast to their appropriate SchemaTypes before being sent.

The `callback` function receives `(err, rawResponse)`.

* `err` is the error if any occurred
* `rawResponse` is the full response from Mongo

#### Note:

All top level keys which are not `atomic` operation names are treated as set operations:

#### Example:


    var query = { name: 'borne' };
    Model.update(query, { name: 'jason bourne' }, options, callback)


    Model.update(query, { $set: { name: 'jason bourne' }}, options, callback)


This helps prevent accidentally overwriting all documents in your collection with `{ name: 'jason bourne' }`.

#### Note:

Be careful to not use an existing model instance for the update clause (this won't work and can cause weird behavior like infinite loops). Also, ensure that the update clause does not have an _id property, which causes Mongo to return a "Mod on _id not allowed" error.

#### Note:

To update documents without waiting for a response from MongoDB, do not pass a `callback`, then call `exec` on the returned [Query][124]:


    Comment.update({ _id: id }, { $set: { text: 'changed' }}).exec();

#### Note:

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

* * *

### [Model.updateMany()][125]

##### Parameters

##### Returns:

Same as `update()`, except MongoDB will update _all_ documents that match `criteria` (as opposed to just the first one) regardless of the value of the `multi` option.

**Note** updateMany will _not_ fire update middleware. Use `pre('updateMany')` and `post('updateMany')` instead.

This function triggers the following middleware.

* * *

### [Model.updateOne()][126]

##### Parameters

##### Returns:

Same as `update()`, except MongoDB will update _only_ the first document that matches `criteria` regardless of the value of the `multi` option.

This function triggers the following middleware.

* * *

### [Model.replaceOne()][127]

##### Parameters

##### Returns:

Same as `update()`, except MongoDB replace the existing document with the given document (no atomic operators like `$set`).

This function triggers the following middleware.

* * *

### [Model.mapReduce()][128]

##### Parameters

* [callback] «Function» optional callback

##### Returns:

Executes a mapReduce command.

`o` is an object specifying all mapReduce options as well as the map and reduce functions. All options are delegated to the driver implementation. See [node-mongodb-native mapReduce() documentation][129] for more detail about options.

This function does not trigger any middleware.

#### Example:


    var o = {};
    o.map = function () { emit(this.name, 1) }
    o.reduce = function (k, vals) { return vals.length }
    User.mapReduce(o, function (err, results) {
      console.log(results)
    })

#### Other options:

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

#### * out options:
* `{inline:1}` the results are returned in an array
* `{replace: 'collectionName'}` add the results to collectionName: the results replace the collection
* `{reduce: 'collectionName'}` add the results to collectionName: if dups are detected, uses the reducer / finalize functions
* `{merge: 'collectionName'}` add the results to collectionName: if dups exist the new docs overwrite the old

If `options.out` is set to `replace`, `merge`, or `reduce`, a Model instance is returned that can be used for further querying. Queries run against this model are all executed with the `lean` option; meaning only the js object is returned and no Mongoose magic is applied (getters, setters, etc).

#### Example:


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

* * *

### [Model.aggregate()][130]

##### Parameters

##### Returns:

Performs [aggregations][131] on the models collection.

If a `callback` is passed, the `aggregate` is executed and a `Promise` is returned. If a callback is not passed, the `aggregate` itself is returned.

This function does not trigger any middleware.

#### Example:


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

#### NOTE:

* Arguments are not cast to the model's schema because `$project` operators allow redefining the "shape" of the documents at any stage of the pipeline, which may leave documents in an incompatible format.
* The documents returned are plain javascript objects, not mongoose documents (since any shape of document can be returned).
* Requires MongoDB >= 2.1
* * *

### [Model.geoSearch()][132]

##### Parameters

* [callback] «Function» optional callback

##### Returns:

Implements `$geoSearch` functionality for Mongoose

This function does not trigger any middleware

#### Example:


    var options = { near: [10, 10], maxDistance: 5 };
    Locations.geoSearch({ type : "house" }, options, function(err, res) {
      console.log(res);
    });

#### Options:

* `near` {Array} x,y point to search for
* `maxDistance` {Number} the maximum distance from the point near that a result can be
* `limit` {Number} The maximum number of results to return
* `lean` {Boolean} return the raw object instead of the Mongoose Model
* * *

### [Model.populate()][133]

##### Parameters

* [callback(err,doc)] «Function» Optional callback, executed upon completion. Receives `err` and the `doc(s)`.

##### Returns:

Populates document references.

#### Available options:

* path: space delimited path(s) to populate
* select: optional fields to select
* match: optional query conditions to match
* model: optional name of the model to use for population
* options: optional query options like sort, limit, etc

#### Examples:


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

### [SchemaType()][263]

##### Parameters

SchemaType constructor. Do **not** instantiate `SchemaType` directly. Mongoose converts your schema paths into SchemaTypes automatically.

#### Example:


    const schema = new Schema({ name: String });
    schema.path('name') instanceof SchemaType;

* * *

### [SchemaType.prototype.default()][264]

##### Parameters

* val «Function,any» the default value

##### Returns:

Sets a default value for this SchemaType.

#### Example:


    var schema = new Schema({ n: { type: Number, default: 10 })
    var M = db.model('M', schema)
    var m = new M;
    console.log(m.n)

Defaults can be either `functions` which return the value to use as the default or the literal value itself. Either way, the value will be cast based on its schema type before being set during document creation.

#### Example:


    var schema = new Schema({ aNumber: { type: Number, default: 4.815162342 }})
    var M = db.model('M', schema)
    var m = new M;
    console.log(m.aNumber)


    var schema = new Schema({ mixed: Schema.Types.Mixed });
    schema.path('mixed').default(function () {
      return {};
    });




    var schema = new Schema({ mixed: Schema.Types.Mixed });
    schema.path('mixed').default({});
    var M = db.model('M', schema);
    var m1 = new M;
    m1.mixed.added = 1;
    console.log(m1.mixed);
    var m2 = new M;
    console.log(m2.mixed);

* * *

### [SchemaType.prototype.index()][265]

##### Parameters

* options «Object,Boolean,String»

##### Returns:

Declares the index options for this schematype.

#### Example:


    var s = new Schema({ name: { type: String, index: true })
    var s = new Schema({ loc: { type: [Number], index: 'hashed' })
    var s = new Schema({ loc: { type: [Number], index: '2d', sparse: true })
    var s = new Schema({ loc: { type: [Number], index: { type: '2dsphere', sparse: true }})
    var s = new Schema({ date: { type: Date, index: { unique: true, expires: '1d' }})
    Schema.path('my.path').index(true);
    Schema.path('my.date').index({ expires: 60 });
    Schema.path('my.path').index({ unique: true, sparse: true });

#### NOTE:

_Indexes are created in the background by default. Specify `background: false` to override._

[Direction doesn't matter for single key indexes][266]

* * *

### [SchemaType.prototype.unique()][267]

##### Parameters

##### Returns:

Declares an unique index.

#### Example:


    var s = new Schema({ name: { type: String, unique: true }});
    Schema.path('name').index({ unique: true });

_NOTE: violating the constraint returns an `E11000` error from MongoDB when saving, not a Mongoose validation error._

* * *

### [SchemaType.prototype.text()][268]

##### Parameters

##### Returns:

Declares a full text index.

### Example:


    var s = new Schema({name : {type: String, text : true })
     Schema.path('name').index({text : true});

* * *

### [SchemaType.prototype.sparse()][269]

##### Parameters

##### Returns:

Declares a sparse index.

#### Example:


    var s = new Schema({ name: { type: String, sparse: true })
    Schema.path('name').index({ sparse: true });

* * *

### [SchemaType.prototype.set()][270]

##### Parameters

##### Returns:

Adds a setter to this schematype.

#### Example:


    function capitalize (val) {
      if (typeof val !== 'string') val = '';
      return val.charAt(0).toUpperCase() + val.substring(1);
    }


    var s = new Schema({ name: { type: String, set: capitalize }})


    var s = new Schema({ name: String })
    s.path('name').set(capitalize)

Setters allow you to transform the data before it gets to the raw mongodb document and is set as a value on an actual key.

Suppose you are implementing user registration for a website. Users provide an email and password, which gets saved to mongodb. The email is a string that you will want to normalize to lower case, in order to avoid one email having more than one account -- e.g., otherwise, [avenue@q.com][271] can be registered for 2 accounts via [avenue@q.com][271] and [AvEnUe@Q.CoM][271].

You can set up email lower case normalization easily via a Mongoose setter.


    function toLower(v) {
      return v.toLowerCase();
    }

    var UserSchema = new Schema({
      email: { type: String, set: toLower }
    });

    var User = db.model('User', UserSchema);

    var user = new User({email: '[AVENUE@Q.COM][271]'});
    console.log(user.email); // '[avenue@q.com][271]'

    // or
    var user = new User();
    user.email = '[Avenue@Q.com][271]';
    console.log(user.email); // '[avenue@q.com][271]'
    User.updateOne({ _id: _id }, { $set: { email: '[AVENUE@Q.COM][271]' } }); // update to '[avenue@q.com][271]'


As you can see above, setters allow you to transform the data before it stored in MongoDB.

_NOTE: we could have also just used the built-in `lowercase: true` SchemaType option instead of defining our own function._


    new Schema({ email: { type: String, lowercase: true }})

Setters are also passed a second argument, the schematype on which the setter was defined. This allows for tailored behavior based on options passed in the schema.


    function inspector (val, schematype) {
      if (schematype.options.required) {
        return schematype.path + ' is required';
      } else {
        return val;
      }
    }

    var VirusSchema = new Schema({
      name: { type: String, required: true, set: inspector },
      taxonomy: { type: String, set: inspector }
    })

    var Virus = db.model('Virus', VirusSchema);
    var v = new Virus({ name: 'Parvoviridae', taxonomy: 'Parvovirinae' });

    console.log(v.name);
    console.log(v.taxonomy);

* * *

### [SchemaType.prototype.get()][272]

##### Parameters

##### Returns:

Adds a getter to this schematype.

#### Example:


    function dob (val) {
      if (!val) return val;
      return (val.getMonth() + 1) + "/" + val.getDate() + "/" + val.getFullYear();
    }


    var s = new Schema({ born: { type: Date, get: dob })


    var s = new Schema({ born: Date })
    s.path('born').get(dob)

Getters allow you to transform the representation of the data as it travels from the raw mongodb document to the value that you see.

Suppose you are storing credit card numbers and you want to hide everything except the last 4 digits to the mongoose user. You can do so by defining a getter in the following way:


    function obfuscate (cc) {
      return '****-****-****-' + cc.slice(cc.length-4, cc.length);
    }

    var AccountSchema = new Schema({
      creditCardNumber: { type: String, get: obfuscate }
    });

    var Account = db.model('Account', AccountSchema);

    Account.findById(id, function (err, found) {
      console.log(found.creditCardNumber);
    });

Getters are also passed a second argument, the schematype on which the getter was defined. This allows for tailored behavior based on options passed in the schema.


    function inspector (val, schematype) {
      if (schematype.options.required) {
        return schematype.path + ' is required';
      } else {
        return schematype.path + ' is not';
      }
    }

    var VirusSchema = new Schema({
      name: { type: String, required: true, get: inspector },
      taxonomy: { type: String, get: inspector }
    })

    var Virus = db.model('Virus', VirusSchema);

    Virus.findById(id, function (err, virus) {
      console.log(virus.name);
      console.log(virus.taxonomy);
    })

* * *

### [SchemaType.prototype.validate()][273]

##### Parameters

* [type] «String» optional validator type

##### Returns:

Adds validator(s) for this document path.

Validators always receive the value to validate as their first argument and must return `Boolean`. Returning `false` means validation failed.

The error message argument is optional. If not passed, the [default generic error message template][274] will be used.

#### Examples:


    function validator (val) {
      return val == 'something';
    }
    new Schema({ name: { type: String, validate: validator }});



    var custom = [validator, 'Uh oh, {PATH} does not equal "something".']
    new Schema({ name: { type: String, validate: custom }});



    var many = [
        { validator: validator, msg: 'uh oh' }
      , { validator: anotherValidator, msg: 'failed' }
    ]
    new Schema({ name: { type: String, validate: many }});



    var schema = new Schema({ name: 'string' });
    schema.path('name').validate(validator, 'validation of `{PATH}` failed with value `{VALUE}`');

#### Error message templates:

From the examples above, you may have noticed that error messages support basic templating. There are a few other template keywords besides `{PATH}` and `{VALUE}` too. To find out more, details are available [here][275]

#### Asynchronous validation:

Passing a validator function that receives two arguments tells mongoose that the validator is an asynchronous validator. The first argument passed to the validator function is the value being validated. The second argument is a callback function that must called when you finish validating the value and passed either `true` or `false` to communicate either success or failure respectively.


    schema.path('name').validate({
      isAsync: true,
      validator: function (value, respond) {
        doStuff(value, function () {
          ...
          respond(false);
        });
      },
      message: 'Custom error message!'
    });


    schema.path('name').validate({
      validator: function (value) {
        return new Promise(function (resolve, reject) {
          resolve(false);
        });
      }
    });

You might use asynchronous validators to retreive other documents from the database to validate against or to meet other I/O bound validation needs.

Validation occurs `pre('save')` or whenever you manually execute [document#validate][59].

If validation fails during `pre('save')` and no callback was passed to receive the error, an `error` event will be emitted on your Models associated db [connection][28], passing the validation error object along.


    var conn = mongoose.createConnection(..);
    conn.on('error', handleError);

    var Product = conn.model('Product', yourSchema);
    var dvd = new Product(..);
    dvd.save();

If you desire handling these errors at the Model level, attach an `error` listener to your Model and the event will instead be emitted there.


    Product.on('error', handleError);

* * *

### [SchemaType.prototype.required()][276]

##### Parameters

* [message] «String» optional custom error message

##### Returns:

Adds a required validator to this SchemaType. The validator gets added to the front of this SchemaType's validators array using `unshift()`.

#### Example:


    var s = new Schema({ born: { type: Date, required: true })



    var s = new Schema({ born: { type: Date, required: '{PATH} is required!' })



    var s = new Schema({
      userId: ObjectId,
      username: {
        type: String,
        required: function() { return this.userId != null; }
      }
    })


    var s = new Schema({
      userId: ObjectId,
      username: {
        type: String,
        required: [
          function() { return this.userId != null; },
          'username is required if id is specified'
        ]
      }
    })



    Schema.path('name').required(true);



    Schema.path('name').required(true, 'grrr :( ');


    var isOver18 = function() { return this.age >= 18; };
    Schema.path('voterRegistrationId').required(isOver18);

The required validator uses the SchemaType's `checkRequired` function to determine whether a given value satisfies the required validator. By default, a value satisfies the required validator if `val != null` (that is, if the value is not null nor undefined). However, most built-in mongoose schema types override the default `checkRequired` function:

* * *

### [SchemaType.prototype.select()][277]

##### Parameters

##### Returns:

Sets default `select()` behavior for this path.

Set to `true` if this path should always be included in the results, `false` if it should be excluded by default. This setting can be overridden at the query level.

#### Example:


    T = db.model('T', new Schema({ x: { type: String, select: true }}));
    T.find(..);

    T.find().select('-x').exec(callback);

* * *
* * *

### [VirtualType()][278]

VirtualType constructor

This is what mongoose uses to define virtual attributes via `Schema.prototype.virtual`.

#### Example:


    var fullname = schema.virtual('fullname');
    fullname instanceof mongoose.VirtualType

* * *

### [VirtualType.prototype.get()][279]

##### Parameters

##### Returns:

Defines a getter.

#### Example:


    var virtual = schema.virtual('fullname');
    virtual.get(function () {
      return this.name.first + ' ' + this.name.last;
    });

* * *

### [VirtualType.prototype.set()][280]

##### Parameters

##### Returns:

Defines a setter.

#### Example:


    var virtual = schema.virtual('fullname');
    virtual.set(function (v) {
      var parts = v.split(' ');
      this.name.first = parts[0];
      this.name.last = parts[1];
    });

* * *

### [VirtualType.prototype.applyGetters()][281]

##### Parameters

##### Returns:

* «any» the value after applying all getters

Applies getters to `value` using optional `scope`.

* * *

### [VirtualType.prototype.applySetters()][282]

##### Parameters

##### Returns:

* «any» the value after applying all setters

Applies setters to `value` using optional `scope`.

* * *
* * *

### [MongooseError()][283]

##### Parameters

* msg «String» Error message

MongooseError constructor

* * *

### [MongooseError.messages][284]

The default built-in validator error messages.

* * *

### [MongooseError.DocumentNotFoundError][285]

An instance of this error class will be returned when `save()` fails because the underlying document was not found. The constructor takes one parameter, the conditions that mongoose passed to `update()` when trying to update the document.

* * *

### [MongooseError.CastError][286]

An instance of this error class will be returned when mongoose failed to cast a value.

* * *

### [MongooseError.ValidationError][287]

An instance of this error class will be returned when [validation][288] failed.

* * *

### [MongooseError.ValidatorError][289]

A `ValidationError` has a hash of `errors` that contain individual `ValidatorError` instances

* * *

### [MongooseError.VersionError][290]

An instance of this error class will be returned when you call `save()` after the document in the database was changed in a potentially unsafe way. See the [`versionKey` option][291] for more information.

* * *

### [MongooseError.OverwriteModelError][292]

* * *

### [MongooseError.MissingSchemaError][293]

Thrown when you try to access a model that has not been registered yet

* * *

### [MongooseError.DivergentArrayError][294]

An instance of this error will be returned if you used an array projection and then modified the array in an unsafe way.

[1]: http://mongoosejs.com#schema_Schema
[2]: http://mongoosejs.com#schema_Schema-childSchemas
[3]: http://mongoosejs.com#schema_Schema-obj
[4]: http://mongoosejs.com#schema_Schema-clone
[5]: http://mongoosejs.com#schema_Schema-add
[6]: http://mongoosejs.com#reserved_reserved
[7]: http://mongoosejs.com#schema_Schema-path
[8]: http://mongoosejs.com#schema_Schema-eachPath
[9]: http://mongoosejs.com#schema_Schema-requiredPaths
[10]: http://mongoosejs.com#schema_Schema-pathType
[11]: http://mongoosejs.com#schema_Schema-queue
[12]: http://mongoosejs.com#schema_Schema-pre
[13]: http://mongoosejs.com#schema_Schema-post
[14]: http://mongoosejs.com#schema_Schema-plugin
[15]: http://mongoosejs.com#schema_Schema-method
[16]: http://mongoosejs.com#schema_Schema-static
[17]: http://mongoosejs.com#schema_Schema-index
[18]: https://www.npmjs.com/package/ms
[19]: http://mongoosejs.com#schema_Schema-set
[20]: http://mongoosejs.com#schema_Schema-get
[21]: http://mongoosejs.com#indextypes_indexTypes
[22]: http://mongoosejs.com#schema_Schema-indexes
[23]: http://mongoosejs.com#schema_Schema-virtual
[24]: http://mongoosejs.com#schema_Schema-virtualpath
[25]: http://mongoosejs.com#schema_Schema-remove
[26]: http://mongoosejs.com#schema_Schema-loadClass
[27]: http://mongoosejs.com#types_Types
[28]: http://mongoosejs.com#connection_Connection
[29]: http://mongoosejs.com#connection_Connection-readyState
[30]: http://mongoosejs.com#connection_Connection-collections
[31]: http://mongoosejs.com#connection_Connection-db
[32]: http://mongoosejs.com#connection_Connection-config
[33]: http://mongoosejs.com#connection_Connection-createCollection
[34]: http://mongoosejs.com#connection_Connection-dropCollection
[35]: http://mongoosejs.com#connection_Connection-dropDatabase
[36]: http://mongoosejs.com#connection_Connection-close
[37]: http://mongoosejs.com#connection_Connection-collection
[38]: http://mongoosejs.com#connection_Connection-model
[39]: http://mongoosejs.com#utils_exports.toCollectionName
[40]: http://mongoosejs.com#connection_Connection-modelNames
[41]: http://mongoosejs.com#document_Document-schema
[42]: http://mongoosejs.com#document_Document-isNew
[43]: http://mongoosejs.com#document_Document-id
[44]: http://mongoosejs.com/docs/guide.html#id
[45]: http://mongoosejs.com#document_Document-errors
[46]: http://mongoosejs.com#document_Document-init
[47]: http://mongoosejs.com#document_Document-update
[48]: http://mongoosejs.com#document_Document-set
[49]: http://mongoosejs.com#document_Document-get
[50]: http://mongoosejs.com#document_Document-markModified
[51]: http://mongoosejs.com/schematypes.html#mixed
[52]: http://mongoosejs.com#document_Document-unmarkModified
[53]: http://mongoosejs.com#document_Document-modifiedPaths
[54]: http://mongoosejs.com#document_Document-isModified
[55]: http://mongoosejs.com#document_Document-isDirectModified
[56]: http://mongoosejs.com#document_Document-isInit
[57]: http://mongoosejs.com#document_Document-isSelected
[58]: http://mongoosejs.com#document_Document-isDirectSelected
[59]: http://mongoosejs.com#document_Document-validate
[60]: http://mongoosejs.com#model_Model-save
[61]: http://mongoosejs.com#document_Document-validateSync
[62]: http://mongoosejs.com#document_Document-invalidate
[63]: http://mongoosejs.com#document_Document-save
[64]: http://mongoosejs.com#document_Document-toObject
[65]: http://mongodb.github.com/node-mongodb-native/api-bson-generated/binary.html
[66]: http://mongoosejs.com/docs/guide.html#toObject
[67]: http://mongoosejs.com#document_Document-toJSON
[68]: http://mongoosejs.com/docs/guide.html#toJSON
[69]: http://mongoosejs.com#document_Document-inspect
[70]: http://mongoosejs.com#document_Document-toString
[71]: http://mongoosejs.com#document_Document-equals
[72]: http://mongoosejs.com#document_Document-populate
[73]: http://mongoosejs.com#document_Document-execPopulate
[74]: http://mongoosejs.com#model_Model.populate
[75]: http://mongoosejs.com#document_Document-populated
[76]: http://mongoosejs.com#document_Document-depopulate
[77]: http://mongoosejs.com#model_Model
[78]: http://mongoosejs.com#model_Model-db
[79]: http://mongoosejs.com#model_Model-collection
[80]: http://mongoosejs.com#model_Model-modelName
[81]: http://mongoosejs.com#model_Model-$where
[82]: http://mongoosejs.com#model_Model-baseModelName
[83]: http://mongoosejs.com#model_Model-increment
[84]: http://mongoosejs.com#model_Model-remove
[85]: http://mongoosejs.com#model_Model-model
[86]: http://mongoosejs.com#discriminator_discriminator
[87]: http://mongoosejs.com#init_init
[88]: https://docs.mongodb.com/manual/indexes/
[89]: http://mongoosejs.com/docs/guide.html#autoIndex
[90]: http://mongoosejs.com#ensureindexes_ensureIndexes
[91]: http://mongoosejs.com#createindexes_createIndexes
[92]: http://mongodb.github.io/node-mongodb-native/2.2/api/Collection.html#createIndex
[93]: http://mongoosejs.com#model_Model-schema
[94]: http://mongoosejs.com#model_Model-base
[95]: http://mongoosejs.com#model_Model-discriminators
[96]: http://mongoosejs.com#translatealiases_translateAliases
[97]: http://mongoosejs.com#remove_remove
[98]: http://mongoosejs.com#deleteone_deleteOne
[99]: http://mongoosejs.com#deletemany_deleteMany
[100]: http://mongoosejs.com#find_find
[101]: http://mongoosejs.com#findbyid_findById
[102]: http://mongoosejs.com#findone_findOne
[103]: http://mongoosejs.com#count_count
[104]: http://mongoosejs.com#distinct_distinct
[105]: http://mongoosejs.com#where_where
[106]: http://mongoosejs.com#findoneandupdate_findOneAndUpdate
[107]: http://mongoosejs.com/docs/validation.html#update-validators
[108]: http://mongoosejs.com/docs/defaults.html
[109]: https://docs.mongodb.org/v2.4/reference/operator/update/setOnInsert/
[110]: http://mongodb.github.io/node-mongodb-native/2.0/api/Collection.html#findAndModify
[111]: http://mongoosejs.com/docs/guide.html#strict
[112]: http://mongoosejs.com#findbyidandupdate_findByIdAndUpdate
[113]: http://mongoosejs.com#findoneandremove_findOneAndRemove
[114]: http://mongoosejs.com#findbyidandremove_findByIdAndRemove
[115]: http://mongoosejs.com#create_create
[116]: http://mongoosejs.com#watch_watch
[117]: https://docs.mongodb.com/manual/changeStreams/
[118]: http://mongoosejs.com#insertmany_insertMany
[119]: https://docs.mongodb.com/manual/reference/method/db.collection.insertMany/#error-handling
[120]: http://mongoosejs.com#bulkwrite_bulkWrite
[121]: http://mongoosejs.com/docs/api.html#model_Model.create
[122]: http://mongoosejs.com#hydrate_hydrate
[123]: http://mongoosejs.com#update_update
[124]: http://mongoosejs.com#query-js
[125]: http://mongoosejs.com#updatemany_updateMany
[126]: http://mongoosejs.com#updateone_updateOne
[127]: http://mongoosejs.com#replaceone_replaceOne
[128]: http://mongoosejs.com#mapreduce_mapReduce
[129]: http://mongodb.github.io/node-mongodb-native/api-generated/collection.html#mapreduce
[130]: http://mongoosejs.com#aggregate_aggregate
[131]: http://docs.mongodb.org/manual/applications/aggregation/
[132]: http://mongoosejs.com#geosearch_geoSearch
[133]: http://mongoosejs.com#populate_populate
[134]: http://mongoosejs.com#query_Query
[135]: http://mongoosejs.com/docs/api.html#find_find
[136]: http://mongoosejs.com#query_Query-use$geoWithin
[137]: http://mongoosejs.com#query_Query-toConstructor
[138]: http://mongoosejs.com#query_Query-$where
[139]: http://docs.mongodb.org/manual/reference/operator/where/
[140]: http://mongoosejs.com#query_Query-where
[141]: http://mongoosejs.com#query_Query-equals
[142]: http://mongoosejs.com#query_Query-or
[143]: http://mongoosejs.com#query_Query-nor
[144]: http://mongoosejs.com#query_Query-and
[145]: http://mongoosejs.com#query_Query-gt
[146]: http://mongoosejs.com#query_Query-gte
[147]: http://mongoosejs.com#query_Query-lt
[148]: http://mongoosejs.com#query_Query-lte
[149]: http://mongoosejs.com#query_Query-ne
[150]: http://mongoosejs.com#query_Query-in
[151]: http://mongoosejs.com#query_Query-nin
[152]: http://mongoosejs.com#query_Query-all
[153]: http://mongoosejs.com#query_Query-size
[154]: http://mongoosejs.com#query_Query-regex
[155]: http://mongoosejs.com#query_Query-maxDistance
[156]: http://mongoosejs.com#query_Query-mod
[157]: http://mongoosejs.com#query_Query-exists
[158]: http://mongoosejs.com#query_Query-elemMatch
[159]: http://mongoosejs.com#query_Query-within
[160]: http://mongoosejs.com#query_Query-use%2524geoWithin
[161]: https://github.com/ebensing/mongoose-within
[162]: http://mongoosejs.com#query_Query-slice
[163]: http://mongoosejs.com#query_Query-limit
[164]: http://mongoosejs.com#query_Query-skip
[165]: http://mongoosejs.com#query_Query-maxScan
[166]: http://mongoosejs.com#query_Query-batchSize
[167]: http://mongoosejs.com#query_Query-snapshot
[168]: http://mongoosejs.com#query_Query-hint
[169]: http://mongoosejs.com#query_Query-select
[170]: http://mongoosejs.com/docs/api.html#schematype_SchemaType-select
[171]: https://docs.mongodb.com/manual/tutorial/project-fields-from-query-results/#suppress-id-field
[172]: http://mongoosejs.com#query_Query-slaveOk
[173]: http://mongoosejs.com#query_Query-read
[174]: http://docs.mongodb.org/manual/applications/replication/#read-preference
[175]: http://mongodb.github.com/node-mongodb-native/driver-articles/anintroductionto1_1and2_2.html#read-preferences
[176]: http://mongoosejs.com#query_Query-merge
[177]: http://mongoosejs.com#query_Query-setOptions
[178]: http://www.mongodb.org/display/DOCS/Tailable+Cursors
[179]: http://www.mongodb.org/display/DOCS/Advanced+Queries#AdvancedQueries-%7B%7Bsort()%7D%7D
[180]: http://www.mongodb.org/display/DOCS/Advanced+Queries#AdvancedQueries-%7B%7Blimit%28%29%7D%7D
[181]: http://www.mongodb.org/display/DOCS/Advanced+Queries#AdvancedQueries-%7B%7Bskip%28%29%7D%7D
[182]: https://docs.mongodb.org/v3.2/reference/operator/meta/maxScan/#metaOp._S_maxScan
[183]: http://www.mongodb.org/display/DOCS/Advanced+Queries#AdvancedQueries-%7B%7BbatchSize%28%29%7D%7D
[184]: http://www.mongodb.org/display/DOCS/Advanced+Queries#AdvancedQueries-%24comment
[185]: http://www.mongodb.org/display/DOCS/Advanced+Queries#AdvancedQueries-%7B%7Bsnapshot%28%29%7D%7D
[186]: http://www.mongodb.org/display/DOCS/Advanced+Queries#AdvancedQueries-%24hint
[187]: https://docs.mongodb.com/manual/reference/method/db.collection.update/
[188]: http://mongoosejs.com/api.html#query_Query-lean
[189]: http://mongoosejs.com#query_Query-getQuery
[190]: http://mongoosejs.com#query_Query-getUpdate
[191]: http://mongoosejs.com#query_Query-lean
[192]: http://mongoosejs.com#document-js
[193]: https://groups.google.com/forum/#!topic/mongoose-orm/u2_DzDydcnA/discussion
[194]: http://mongoosejs.com#query_Query-stream
[195]: http://mongoosejs.com#query_Query-error
[196]: http://mongoosejs.com#query_Query-mongooseOptions
[197]: http://mongoosejs.com#query_Query-find
[198]: http://mongoosejs.com#query_Query-collation
[199]: http://mongoosejs.com#query_Query-findOne
[200]: http://mongoosejs.com#query_Query-count
[201]: http://mongoosejs.com#query_Query-distinct
[202]: http://mongoosejs.com#query_Query-sort
[203]: http://mongoosejs.com#query_Query-remove
[204]: http://mongoosejs.com#query_Query-deleteOne
[205]: http://mongoosejs.com#query_Query-deleteMany
[206]: http://mongoosejs.com#query_Query-findOneAndUpdate
[207]: http://www.mongodb.org/display/DOCS/findAndModify+Command
[208]: http://mongoosejs.com#query_Query-findOneAndRemove
[209]: http://mongoosejs.com#query_Query-update
[210]: mailto:address%40example.com
[211]: http://mongoosejs.com#query_Query-updateMany
[212]: http://mongoosejs.com#query_Query-updateOne
[213]: http://mongoosejs.com#query_Query-replaceOne
[214]: http://mongoosejs.com#query_Query-exec
[215]: http://mongoosejs.com#query_Query-then
[216]: http://mongoosejs.com#query_Query-catch
[217]: http://mongoosejs.com#query_Query-populate
[218]: http://mongoosejs.com#query_Query-cast
[219]: http://mongoosejs.com#query_Query-cursor
[220]: http://mongodb.github.io/node-mongodb-native/2.1/api/Cursor.html
[221]: https://strongloop.com/strongblog/whats-new-io-js-beta-streams3/
[222]: http://mongoosejs.com#query_Query-maxscan
[223]: http://mongoosejs.com#query_Query-tailable
[224]: http://mongoosejs.com#query_Query-intersects
[225]: http://mongoosejs.com#query_Query-geometry
[226]: http://mongoosejs.com#query_Query-near
[227]: http://mongoosejs.com#query_Query-nearSphere
[228]: http://mongoosejs.com#query_Query-polygon
[229]: http://mongoosejs.com#query_Query-box
[230]: http://mongoosejs.com#query_Query-circle
[231]: http://mongoosejs.com#query_Query-center
[232]: http://mongoosejs.com#query_Query-centerSphere
[233]: http://mongoosejs.com#query_Query-selected
[234]: http://mongoosejs.com#query_Query-selectedInclusively
[235]: http://mongoosejs.com#query_Query-selectedExclusively
[236]: http://mongoosejs.com/docs/api.html#aggregate_aggregate
[237]: http://mongoosejs.com#aggregate_aggregate-model
[238]: http://mongoosejs.com#aggregate_aggregate-append
[239]: http://mongoosejs.com#aggregate_aggregate-addFields
[240]: http://mongoosejs.com#aggregate_aggregate-project
[241]: http://mongoosejs.com#aggregate_aggregate-group
[242]: http://mongoosejs.com#aggregate_aggregate-match
[243]: http://mongoosejs.com#aggregate_aggregate-skip
[244]: http://mongoosejs.com#aggregate_aggregate-limit
[245]: http://mongoosejs.com#aggregate_aggregate-near
[246]: http://mongoosejs.com#aggregate_aggregate-unwind
[247]: http://mongoosejs.com#aggregate_aggregate-lookup
[248]: http://mongoosejs.com#aggregate_aggregate-graphLookup
[249]: http://mongoosejs.com#aggregate_aggregate-sample
[250]: http://mongoosejs.com#aggregate_aggregate-sort
[251]: http://mongoosejs.com#aggregate_aggregate-read
[252]: http://mongoosejs.com#aggregate_aggregate-explain
[253]: http://mongoosejs.com#aggregate_aggregate-allowDiskUse
[254]: http://mongoosejs.com#aggregate_aggregate-option
[255]: http://mongoosejs.com#aggregate_aggregate-cursor
[256]: http://mongoosejs.com#aggregate_aggregate-addCursorFlag
[257]: http://mongodb.github.io/node-mongodb-native/2.2/api/Cursor.html#addCursorFlag
[258]: http://mongoosejs.com#aggregate_aggregate-collation
[259]: http://mongoosejs.com#aggregate_aggregate-facet
[260]: http://mongoosejs.com#aggregate_aggregate-pipeline
[261]: http://mongoosejs.com#aggregate_aggregate-exec
[262]: http://mongoosejs.com#aggregate_aggregate-then
[263]: http://mongoosejs.com#schematype_SchemaType
[264]: http://mongoosejs.com#schematype_SchemaType-default
[265]: http://mongoosejs.com#schematype_SchemaType-index
[266]: http://www.mongodb.org/display/DOCS/Indexes#Indexes-CompoundKeysIndexes
[267]: http://mongoosejs.com#schematype_SchemaType-unique
[268]: http://mongoosejs.com#schematype_SchemaType-text
[269]: http://mongoosejs.com#schematype_SchemaType-sparse
[270]: http://mongoosejs.com#schematype_SchemaType-set
[271]: mailto:avenue%40q.com
[272]: http://mongoosejs.com#schematype_SchemaType-get
[273]: http://mongoosejs.com#schematype_SchemaType-validate
[274]: http://mongoosejs.com#error_messages_MongooseError-messages
[275]: http://mongoosejs.com#error_messages_MongooseError.messages
[276]: http://mongoosejs.com#schematype_SchemaType-required
[277]: http://mongoosejs.com#schematype_SchemaType-select
[278]: http://mongoosejs.com#virtualtype_VirtualType
[279]: http://mongoosejs.com#virtualtype_VirtualType-get
[280]: http://mongoosejs.com#virtualtype_VirtualType-set
[281]: http://mongoosejs.com#virtualtype_VirtualType-applyGetters
[282]: http://mongoosejs.com#virtualtype_VirtualType-applySetters
[283]: http://mongoosejs.com#mongooseerror_MongooseError
[284]: http://mongoosejs.com#messages_messages
[285]: http://mongoosejs.com#documentnotfounderror_DocumentNotFoundError
[286]: http://mongoosejs.com#casterror_CastError
[287]: http://mongoosejs.com#validationerror_ValidationError
[288]: http://mongoosejs.com/docs/validation.html
[289]: http://mongoosejs.com#validatorerror_ValidatorError
[290]: http://mongoosejs.com#versionerror_VersionError
[291]: http://mongoosejs.com/docs/guide.html#versionKey
[292]: http://mongoosejs.com#overwritemodelerror_OverwriteModelError
[293]: http://mongoosejs.com#missingschemaerror_MissingSchemaError
[294]: http://mongoosejs.com#divergentarrayerror_DivergentArrayError


