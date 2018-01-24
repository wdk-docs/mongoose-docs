# Mongoose v5.0.1: FAQ

[Source](http://mongoosejs.com/docs/faq.html "Permalink to Mongoose v5.0.1: FAQ")

## FAQ

**Q**. Why don't my changes to arrays get saved when I update an element directly?


    doc.array[3] = 'changed';
    doc.save();


**A**. Mongoose doesn't create getters/setters for array indexes; without them mongoose never gets notified of the change and so doesn't know to persist the new value. The work-around is to use [MongooseArray#set][1] available in **Mongoose >= 3.2.0**.


    doc.array.set(3, 'changed');
    doc.save();


    doc.array[3] = 'changed';
    doc.markModified('array');
    doc.save();


* * *

**Q**. I declared a schema property as `unique` but I can still save duplicates. What gives?

**A**. Mongoose doesn't handle `unique` on its own: `{ name: { type: String, unique: true } }` is just a shorthand for creating a [MongoDB unique index on `name`][2]. For example, if MongoDB doesn't already have a unique index on `name`, the below code will not error despite the fact that `unique` is true.


    var schema = new mongoose.Schema({
      name: { type: String, unique: true }
    });
    var Model = db.model('Test', schema);

    Model.create([{ name: 'Val' }, { name: 'Val' }], function(err) {
      console.log(err);
    });


However, if you wait for the index to build using the `Model.on('index')` event, attempts to save duplicates will correctly error.


    var schema = new mongoose.Schema({
      name: { type: String, unique: true }
    });
    var Model = db.model('Test', schema);

    Model.on('index', function(err) {
      assert.ifError(err);
      Model.create([{ name: 'Val' }, { name: 'Val' }], function(err) {
        console.log(err);
      });
    });




    Model.init().then(function() {
      assert.ifError(err);
      Model.create([{ name: 'Val' }, { name: 'Val' }], function(err) {
        console.log(err);
      });
    });


MongoDB persists indexes, so you only need to rebuild indexes if you're starting with a fresh database or you ran `db.dropDatabase()`. In a production environment, you should [create your indexes using the MongoDB shell])() rather than relying on mongoose to do it for you. The `unique` option for schemas is convenient for development and documentation, but mongoose is _not_ an index management solution.

* * *

**Q**. When I have a nested property in a schema, mongoose adds empty objects by default. Why?


    var schema = new mongoose.Schema({
      nested: {
        prop: String
      }
    });
    var Model = db.model('Test', schema);



    console.log(new Model());


**A**. This is a performance optimization. These empty objects are not saved to the database, nor are they in the result `toObject()`, nor do they show up in `JSON.stringify()` output unless you turn off the [`minimize` option][3].

The reason for this behavior is that Mongoose's change detection and getters/setters are based on [`Object.defineProperty()`][4]. In order to support change detection on nested properties without incurring the overhead of running `Object.defineProperty()` every time a document is created, mongoose defines properties on the `Model` prototype when the model is compiled. Because mongoose needs to define getters and setters for `nested.prop`, `nested` must always be defined as an object on a mongoose document, even if `nested` is undefined on the underlying [POJO][3].

* * *

**Q**. I'm using an arrow function for a [virtual][5], getter/setter, or [method][6] and the value of `this` is wrong.

**A**. Arrow functions [handle the `this` keyword much differently than conventional functions][7]. Mongoose getters/setters depend on `this` to give you access to the document that you're writing to, but this functionality does not work with arrow functions. Do **not** use arrow functions for mongoose getters/setters unless do not intend to access the document in the getter/setter.




    var schema = new mongoose.Schema({
      propWithGetter: {
        type: String,
        get: v => {

          console.log(this);
          return v;
        }
      }
    });


    schema.method.arrowMethod = () => this;
    schema.virtual('virtualWithArrow').get(() => {

      console.log(this);
    });


* * *

**Q**. I have an embedded property named `type` like this:


    const holdingSchema = new Schema({



      asset: {
        type: String,
        ticker: String
      }
    });


But mongoose gives me a CastError telling me that it can't cast an object to a string when I try to save a `Holding` with an `asset` object. Why is this?


    Holding.create({ asset: { type: 'stock', ticker: 'MDB' } }).catch(error => {

      console.error(error);
    });


**A**. The `type` property is special in mongoose, so when you say `type: String`, mongoose interprets it as a type declaration. In the above schema, mongoose thinks `asset` is a string, not an object. Do this instead:


    const holdingSchema = new Schema({



      asset: {
        type: { type: String },
        ticker: String
      }
    });


* * *

**Q**. Why don't in-place modifications to date objects (e.g. `date.setMonth(1);`) get saved?


    doc.createdAt.setDate(2011, 5, 1);
    doc.save();


**A**. Mongoose currently doesn't watch for in-place updates to date objects. If you have need for this feature, feel free to discuss on [this GitHub issue][8]. There are several workarounds:


    doc.createdAt.setDate(2011, 5, 1);
    doc.markModified('createdAt');
    doc.save();

    doc.createdAt = new Date(2011, 5, 1).setHours(4);
    doc.save();


* * *

**Q**. I'm populating a nested property under an array like the below code:


      new Schema({
        arr: [{
          child: { ref: 'OtherModel', type: Schema.Types.ObjectId }
        }]
      });


`.populate({ path: 'arr.child', options: { sort: 'name' } })` won't sort by `arr.child.name`?

**A**. See [this GitHub issue][9]. It's a known issue but one that's exceptionally difficult to fix.

* * *

**Q**. All function calls on my models hang, what am I doing wrong?

**A**. By default, mongoose will buffer your function calls until it can connect to MongoDB. Read the [buffering section of the connection docs][10] for more information.

* * *

**Q**. How can I enable debugging?

**A**. Set the `debug` option to `true`:


    mongoose.set('debug', true)


All executed collection methods will log output of their arguments to your console.

* * *

**Q**. My `save()` callback never executes. What am I doing wrong?

**A**. All `collection` actions (insert, remove, queries, etc.) are queued until the `connection` opens. It is likely that an error occurred while attempting to connect. Try adding an error handler to your connection.


    mongoose.connect(..);
    mongoose.connection.on('error', handleError);


    var conn = mongoose.createConnection(..);
    conn.on('error', handleError);


If you want to opt out of mongoose's buffering mechanism across your entire application, set the global `bufferCommands` option to false:


    mongoose.set('bufferCommands', false);


* * *

**Q**. Should I create/destroy a new connection for each database operation?

**A**. No. Open your connection when your application starts up and leave it open until the application shuts down.

* * *

**Q**. Why do I get "OverwriteModelError: Cannot overwrite .. model once compiled" when I use nodemon / a testing framework?

**A**. `mongoose.model('ModelName', schema)` requires 'ModelName' to be unique, so you can access the model by using `mongoose.model('ModelName')`. If you put `mongoose.model('ModelName', schema);` in a [mocha `beforeEach()` hook][11], this code will attempt to create a new model named 'ModelName' before **every** test, and so you will get an error. Make sure you only create a new model with a given name **once**. If you need to create multiple models with the same name, create a new connection and bind the model to the connection.


    var mongoose = require('mongoose');
    var connection = mongoose.createConnection(..);


    var kittySchema = mongoose.Schema({ name: String });


    var Kitten = connection.model('Kitten', kittySchema);


**Something to add?**

If you'd like to contribute to this page, please [visit it][12] on github and use the [Edit][13] button to send a pull request.

[1]: http://mongoosejs.com/api.html#types_array_MongooseArray.set
[2]: https://docs.mongodb.com/manual/core/index-unique/
[3]: http://mongoosejs.com/docs/guide.html#minimize
[4]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty
[5]: http://mongoosejs.com/docs/guide.html#virtuals
[6]: http://mongoosejs.com/docs/guide.html#methods
[7]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#No_binding_of_this
[8]: https://github.com/Automattic/mongoose/issues/3738
[9]: https://github.com/Automattic/mongoose/issues/2202
[10]: http://mongoosejs.com/docs/connections.html#buffering
[11]: https://mochajs.org/#hooks
[12]: https://github.com/Automattic/mongoose/tree/master/docs/faq.jade
[13]: https://github.com/blog/844-forking-with-the-edit-button


