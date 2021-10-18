# 连接到MongoDB

[Source](http://mongoosejs.com/docs/connections.html "Permalink to Mongoose v5.0.1: Connecting to MongoDB")

You can connect to MongoDB with the `mongoose.connect()` method.

    mongoose.connect('mongodb://localhost/myapp');

This is the minimum needed to connect the `myapp` database running locally on the default port (27017). If the local connection fails then try using 127.0.0.1 instead of localhost. Sometimes issues may arise when the local hostname has been changed.

You can also specify several more parameters in the `uri`:

    mongoose.connect('mongodb://username:password@host:port/database?options...');

See the [mongodb connection string spec][1] for more detail.

## [操作缓冲][2]

Mongoose lets you start using your models immediately, without waiting for mongoose to establish a connection to MongoDB.

    mongoose.connect('mongodb://localhost/myapp');
    var MyModel = mongoose.model('Test', new Schema({ name: String }));

    MyModel.findOne(function(error, result) {  });

That's because mongoose buffers model function calls internally. This buffering is convenient, but also a common source of confusion. Mongoose will _not_ throw any errors by default if you use a model without connecting.

    var MyModel = mongoose.model('Test', new Schema({ name: String }));

    MyModel.findOne(function(error, result) {  });

    setTimeout(function() {
      mongoose.connect('mongodb://localhost/myapp');
    }, 60000);

To disable buffering, turn off the [`bufferCommands` option on your schema][3]. If you have `bufferCommands` on and your connection is hanging, try turning `bufferCommands` off to see if you haven't opened a connection properly. You can also disable `bufferCommands` globally:

    mongoose.set('bufferCommands', false);

## [选项][4]

The `connect` method also accepts an `options` object which will be passed on to the underlying MongoDB driver.

    mongoose.connect(uri, options);

A full list of options can be found on the [MongoDB Node.js driver docs for `connect()`][5]. Mongoose passes options to the driver without modification, modulo three exceptions that are explained below.

* `bufferCommands` \- This is a mongoose-specific option (not passed to the MongoDB driver) that disables [mongoose's buffering mechanism][6]
* `user`/`pass` \- The username and password for authentication. These options are mongoose-specific, they are equivalent to the MongoDB driver's `auth.user` and `auth.password` options.
* `autoIndex` \- By default, mongoose will automatically build indexes defined in your schema when it connects. This is great for development, but not ideal for large production deployments, because index builds can cause performance degradation. If you set `autoIndex` to false, mongoose will not automatically build indexes for **any** model associated with this connection.

Below are some of the options that are important for tuning mongoose.

* `autoReconnect` \- The underlying MongoDB driver will automatically try to reconnect when it loses connection to MongoDB. Unless you are an extremely advanced user that wants to manage their own connection pool, do **not** set this option to `false`.
* `reconnectTries` \- If you're connected to a single server or mongos proxy (as opposed to a replica set), the MongoDB driver will try to reconnect every `reconnectInterval` milliseconds for `reconnectTries` times, and give up afterward. When the driver gives up, the mongoose connection emits a `reconnectFailed` event. This option does nothing for replica set connections.
* `reconnectInterval` \- See `reconnectTries`
* `promiseLibrary` \- sets the [underlying driver's promise library][7]
* `poolSize` \- The maximum number of sockets the MongoDB driver will keep open for this connection. By default, `poolSize` is 5. Keep in mind that, as of MongoDB 3.4, MongoDB only allows one operation per socket at a time, so you may want to increase this if you find you have a few slow queries that are blocking faster queries from proceeding.
* `bufferMaxEntries` \- The MongoDB driver also has its own buffering mechanism that kicks in when the driver is disconnected. Set this option to 0 and set `bufferCommands` to `false` on your schemas if you want your database operations to fail immediately when the driver is not connected, as opposed to waiting for reconnection.

Example:

    const options = {
      useMongoClient: true,
      autoIndex: false,
      reconnectTries: Number.MAX_VALUE,
      reconnectInterval: 500,
      poolSize: 10,

      bufferMaxEntries: 0
    };
    mongoose.connect(uri, options);

## [回调函数][8]

The `connect()` function also accepts a callback parameter and returns a [promise][9].

    mongoose.connect(uri, options, function(error) {

    });

    mongoose.connect(uri, options).then(
      () => {  },
      err => {  }
    );

## [连接字符串选项][10]

You can also specify driver options in your connection string as [parameters in the query string][11] portion of the URI. This only applies to options passed to the MongoDB driver. You **can't** set Mongoose-specific options like `bufferCommands` in the query string.

    mongoose.connect('mongodb://localhost:27017/test?connectTimeoutMS=1000&bufferCommands=false');

    mongoose.connect('mongodb://localhost:27017/test', {
      connectTimeoutMS: 1000

    });

The disadvantage of putting options in the query string is that query string options are harder to read. The advantage is that you only need a single configuration option, the URI, rather than separate options for `socketTimeoutMS`, `connectTimeoutMS`, etc. Best practice is to put options that likely differ between development and production, like `replicaSet` or `ssl`, in the connection string, and options that should remain constant, like `connectTimeoutMS` or `poolSize`, in the options object.

The MongoDB docs have a full list of [supported connection string options][12]

## [有关keepAlive的说明][13]

For long running applications, it is often prudent to enable `keepAlive` with a number of milliseconds. Without it, after some period of time you may start to see `"connection closed"` errors for what seems like no reason. If so, after [reading this][14], you may decide to enable `keepAlive`:

    mongoose.connect(uri, { keepAlive: 120 });

## [副本集连接][15]

To connect to a replica set you pass a comma delimited list of hosts to connect to rather than a single host.

    mongoose.connect('mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]' [, options]);

To connect to a single node replica set, specify the `replicaSet` option.

    mongoose.connect('mongodb://host1:port1/?replicaSet=rsName');

## [多mongos支持][16]

High availability over multiple `mongos` instances is also supported. Pass a connection string for your `mongos` instances and set the `mongos` option to `true`:

    mongoose.connect('mongodb://mongosA:27501,mongosB:27501', { mongos: true }, cb);

## [多个连接][17]

So far we've seen how to connect to MongoDB using Mongoose's default connection. At times we may need multiple connections open to Mongo, each with different read/write settings, or maybe just to different databases for example. In these cases we can utilize `mongoose.createConnection()` which accepts all the arguments already discussed and returns a fresh connection for you.

    const conn = mongoose.createConnection('mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]', options);

This [connection][18] object is then used to create and retrieve [models][19]. Models are **always** scoped to a single connection.

Mongoose creates a _default connection_ when you call `mongoose.connect()`. You can access the default connection using `mongoose.connection`.

## [连接池][20]

Each `connection`, whether created with `mongoose.connect` or `mongoose.createConnection` are all backed by an internal configurable connection pool defaulting to a maximum size of 5. Adjust the pool size using your connection options:

    mongoose.createConnection(uri, { poolSize: 4 });

    const uri = 'mongodb://localhost/test?poolSize=4';
    mongoose.createConnection(uri);

## [v5.x中的选项更改][21]

You may see the following deprecation warning if upgrading from 4.x to 5.x and you didn't use the `useMongoClient` option in 4.x:

    the server/replset/mongos options are deprecated, all their options are supported at the top level of the options object

In older version of the MongoDB driver you had to specify distinct options for server connections, replica set connections, and mongos connections:

    mongoose.connect(myUri, {
      server: {
        socketOptions: {
          socketTimeoutMS: 0,
          keepAlive: true
        },
        reconnectTries: 30
      },
      replset: {
        socketOptions: {
          socketTimeoutMS: 0,
          keepAlive: true
        },
        reconnectTries: 30
      },
      mongos: {
        socketOptions: {
          socketTimeoutMS: 0,
          keepAlive: true
        },
        reconnectTries: 30
      }
    });

In mongoose v5.x you can instead declare these options at the top level, without all that extra nesting. [Here's the list of all supported options][22].

    mongoose.connect(myUri, {
      socketTimeoutMS: 0,
      keepAlive: true,
      reconnectTries: 30
    });

## 接下来

现在我们已经涵盖了连接, 让我们来看看[模型][23].

[1]: http://docs.mongodb.org/manual/reference/connection-string/
[2]: http://mongoosejs.com#buffering
[3]: http://mongoosejs.com/docs/guide.html#bufferCommands
[4]: http://mongoosejs.com#options
[5]: http://mongodb.github.io/node-mongodb-native/2.2/api/MongoClient.html#connect
[6]: http://mongoosejs.com/docs/faq.html#callback_never_executes
[7]: http://mongodb.github.io/node-mongodb-native/2.1/api/MongoClient.html
[8]: http://mongoosejs.com#callback
[9]: http://mongoosejs.com/docs/promises.html
[10]: http://mongoosejs.com#connection-string-options
[11]: https://en.wikipedia.org/wiki/Query_string
[12]: https://docs.mongodb.com/manual/reference/connection-string/
[13]: http://mongoosejs.com#keepAlive
[14]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/overview.html
[15]: http://mongoosejs.com#replicaset_connections
[16]: http://mongoosejs.com#mongos_connections
[17]: http://mongoosejs.com#multiple_connections
[18]: http://mongoosejs.com/api.html#connection_Connection
[19]: http://mongoosejs.com/api.html#model_Model
[20]: http://mongoosejs.com/connection_pools
[21]: http://mongoosejs.com#v5-changes
[22]: http://mongodb.github.io/node-mongodb-native/2.2/api/MongoClient.html
[23]: http://mongoosejs.com/docs/models.html

