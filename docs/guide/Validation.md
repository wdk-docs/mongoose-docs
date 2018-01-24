# Validation

[Source](http://mongoosejs.com/docs/validation.html "Permalink to Mongoose v5.0.1: Validation")

Before we get into the specifics of validation syntax, please keep the following rules in mind:

* Validation is defined in the [SchemaType][1]
* Validation is [middleware][2]. Mongoose registers validation as a `pre('save')` hook on every schema by default.
* You can manually run validation using `doc.validate(callback)` or `doc.validateSync()`
* Validators are not run on undefined values. The only exception is the [`required` validator][3].
* Validation is asynchronously recursive; when you call [Model#save][4], sub-document validation is executed as well. If an error occurs, your [Model#save][4] callback receives it
* Validation is customizable


        var schema = new Schema({
          name: {
            type: String,
            required: true
          }
        });
        var Cat = db.model('Cat', schema);


        var cat = new Cat();
        cat.save(function(error) {
          assert.equal(error.errors['name'].message,
            'Path `name` is required.');

          error = cat.validateSync();
          assert.equal(error.errors['name'].message,
            'Path `name` is required.');
        });



Mongoose has several built-in validators.

Each of the validator links above provide more information about how to enable them and customize their error messages.


        var breakfastSchema = new Schema({
          eggs: {
            type: Number,
            min: [6, 'Too few eggs'],
            max: 12
          },
          bacon: {
            type: Number,
            required: [true, 'Why no bacon?']
          },
          drink: {
            type: String,
            enum: ['Coffee', 'Tea'],
            required: function() {
              return this.bacon > 3;
            }
          }
        });
        var Breakfast = db.model('Breakfast', breakfastSchema);

        var badBreakfast = new Breakfast({
          eggs: 2,
          bacon: 0,
          drink: 'Milk'
        });
        var error = badBreakfast.validateSync();
        assert.equal(error.errors['eggs'].message,
          'Too few eggs');
        assert.ok(!error.errors['bacon']);
        assert.equal(error.errors['drink'].message,
          '`Milk` is not a valid enum value for path `drink`.');

        badBreakfast.bacon = 5;
        badBreakfast.drink = null;

        error = badBreakfast.validateSync();
        assert.equal(error.errors['drink'].message, 'Path `drink` is required.');

        badBreakfast.bacon = null;
        error = badBreakfast.validateSync();
        assert.equal(error.errors['bacon'].message, 'Why no bacon?');



A common gotcha for beginners is that the `unique` option for schemas is _not_ a validator. It's a convenient helper for building [MongoDB unique indexes][5]. See the [FAQ][6] for more information.


        var uniqueUsernameSchema = new Schema({
          username: {
            type: String,
            unique: true
          }
        });
        var U1 = db.model('U1', uniqueUsernameSchema);
        var U2 = db.model('U2', uniqueUsernameSchema);

        var dup = [{ username: 'Val' }, { username: 'Val' }];
        U1.create(dup, function(error) {


        });



        U2.once('index', function(error) {
          assert.ifError(error);
          U2.create(dup, function(error) {


            assert.ok(error);
            assert.ok(!error.errors);
            assert.ok(error.message.indexOf('duplicate key error') !== -1);
          });
        });




        U2.init().then(function() {
          U2.create(dup, function(error) {


            assert.ok(error);
            assert.ok(!error.errors);
            assert.ok(error.message.indexOf('duplicate key error') !== -1);
          });
        });



If the built-in validators aren't enough, you can define custom validators to suit your needs.

Custom validation is declared by passing a validation function. You can find detailed instructions on how to do this in the [`SchemaType#validate()` API docs][7].


        var userSchema = new Schema({
          phone: {
            type: String,
            validate: {
              validator: function(v) {
                return /d{3}-d{3}-d{4}/.test(v);
              },
              message: '{VALUE} is not a valid phone number!'
            },
            required: [true, 'User phone number required']
          }
        });

        var User = db.model('user', userSchema);
        var user = new User();
        var error;

        user.phone = '555.0123';
        error = user.validateSync();
        assert.equal(error.errors['phone'].message,
          '555.0123 is not a valid phone number!');

        user.phone = '';
        error = user.validateSync();
        assert.equal(error.errors['phone'].message,
          'User phone number required');

        user.phone = '201-555-0123';


        error = user.validateSync();
        assert.equal(error, null);



Custom validators can also be asynchronous. If your validator function takes 2 arguments, mongoose will assume the 2nd argument is a callback.

Even if you don't want to use asynchronous validators, be careful, because mongoose 4 will assume that **all** functions that take 2 arguments are asynchronous, like the [`validator.isEmail` function][8]. This behavior is considered deprecated as of 4.9.0, and you can shut it off by specifying `isAsync: false` on your custom validator.


        var userSchema = new Schema({
          phone: {
            type: String,
            validate: {



              isAsync: true,
              validator: function(v, cb) {
                setTimeout(function() {
                  var phoneRegex = /d{3}-d{3}-d{4}/;
                  var msg = v + ' is not a valid phone number!';


                  cb(phoneRegex.test(v), msg);
                }, 5);
              },

              message: 'Default error message'
            },
            required: [true, 'User phone number required']
          },
          name: {
            type: String,


            validate: function(v) {
              return new Promise(function(resolve, reject) {
                setTimeout(function() {
                  resolve(false);
                }, 5);
              });
            }
          }
        });

        var User = db.model('User', userSchema);
        var user = new User();
        var error;

        user.phone = '555.0123';
        user.name = 'test';
        user.validate(function(error) {
          assert.ok(error);
          assert.equal(error.errors['phone'].message,
            '555.0123 is not a valid phone number!');
          assert.equal(error.errors['name'].message,
            'Validator failed for path `name` with value `test`');
        });



Errors returned after failed validation contain an `errors` object whose values are `ValidatorError` objects. Each [ValidatorError][9] has `kind`, `path`, `value`, and `message` properties. A ValidatorError also may have a `reason` property. If an error was thrown in the validator, this property will contain the error that was thrown.


        var toySchema = new Schema({
          color: String,
          name: String
        });

        var validator = function(value) {
          return /red|white|gold/i.test(value);
        };
        toySchema.path('color').validate(validator,
          'Color `{VALUE}` not valid', 'Invalid color');
        toySchema.path('name').validate(function(v) {
          if (v !== 'Turbo Man') {
            throw new Error('Need to get a Turbo Man for Christmas');
          }
          return true;
        }, 'Name `{VALUE}` is not valid');

        var Toy = db.model('Toy', toySchema);

        var toy = new Toy({ color: 'Green', name: 'Power Ranger' });

        toy.save(function (err) {


          assert.equal(err.errors.color.message, 'Color `Green` not valid');
          assert.equal(err.errors.color.kind, 'Invalid color');
          assert.equal(err.errors.color.path, 'color');
          assert.equal(err.errors.color.value, 'Green');




          assert.equal(err.errors.name.message,
            'Need to get a Turbo Man for Christmas');
          assert.equal(err.errors.name.value, 'Power Ranger');


          assert.equal(err.errors.name.reason.message,
            'Need to get a Turbo Man for Christmas');

          assert.equal(err.name, 'ValidationError');
        });



Defining validators on nested objects in mongoose is tricky, because nested objects are not fully fledged paths.


        var personSchema = new Schema({
          name: {
            first: String,
            last: String
          }
        });

        assert.throws(function() {

          personSchema.path('name').required(true);
        }, /Cannot.*'required'/);


        var nameSchema = new Schema({
          first: String,
          last: String
        });

        personSchema = new Schema({
          name: {
            type: nameSchema,
            required: true
          }
        });

        var Person = db.model('Person', personSchema);

        var person = new Person();
        var error = person.validateSync();
        assert.ok(error.errors['name']);



In the above examples, you learned about document validation. Mongoose also supports validation for `update()` and `findOneAndUpdate()` operations. In Mongoose 4.x, update validators are off by default - you need to specify the `runValidators` option.

To turn on update validators, set the `runValidators` option for `update()` or `findOneAndUpdate()`. Be careful: update validators are off by default because they have several caveats.


        var toySchema = new Schema({
          color: String,
          name: String
        });

        var Toy = db.model('Toys', toySchema);

        Toy.schema.path('color').validate(function (value) {
          return /blue|green|white|red|orange|periwinkle/i.test(value);
        }, 'Invalid color');

        var opts = { runValidators: true };
        Toy.update({}, { color: 'bacon' }, opts, function (err) {
          assert.equal(err.errors.color.message,
            'Invalid color');
        });



There are a couple of key differences between update validators and document validators. In the color validation function above, `this` refers to the document being validated when using document validation. However, when running update validators, the document being updated may not be in the server's memory, so by default the value of `this` is not defined.


        var toySchema = new Schema({
          color: String,
          name: String
        });

        toySchema.path('color').validate(function(value) {



          if (this.name.toLowerCase().indexOf('red') !== -1) {
            return value !== 'red';
          }
          return true;
        });

        var Toy = db.model('ActionFigure', toySchema);

        var toy = new Toy({ color: 'red', name: 'Red Power Ranger' });
        var error = toy.validateSync();
        assert.ok(error.errors['color']);

        var update = { color: 'red', name: 'Red Power Ranger' };
        var opts = { runValidators: true };

        Toy.update({}, update, opts, function(error) {




          assert.ok(error);
        });



The `context` option lets you set the value of `this` in update validators to the underlying query.


        toySchema.path('color').validate(function(value) {


          if (this.getUpdate().$set.name.toLowerCase().indexOf('red') !== -1) {
            return value === 'red';
          }
          return true;
        });

        var Toy = db.model('Figure', toySchema);

        var update = { color: 'blue', name: 'Red Power Ranger' };

        var opts = { runValidators: true, context: 'query' };

        Toy.update({}, update, opts, function(error) {
          assert.ok(error.errors['color']);
        });



The other key difference that update validators only run on the paths specified in the update. For instance, in the below example, because 'name' is not specified in the update operation, update validation will succeed.

When using update validators, `required` validators **only** fail when you try to explicitly `$unset` the key.


        var kittenSchema = new Schema({
          name: { type: String, required: true },
          age: Number
        });

        var Kitten = db.model('Kitten', kittenSchema);

        var update = { color: 'blue' };
        var opts = { runValidators: true };
        Kitten.update({}, update, opts, function(err) {

        });

        var unset = { $unset: { name: 1 } };
        Kitten.update({}, unset, opts, function(err) {

          assert.ok(err);
          assert.ok(err.errors['name']);
        });



One final detail worth noting: update validators **only** run on the following update operators:
* `$set` * `$unset` * `$push` (>= 4.8.0) * `$addToSet` (>= 4.8.0) * `$pull` (>= 4.12.0) * `$pullAll` (>= 4.12.0)

For instance, the below update will succeed, regardless of the value of `number`, because update validators ignore `$inc`. Also, `$push`, `$addToSet`, `$pull`, and `$pullAll` validation does **not** run any validation on the array itself, only individual elements of the array.


        var testSchema = new Schema({
          number: { type: Number, max: 0 },
          arr: [{ message: { type: String, maxLength: 10 } }]
        });



        testSchema.path('arr').validate(function(v) {
          return v.length < 2;
        });

        var Test = db.model('Test', testSchema);

        var update = { $inc: { number: 1 } };
        var opts = { runValidators: true };
        Test.update({}, update, opts, function(error) {

          update = { $push: [{ message: 'hello' }, { message: 'world' }] };
          Test.update({}, update, opts, function(error) {


          });
        });



New in 4.8.0: update validators also run on `$push` and `$addToSet`


        var testSchema = new Schema({
          numbers: [{ type: Number, max: 0 }],
          docs: [{
            name: { type: String, required: true }
          }]
        });

        var Test = db.model('TestPush', testSchema);

        var update = {
          $push: {
            numbers: 1,
            docs: { name: null }
          }
        };
        var opts = { runValidators: true };
        Test.update({}, update, opts, function(error) {
          assert.ok(error.errors['numbers']);
          assert.ok(error.errors['docs']);
        });



[1]: http://mongoosejs.com/schematypes.html
[2]: http://mongoosejs.com/middleware.html
[3]: http://mongoosejs.com/api.html#schematype_SchemaType-required
[4]: http://mongoosejs.com/api.html#model_Model-save
[5]: https://docs.mongodb.com/manual/core/index-unique/
[6]: http://mongoosejs.com/docs/faq.html
[7]: http://mongoosejs.com/api.html#schematype_SchemaType-validate
[8]: https://www.npmjs.com/package/validator
[9]: http://mongoosejs.com/api.html#error-validation-js


