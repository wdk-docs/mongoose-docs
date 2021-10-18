
# 文档

## Document.prototype.schema

## Document.prototype.isNew

布尔标志指定文档

## Document.prototype.id

这个文件的字符串版本_id

**注释**

这个getter默认存在于所有的文件上。 The getter can be disabled by setting the `id` option of its `Schema` to false at construction time.


    new Schema({ name: String }, { id: false });

## Document.prototype.errors

Hash containing current validation errors.

## Document.prototype.init()

**参数**

* doc «Object» document returned by mongo

Initializes the document without setters or marking anything modified.

Called internally after a document is returned from mongodb.

## Document.prototype.update()

**参数**

**返回**

Sends an update command with this document `_id` as the query selector.

**示例**


    weirdCar.update({$inc: {wheels:1}}, { w: 1 }, callback);

**有效的选项**

## Document.prototype.$set()

**参数**

* options] «Object» optionally specify options that modify the behavior of the set

Alias for `set()`, used internally to avoid conflicts

## Document.prototype.set()

**参数**

* options] «Object» optionally specify options that modify the behavior of the set

Sets the value of a path, or many paths.

**示例**


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

## Document.prototype.get()

**参数**

* type] «Schema,String,Number,Buffer,*» optionally specify a type for on-the-fly attributes

Returns the value of a path.

**示例**


    doc.get('age')


    doc.get('age', String)

## Document.prototype.markModified()

**参数**

* scope] «Document» the scope to run validators with

Marks the path as having pending changes to write to the db.

_Very helpful when using Mixed types._

**示例**


    doc.mixed.type = 'changed';
    doc.markModified('mixed.type');
    doc.save()

## Document.prototype.unmarkModified()

**参数**

* path «String» the path to unmark modified

Clears the modified state on the specified path.

**示例**


    doc.foo = 'bar';
    doc.unmarkModified('foo');
    doc.save()

## Document.prototype.$ignore()

**参数**

* path «String» the path to ignore

Don't run validation on this path or persist changes to this path.

**示例**


    doc.foo = null;
    doc.$ignore('foo');
    doc.save()

## Document.prototype.modifiedPaths()

**返回**

Returns the list of paths that have been modified.

## Document.prototype.isModified()

**参数**

* path] «String» optional

**返回**

Returns true if this document was modified, else false.

If `path` is given, checks if a path or any full path containing `path` as part of its path chain has been modified.

**示例**


    doc.set('documents.0.title', 'changed');
    doc.isModified()
    doc.isModified('documents')
    doc.isModified('documents.0.title')
    doc.isModified('documents otherProp')
    doc.isDirectModified('documents')

## Document.prototype.$isDefault()

**参数**

**返回**

Checks if a path is set to its default.

**示例**


    MyModel = mongoose.model('test', { name: { type: String, default: 'Val '} });
    var m = new MyModel();
    m.$isDefault('name');

## Document.prototype.$isDeleted()

**参数**

* val] «Boolean» optional, overrides whether mongoose thinks the doc is deleted

**返回**

* «Boolean» whether mongoose thinks this doc is deleted.

Getter/setter, determines whether the document was removed or not.

**示例**


    product.remove(function (err, product) {
      product.isDeleted();
      product.remove();

      product.isDeleted(false);
      product.isDeleted();
      product.remove();
    })

## Document.prototype.isDirectModified()

**参数**

**返回**

Returns true if `path` was directly set and modified, else false.

**示例**


    doc.set('documents.0.title', 'changed');
    doc.isDirectModified('documents.0.title')
    doc.isDirectModified('documents')

## Document.prototype.isInit()

**参数**

**返回**

Checks if `path` was initialized.

## Document.prototype.isSelected()

**参数**

**返回**

Checks if `path` was selected in the source query which initialized this document.

**示例**


    Thing.findOne().select('name').exec(function (err, doc) {
       doc.isSelected('name')
       doc.isSelected('age')
    })

## Document.prototype.isDirectSelected()

**参数**

**返回**

Checks if `path` was explicitly selected. If no projection, always returns true.

**示例**


    Thing.findOne().select('nested.name').exec(function (err, doc) {
       doc.isDirectSelected('nested.name')
       doc.isDirectSelected('nested.otherName')
       doc.isDirectSelected('nested')
    })

## Document.prototype.validate()

**参数**

* callback «Function» optional callback called after validation completes, passing an error if one occurred

**返回**

Executes registered validation rules for this document.

**注释**

This method is called `pre` save and if a validation rule is violated, save is aborted and the error is returned to your `callback`.

**示例**


    doc.validate(function (err) {
      if (err) handleError(err);
      else
    });

## Document.prototype.validateSync()

**参数**

* pathsToValidate «Array,string» only validate the given paths

**返回**

* «MongooseError,undefined» MongooseError if there are errors during validation, or undefined if there is no error.

Executes registered validation rules (skipping asynchronous validators) for this document.

**注释**

This method is useful if you need synchronous validation.

**示例**


    var err = doc.validateSync();
    if ( err ){
      handleError( err );
    } else {

    }

## Document.prototype.invalidate()

**参数**

* kind] «String» optional `kind` property for the error

**返回**

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

## Document.prototype.$markValid()

**参数**

* path «String» the field to mark as valid

Marks a path as valid, removing existing validation errors.

## Document.prototype.save()

**参数**

* fn] «Function» optional callback

**返回**

Saves this document.

**示例**


    product.sold = Date.now();
    product.save(function (err, product, numAffected) {
      if (err) ..
    })

The callback will receive three parameters

1. `err` if an error occurred
2. `product` which is the saved `product`
3. `numAffected` will be 1 when the document was successfully persisted to MongoDB, otherwise 0. Unless you tweak mongoose's internals, you don't need to worry about checking this parameter for errors - checking `err` is sufficient to make sure your document was properly saved.

As an extra measure of flow control, save will return a Promise.

**示例**


    product.save().then(function(product) {
       ...
    });

## Document.prototype.toObject()

**参数**

**返回**

Converts this document into a plain javascript object, ready for storage in MongoDB.

Buffers are converted to instances of mongodb.Binary for proper storage.

**选项:**

* `getters` apply all getters (path and virtual getters)
* `virtuals` apply virtual getters (can override `getters` option)
* `minimize` remove empty objects (defaults to true)
* `transform` a transform function to apply to the resulting document before returning
* `depopulate` depopulate any populated paths, replacing them with their original refs (defaults to false)
* `versionKey` whether to include the version key (defaults to true)

**Getters/Virtuals**

Example of only applying path getters


    doc.toObject({ getters: true, virtuals: false })

Example of only applying virtual getters


    doc.toObject({ virtuals: true })

Example of applying both path and virtual getters


    doc.toObject({ getters: true })

To apply these options to every document of your schema by default, set your schemas `toObject` option to the same argument.


    schema.set('toObject', { virtuals: true })

**转变**

We may need to perform a transformation of the resulting object based on some criteria, say to remove some sensitive information or return a custom object. In this case we set the optional `transform` function.

Transform functions receive three arguments


    function (doc, ret, options) {}

* `doc` The mongoose document which is being converted
* `ret` The plain object representation which has been converted
* `options` The options in use (either schema options or the options passed inline)

**示例**


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
          delete retprop];
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

See schema options for some more details.

_During save, no custom options are applied to the document before being sent to the database._

## Document.prototype.toJSON()

**参数**

**返回**

The return value of this method is used in calls to JSON.stringify(doc).

This method accepts the same options as Document#toObject. To apply the options to every document of your schema by default, set your schemas `toJSON` option to the same argument.


    schema.set('toJSON', { virtuals: true })

See schema options for details.

## Document.prototype.inspect()

## Document.prototype.toString()

## Document.prototype.equals()

**参数**

* doc «Document» a document to compare

**返回**

Returns true if the Document stores the same data as doc.

Documents are considered equal when they have matching `_id`s, unless neither document has an `_id`, in which case this function falls back to using `deepEqual()`.

## Document.prototype.populate()

**参数**

* callback] «Function» When passed, population is invoked

**返回**

Populates document references, executing the `callback` when complete. If you want to use promises instead, use this function with `execPopulate()`

**示例**


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

**注释**

Population does not occur unless a `callback` is passed _or_ you explicitly call `execPopulate()`. Passing the same path a second time will overwrite the previous path options. See Model.populate() for explaination of options.

## Document.prototype.execPopulate()

**返回**

* «Promise» promise that resolves to the document when population is done

Explicitly executes population and returns a promise. Useful for ES2015 integration.

**示例**


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

## Document.prototype.populated()

**参数**

**返回**

* «Array,ObjectId,Number,Buffer,String,undefined»

Gets _id(s) used during population of the given `path`.

**示例**


    Model.findOne().populate('author').exec(function (err, doc) {
      console.log(doc.author.name)
      console.log(doc.populated('author'))
    })

If the path was not populated, undefined is returned.

## Document.prototype.depopulate()

**参数**

**返回**

Takes a populated field and returns it to its unpopulated state.

**示例**


    Model.findOne().populate('author').exec(function (err, doc) {
      console.log(doc.author.name);
      console.log(doc.depopulate('author'));
      console.log(doc.author);
    })

If the path was not populated, this is a no-op.