# 模式

## Schema()

**参数**

模式构造函数

**示例**

    var child = new Schema({ name: String });
    var schema = new Schema({ name: String, age: Number, children: [child] });
    var Tree = mongoose.model('Tree', schema);


    new Schema({ name: String }, { _id: false, autoIndex: false })

**选项**

**注意**

_When nesting schemas, (`children` in the 示例 above), always declare the child schema first before passing it into its parent._

## Schema.prototype.childSchemas

Array of child schemas (from document arrays and single nested subdocs) and their corresponding compiled models. Each element of the array is an object with 2 properties: `schema` and `model`.

This property is typically only useful for plugin authors and advanced users. You do not need to interact with this property at all to use mongoose.

## Schema.prototype.obj

The original object passed to the schema constructor

**示例**


    var schema = new Schema({ a: String }).add({ b: String });
    schema.obj;

## Schema.prototype.clone()

**返回**

* «Schema» the cloned schema

返回 a deep copy of the schema

## Schema.prototype.add()

**参数**

Adds key path / schema type pairs to this schema.

**示例**


    var ToySchema = new Schema;
    ToySchema.add({ name: 'string', color: 'string', price: 'number' });

## Schema.reserved

Reserved document keys.

Keys in this object are names that are rejected in schema declarations b/c they conflict with mongoose functionality. Using these key name will throw an error.


    on, emit, _events, db, get, set, init, isNew, errors, schema, 选项, modelName, collection, _pres, _posts, toObject

_注意:_ Use of these terms as method names is permitted, but play at your own risk, as they may be existing mongoose document methods you are stomping on.


    var schema = new Schema(..);
     schema.methods.init = function () {}

## Schema.prototype.path()

**参数**

Gets/sets schema paths.

Sets a path (if arity 2) Gets a path (if arity 1)

**示例**


    schema.path('name')
    schema.path('name', Number)

## Schema.prototype.eachPath()

**参数**

* fn «Function» callback function

**返回**

Iterates the schemas paths similar to Array#forEach.

The callback is passed the pathname and schemaType as arguments on each iteration.

## Schema.prototype.requiredPaths()

**参数**

* invalidate «Boolean» refresh the cache

**返回**

返回 an Array of path strings that are required by this schema.

## Schema.prototype.pathType()

**参数**

**返回**

返回 the pathType of `path` for this schema.

Given a path, 返回 whether it is a real, virtual, nested, or ad-hoc/undefined path.

## Schema.prototype.queue()

**参数**

* args «Array» arguments to pass to the method

Adds a method call to the queue.

## Schema.prototype.pre()

**参数**

Defines a pre hook for the document.

**示例**


    var toySchema = new Schema(..);

    toySchema.pre('save', function (next) {
      if (!this.created) this.created = new Date;
      next();
    })

    toySchema.pre('validate', function (next) {
      if (this.name !== 'Woody') this.name = 'Woody';
      next();
    })

## Schema.prototype.post()

**参数**

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

## Schema.prototype.plugin()

**参数**

Registers a plugin for this schema.

## Schema.prototype.method()

**参数**

Adds an instance method to documents constructed from Models compiled from this schema.

**示例**


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

## Schema.prototype.static()

**参数**

Adds static "class" methods to Models compiled from this schema.

**示例**


    var schema = new Schema(..);
    schema.static('findByName', function (name, callback) {
      return this.find({ name: name }, callback);
    });

    var Drink = mongoose.model('Drink', schema);
    Drink.findByName('sanpellegrino', function (err, drinks) {

    });

If a hash of name/fn pairs is passed as the only argument, each name/fn pair will be added as statics.

## Schema.prototype.index()

**参数**

* [选项.expires=null] «String» Mongoose-specific syntactic sugar, uses [ms to convert `expires` option into seconds for the `expireAfterSeconds` in the above link.

Defines an index (most likely compound) for this schema.

**示例**


    schema.index({ first: 1, last: -1 })

## Schema.prototype.set()

**参数**

* [value] «Object» if not passed, the current option value is returned

Sets/gets a schema option.

**示例**


    schema.set('strict');
    schema.set('strict', false);
    schema.set('strict');

## Schema.prototype.get()

**参数**

* key «String» option name
* * *

## indexTypes

## Schema.prototype.indexes()

Compiles indexes from fields and schema-level indexes

## Schema.prototype.virtual()

**参数**

**返回**

Creates a virtual type with the given name.

## Schema.prototype.virtualpath()

**参数**

**返回**

返回 the virtual type with the given `name`.

## Schema.prototype.remove()

**参数**

Removes the given `path` (or [`paths`]).

## Schema.prototype.loadClass()

**参数**

Loads an ES6 class into a schema. Maps setters + getters, static methods, and instance methods to schema virtuals, statics, and methods.
* * *

## Schema.Types

The various built-in Mongoose Schema Types.

**示例**


    var mongoose = require('mongoose');
    var ObjectId = mongoose.Schema.Types.ObjectId;

**类型**

Using this exposed access to the `Mixed` SchemaType, we can use them in our schema.


    var Mixed = mongoose.Schema.Types.Mixed;
    new mongoose.Schema({ _user: Mixed })