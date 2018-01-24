# Mongoose v5.0.1: MongoDB Version Compatibility

[Source](http://mongoosejs.com/docs/compatibility.html "Permalink to Mongoose v5.0.1: MongoDB Version Compatibility")

## MongoDB Server Version Compatibility

Mongoose relies on the [MongoDB Node.js Driver][1] to talk to MongoDB. You can refer to [this table][2] for up-to-date information as to which version of the MongoDB driver supports which version of MongoDB.

Below are the [semver][3] ranges representing which versions of mongoose are compatible with the listed versions of MongoDB server.

* MongoDB Server 2.4.x: mongoose `~3.8` or `4.x`
* MongoDB Server 2.6.x: mongoose `~3.8.8` or `4.x`
* MongoDB Server 3.0.x: mongoose `~3.8.22`, `4.x` or `5.x`
* MongoDB Server 3.2.x: mongoose `>=4.3.0` or `5.x`
* MongoDB Server 3.4.x: mongoose `>=4.7.3` or `5.x`
* MongoDB Server 3.6.x: mongoose `5.x`, or `>=4.11.0` with `useMongoClient` and `usePushEach`

[1]: http://mongodb.github.io/node-mongodb-native/
[2]: http://docs.mongodb.org/ecosystem/drivers/node-js/
[3]: http://semver.org/

