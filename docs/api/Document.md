
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