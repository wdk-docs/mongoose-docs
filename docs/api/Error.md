
# 错误

## MongooseError()

**参数**

* msg «String» Error message

MongooseError constructor

## MongooseError.messages

The default built-in validator error messages.

## MongooseError.DocumentNotFoundError

An instance of this error class will be returned when `save()` fails because the underlying document was not found. The constructor takes one parameter, the conditions that mongoose passed to `update()` when trying to update the document.

## MongooseError.CastError

An instance of this error class will be returned when mongoose failed to cast a value.

## MongooseError.ValidationError

An instance of this error class will be returned when [validation failed.

## MongooseError.ValidatorError

A `ValidationError` has a hash of `errors` that contain individual `ValidatorError` instances

## MongooseError.VersionError

An instance of this error class will be returned when you call `save()` after the document in the database was changed in a potentially unsafe way. See the [`versionKey` option for more information.

## MongooseError.OverwriteModelError

## MongooseError.MissingSchemaError

Thrown when you try to access a model that has not been registered yet

## MongooseError.DivergentArrayError

An instance of this error will be returned if you used an array projection and then modified the array in an unsafe way.