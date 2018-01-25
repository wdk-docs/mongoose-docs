
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