### [Connection()][28]

##### Parameters

* base «Mongoose» a mongoose instance

Connection constructor

For practical reasons, a Connection equals a Db.

* * *

### [Connection.prototype.readyState][29]

Connection ready state

* 0 = disconnected
* 1 = connected
* 2 = connecting
* 3 = disconnecting

Each state change emits its associated event name.

#### Example


    conn.on('connected', callback);
    conn.on('disconnected', callback);

* * *

### [Connection.prototype.collections][30]

A hash of the collections associated with this connection

* * *

### [Connection.prototype.db][31]

The mongodb.Db instance, set when the connection is opened

* * *

### [Connection.prototype.config][32]

A hash of the global options that are associated with this connection

* * *

### [Connection.prototype.createCollection()][33]

##### Parameters

##### Returns:

* * *

### [Connection.prototype.dropCollection()][34]

##### Parameters

##### Returns:

Helper for `dropCollection()`. Will delete the given collection, including all documents and indexes.

* * *

### [Connection.prototype.dropDatabase()][35]

##### Parameters

##### Returns:

Helper for `dropDatabase()`. Deletes the given database, including all collections, documents, and indexes.

* * *

### [Connection.prototype.close()][36]

##### Parameters

* [callback] «Function» optional

##### Returns:

* * *

### [Connection.prototype.collection()][37]

##### Parameters

* [options] «Object» optional collection options

##### Returns:

* «Collection» collection instance

Retrieves a collection, creating it if not cached.

Not typically needed by applications. Just talk to your collection through your model.

* * *

### [Connection.prototype.model()][38]

##### Parameters

* [collection] «String» name of mongodb collection (optional) if not given it will be induced from model name

##### Returns:

* «Model» The compiled model

Defines or retrieves a model.


    var mongoose = require('mongoose');
    var db = mongoose.createConnection(..);
    db.model('Venue', new Schema(..));
    var Ticket = db.model('Ticket', new Schema(..));
    var Venue = db.model('Venue');

_When no `collection` argument is passed, Mongoose produces a collection name by passing the model `name` to the [utils.toCollectionName][39] method. This method pluralizes the name. If you don't like this behavior, either pass a collection name or set your schemas collection name option._

#### Example:


    var schema = new Schema({ name: String }, { collection: 'actor' });



    schema.set('collection', 'actor');



    var collectionName = 'actor'
    var M = conn.model('Actor', schema, collectionName)

* * *

### [Connection.prototype.modelNames()][40]

##### Returns:

Returns an array of model names created on this connection.

* * *