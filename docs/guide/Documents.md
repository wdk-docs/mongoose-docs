# Mongoose v5.0.1: Documents

## Documents

[Source](http://mongoosejs.com/docs/documents.html "Permalink to Mongoose v5.0.1: Documents")

Mongoose [documents][1] represent a one-to-one mapping to documents as stored in MongoDB. Each document is an instance of its [Model][2].

### Retrieving

There are many ways to retrieve documents from MongoDB. We won't cover that in this section. See the chapter on [querying][3] for detail.

### Updating

There are a number of ways to update documents. We'll first look at a traditional approach using [findById][4]:


    Tank.findById(id, function (err, tank) {
      if (err) return handleError(err);

      tank.size = 'large';
      tank.save(function (err, updatedTank) {
        if (err) return handleError(err);
        res.send(updatedTank);
      });
    });


You can also use [`.set()`][5] to modify documents. Under the hood, `tank.size = 'large';` becomes `tank.set({ size: 'large' })`.


    Tank.findById(id, function (err, tank) {
      if (err) return handleError(err);

      tank.set({ size: 'large' });
      tank.save(function (err, updatedTank) {
        if (err) return handleError(err);
        res.send(updatedTank);
      });
    });


This approach involves first retrieving the document from Mongo, then issuing an update command (triggered by calling `save`). However, if we don't need the document returned in our application and merely want to update a property in the database directly, [Model#update][6] is right for us:


    Tank.update({ _id: id }, { $set: { size: 'large' }}, callback);


If we do need the document returned in our application there is another, often [better][7], option:


    Tank.findByIdAndUpdate(id, { $set: { size: 'large' }}, { new: true }, function (err, tank) {
      if (err) return handleError(err);
      res.send(tank);
    });


The `findAndUpdate/Remove` static methods all make a change to at most one document, and return it with just one call to the database. There [are][8] [several][9] [variations][10] on the [findAndModify][11] theme. Read the [API][12] docs for more detail.

_Note that `findAndUpdate/Remove` do _not_ execute any hooks or validation before making the change in the database. You can use the [`runValidators` option][13] to access a limited subset of document validation. However, if you need hooks and full document validation, first query for the document and then `save()` it._

### Validating

Documents are validated before they are saved. Read the [api][14] docs or the [validation][15] chapter for detail.

### Overwriting

You can overwrite an entire document using `.set()`. This is handy if you want to change what document is being saved in [middleware][16].


    Tank.findById(id, function (err, tank) {
      if (err) return handleError(err);

      otherTank.set(tank);
    });


### Next Up

Now that we've covered Documents, let's take a look at [Sub-documents][17].

[1]: http://mongoosejs.com/api.html#document-js
[2]: http://mongoosejs.com/models.html
[3]: http://mongoosejs.com/queries.html
[4]: http://mongoosejs.com/api.html#model_Model.findById
[5]: http://mongoosejs.com/docs/api.html#document_Document-set
[6]: http://mongoosejs.com/api.html#model_Model.update
[7]: http://mongoosejs.com/api.html#model_Model.findByIdAndUpdate
[8]: http://mongoosejs.com/api.html#model_Model.findByIdAndRemove
[9]: http://mongoosejs.com/api.html#model_Model.findOneAndUpdate
[10]: http://mongoosejs.com/api.html#model_Model.findOneAndRemove
[11]: http://www.mongodb.org/display/DOCS/findAndModify+Command
[12]: http://mongoosejs.com/api.html
[13]: http://mongoosejs.com/docs/validation.html#update-validators
[14]: http://mongoosejs.com/api.html#document_Document-validate
[15]: http://mongoosejs.com/validation.html
[16]: http://mongoosejs.com/docs/middleware.html
[17]: http://mongoosejs.com/docs/subdocs.html


