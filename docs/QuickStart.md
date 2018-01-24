# Mongoose v5.0.1: Getting Started

[Source](http://mongoosejs.com/docs/index.html "Permalink to Mongoose v5.0.1: Getting Started")

## Getting Started

_First be sure you have [MongoDB][1] and [Node.js][2] installed._

Next install Mongoose from the command line using `npm`:


    $ npm install mongoose


Now say we like fuzzy kittens and want to record every kitten we ever meet in MongoDB. The first thing we need to do is include mongoose in our project and open a connection to the `test` database on our locally running instance of MongoDB.


    var mongoose = require('mongoose');
    mongoose.connect('mongodb://localhost/test');


We have a pending connection to the test database running on localhost. We now need to get notified if we connect successfully or if a connection error occurs:


    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {

    });


Once our connection opens, our callback will be called. For brevity, let's assume that all following code is within this callback.

With Mongoose, everything is derived from a [Schema][3]. Let's get a reference to it and define our kittens.


    var kittySchema = mongoose.Schema({
      name: String
    });


So far so good. We've got a schema with one property, `name`, which will be a `String`. The next step is compiling our schema into a [Model][4].


    var Kitten = mongoose.model('Kitten', kittySchema);


A model is a class with which we construct documents. In this case, each document will be a kitten with properties and behaviors as declared in our schema. Let's create a kitten document representing the little guy we just met on the sidewalk outside:


    var silence = new Kitten({ name: 'Silence' });
    console.log(silence.name);


Kittens can meow, so let's take a look at how to add "speak" functionality to our documents:


    kittySchema.methods.speak = function () {
      var greeting = this.name
        ? "Meow name is " + this.name
        : "I don't have a name";
      console.log(greeting);
    }

    var Kitten = mongoose.model('Kitten', kittySchema);


Functions added to the `methods` property of a schema get compiled into the `Model` prototype and exposed on each document instance:


    var fluffy = new Kitten({ name: 'fluffy' });
    fluffy.speak();


We have talking kittens! But we still haven't saved anything to MongoDB. Each document can be saved to the database by calling its [save][5] method. The first argument to the callback will be an error if any occured.


      fluffy.save(function (err, fluffy) {
        if (err) return console.error(err);
        fluffy.speak();
      });


Say time goes by and we want to display all the kittens we've seen. We can access all of the kitten documents through our Kitten [model][4].


    Kitten.find(function (err, kittens) {
      if (err) return console.error(err);
      console.log(kittens);
    })


We just logged all of the kittens in our db to the console. If we want to filter our kittens by name, Mongoose supports MongoDBs rich [querying][6] syntax.


    Kitten.find({ name: /^fluff/ }, callback);


This performs a search for all documents with a name property that begins with "Fluff" and returns the result as an array of kittens to the callback.

### Congratulations

That's the end of our quick start. We created a schema, added a custom document method, saved and queried kittens in MongoDB using Mongoose. Head over to the [guide][7], or [API docs][8] for more.

[1]: http://www.mongodb.org/downloads
[2]: http://nodejs.org/
[3]: http://mongoosejs.com/docs/guide.html
[4]: http://mongoosejs.com/docs/models.html
[5]: http://mongoosejs.com/docs/api.html#model_Model-save
[6]: http://mongoosejs.com/docs/queries.html
[7]: http://mongoosejs.com/guide.html
[8]: http://mongoosejs.com/api.html


