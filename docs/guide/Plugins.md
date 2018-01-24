# Mongoose v5.0.1: Plugins

## Plugins

[Source](http://mongoosejs.com/docs/plugins.html "Permalink to Mongoose v5.0.1: Plugins")

Schemas are pluggable, that is, they allow for applying pre-packaged capabilities to extend their functionality. This is a very powerful feature.

Suppose that we have several collections in our database and want to add last-modified functionality to each one. With plugins this is easy. Just create a plugin once and apply it to each `Schema`:


    module.exports = exports = function lastModifiedPlugin (schema, options) {
      schema.add({ lastMod: Date });

      schema.pre('save', function (next) {
        this.lastMod = new Date();
        next();
      });

      if (options && options.index) {
        schema.path('lastMod').index(options.index);
      }
    }


    var lastMod = require('./lastMod');
    var Game = new Schema({ ... });
    Game.plugin(lastMod, { index: true });


    var lastMod = require('./lastMod');
    var Player = new Schema({ ... });
    Player.plugin(lastMod);


We just added last-modified behavior to both our `Game` and `Player` schemas and declared an index on the `lastMod` path of our Games to boot. Not bad for a few lines of code.

### [Global Plugins][1]

Want to register a plugin for all schemas? The mongoose singleton has a `.plugin()` function that registers a plugin for every schema. For example:


    var mongoose = require('mongoose');
    mongoose.plugin(require('./lastMod'));

    var gameSchema = new Schema({ ... });
    var playerSchema = new Schema({ ... });

    var Game = mongoose.model('Game', gameSchema);
    var Player = mongoose.model('Player', playerSchema);


Not only can you re-use schema functionality in your own projects but you also reap the benefits of the Mongoose community as well. Any plugin published to [npm][2] and [tagged][3] with `mongoose` will show up on our [search results][4] page.

[1]: http://mongoosejs.com#global
[2]: https://npmjs.org/
[3]: https://npmjs.org/doc/tag.html
[4]: http://plugins.mongoosejs.io


