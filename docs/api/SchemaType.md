
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