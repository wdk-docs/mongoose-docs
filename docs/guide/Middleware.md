# Mongoose v5.0.1: Middleware

## Middleware

[Source](http://mongoosejs.com/docs/middleware.html "Permalink to Mongoose v5.0.1: Middleware")

Middleware (also called pre and post _hooks_) are functions which are passed control during execution of asynchronous functions. Middleware is specified on the schema level and is useful for writing [plugins][1]. Mongoose 4.x has 4 types of middleware: document middleware, model middleware, aggregate middleware, and query middleware. Document middleware is supported for the following document functions. In document middleware functions, `this` refers to the document.

Query middleware is supported for the following Model and Query functions. In query middleware functions, `this` refers to the query.

Aggregate middleware is for `MyModel.aggregate()`. Aggregate middleware executes when you call `exec()` on an aggregate object. In aggregate middleware, `this` refers to the [aggregation object][2].

Model middleware is supported for the following model functions. In model middleware functions, `this` refers to the model.

All middleware types support pre and post hooks. How pre and post hooks work is described in more detail below.

**Note:** There is no query hook for `remove()`, only for documents. If you set a 'remove' hook, it will be fired when you call `myDoc.remove()`, not when you call `MyModel.remove()`. **Note:** The `create()` function fires `save()` hooks.

### Pre

There are two types of `pre` hooks, serial and parallel.

#### Serial

Serial middleware functions are executed one after another, when each middleware calls `next`.


    var schema = new Schema(..);
    schema.pre('save', function(next) {

      next();
    });


The `next()` call does **not** stop the rest of the code in your middleware function from executing. Use [the early `return` pattern][3] to prevent the rest of your middleware function from running when you call `next()`.


    var schema = new Schema(..);
    schema.pre('save', function(next) {
      if (foo()) {
        console.log('calling next!');

         next();
      }

      console.log('after next');
    });


#### Parallel

Parallel middleware offer more fine-grained flow control.


    var schema = new Schema(..);



    schema.pre('save', true, function(next, done) {

      next();
      setTimeout(done, 100);
    });


The hooked method, in this case `save`, will not be executed until `done` is called by each middleware.

#### Use Cases

Middleware are useful for atomizing model logic. Here are some other ideas:

* complex validation
* removing dependent documents (removing a user removes all his blogposts)
* asynchronous defaults
* asynchronous tasks that a certain action triggers

#### Error handling

If any middleware calls `next` or `done` with a parameter of type `Error`, the flow is interrupted, and the error is passed to the callback.


    schema.pre('save', function(next) {
      var err = new Error('something went wrong');
      next(err);
    });



    myDoc.save(function(err) {
      console.log(err.message)
    });


### Post middleware

[post][4] middleware are executed _after_ the hooked method and all of its `pre` middleware have completed.


    schema.post('init', function(doc) {
      console.log('%s has been initialized from the db', doc._id);
    });
    schema.post('validate', function(doc) {
      console.log('%s has been validated (but not saved yet)', doc._id);
    });
    schema.post('save', function(doc) {
      console.log('%s has been saved', doc._id);
    });
    schema.post('remove', function(doc) {
      console.log('%s has been removed', doc._id);
    });


### Asynchronous Post Hooks

If your post hook function takes at least 2 parameters, mongoose will assume the second parameter is a `next()` function that you will call to trigger the next middleware in the sequence.


    schema.post('save', function(doc, next) {
      setTimeout(function() {
        console.log('post1');

        next();
      }, 10);
    });


    schema.post('save', function(doc, next) {
      console.log('post2');
      next();
    });


### Save/Validate Hooks

The `save()` function triggers `validate()` hooks, because mongoose has a built-in `pre('save')` hook that calls `validate()`. This means that all `pre('validate')` and `post('validate')` hooks get called **before** any `pre('save')` hooks.


    schema.pre('validate', function() {
      console.log('this gets printed first');
    });
    schema.post('validate', function() {
      console.log('this gets printed second');
    });
    schema.pre('save', function() {
      console.log('this gets printed third');
    });
    schema.post('save', function() {
      console.log('this gets printed fourth');
    });


### Notes on findAndUpdate() and Query Middleware

Pre and post `save()` hooks are **not** executed on `update()`, `findOneAndUpdate()`, etc. You can see a more detailed discussion why in [this GitHub issue][5]. Mongoose 4.0 introduced distinct hooks for these functions.


    schema.pre('find', function() {
      console.log(this instanceof mongoose.Query);
      this.start = Date.now();
    });

    schema.post('find', function(result) {
      console.log(this instanceof mongoose.Query);

      console.log('find() returned ' + JSON.stringify(result));

      console.log('find() took ' + (Date.now() - this.start) + ' millis');
    });


Query middleware differs from document middleware in a subtle but important way: in document middleware, `this` refers to the document being updated. In query middleware, mongoose doesn't necessarily have a reference to the document being updated, so `this` refers to the **query** object rather than the document being updated.

For instance, if you wanted to add an `updatedAt` timestamp to every `update()` call, you would use the following pre hook.


    schema.pre('update', function() {
      this.update({},{ $set: { updatedAt: new Date() } });
    });


### Error Handling Middleware

_New in 4.5.0_

Middleware execution normally stops the first time a piece of middleware calls `next()` with an error. However, there is a special kind of post middleware called "error handling middleware" that executes specifically when an error occurs.

Error handling middleware is defined as middleware that takes one extra parameter: the 'error' that occurred as the first parameter to the function. Error handling middleware can then transform the error however you want.


    var schema = new Schema({
      name: {
        type: String,


        unique: true
      }
    });



    schema.post('save', function(error, doc, next) {
      if (error.name === 'MongoError' && error.code === 11000) {
        next(new Error('There was a duplicate key error'));
      } else {
        next(error);
      }
    });


    Person.create([{ name: 'Axl Rose' }, { name: 'Axl Rose' }]);


Error handling middleware also works with query middleware. You can also define a post `update()` hook that will catch MongoDB duplicate key errors.





    schema.post('update', function(error, res, next) {
      if (error.name === 'MongoError' && error.code === 11000) {
        next(new Error('There was a duplicate key error'));
      } else {
        next(error);
      }
    });

    var people = [{ name: 'Axl Rose' }, { name: 'Slash' }];
    Person.create(people, function(error) {
      Person.update({ name: 'Slash' }, { $set: { name: 'Axl Rose' } }, function(error) {

      });
    });


### Next Up

Now that we've covered middleware, let's take a look at Mongoose's approach to faking JOINs with its query [population][6] helper.

[1]: http://mongoosejs.com/plugins.html
[2]: http://mongoosejs.com/docs/api.html#model_Model.aggregate
[3]: https://www.bennadel.com/blog/2323-use-a-return-statement-when-invoking-callbacks-especially-in-a-guard-statement.htm
[4]: http://mongoosejs.com/docs/api.html#schema_Schema-post
[5]: http://github.com/Automattic/mongoose/issues/964
[6]: http://mongoosejs.com/docs/populate.html


