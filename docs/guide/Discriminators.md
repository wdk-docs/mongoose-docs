# Mongoose v5.0.1: Discriminators

[Source](http://mongoosejs.com/docs/discriminators.html "Permalink to Mongoose v5.0.1: Discriminators")

Discriminators are a schema inheritance mechanism. They enable you to have multiple models with overlapping schemas on top of the same underlying MongoDB collection.

Suppose you wanted to track different types of events in a single collection. Every event will have a timestamp, but events that represent clicked links should have a URL. You can achieve this using the `model.discriminator()` function. This function takes 2 parameters, a model name and a discriminator schema. It returns a model whose schema is the union of the base schema and the discriminator schema.


        var options = {discriminatorKey: 'kind'};

        var eventSchema = new mongoose.Schema({time: Date}, options);
        var Event = mongoose.model('Event', eventSchema);



        var ClickedLinkEvent = Event.discriminator('ClickedLink',
          new mongoose.Schema({url: String}, options));


        var genericEvent = new Event({time: Date.now(), url: 'google.com'});
        assert.ok(!genericEvent.url);


        var clickedEvent =
          new ClickedLinkEvent({time: Date.now(), url: 'google.com'});
        assert.ok(clickedEvent.url);



Suppose you created another discriminator to track events where a new user registered. These `SignedUpEvent` instances will be stored in the same collection as generic events and `ClickedLinkEvent` instances.


        var event1 = new Event({time: Date.now()});
        var event2 = new ClickedLinkEvent({time: Date.now(), url: 'google.com'});
        var event3 = new SignedUpEvent({time: Date.now(), user: 'testuser'});

        var save = function (doc, callback) {
          doc.save(function (error, doc) {
            callback(error, doc);
          });
        };

        async.map([event1, event2, event3], save, function (error) {

          Event.count({}, function (error, count) {
            assert.equal(count, 3);
          });
        });



The way mongoose tells the difference between the different discriminator models is by the 'discriminator key', which is `__t` by default. Mongoose adds a String path called `__t` to your schemas that it uses to track which discriminator this document is an instance of.


        var event1 = new Event({time: Date.now()});
        var event2 = new ClickedLinkEvent({time: Date.now(), url: 'google.com'});
        var event3 = new SignedUpEvent({time: Date.now(), user: 'testuser'});

        assert.ok(!event1.__t);
        assert.equal(event2.__t, 'ClickedLink');
        assert.equal(event3.__t, 'SignedUp');



Discriminator models are special; they attach the discriminator key to queries. In other words, `find()`, `count()`, `aggregate()`, etc. are smart enough to account for discriminators.


        var event1 = new Event({time: Date.now()});
        var event2 = new ClickedLinkEvent({time: Date.now(), url: 'google.com'});
        var event3 = new SignedUpEvent({time: Date.now(), user: 'testuser'});

        var save = function (doc, callback) {
          doc.save(function (error, doc) {
            callback(error, doc);
          });
        };

        async.map([event1, event2, event3], save, function (error) {

          ClickedLinkEvent.find({}, function (error, docs) {
            assert.equal(docs.length, 1);
            assert.equal(docs[0]._id.toString(), event2._id.toString());
            assert.equal(docs[0].url, 'google.com');
          });
        });



Discriminators also take their base schema's pre and post middleware. However, you can also attach middleware to the discriminator schema without affecting the base schema.


        var options = {discriminatorKey: 'kind'};

        var eventSchema = new mongoose.Schema({time: Date}, options);
        var eventSchemaCalls = 0;
        eventSchema.pre('validate', function (next) {
          ++eventSchemaCalls;
          next();
        });
        var Event = mongoose.model('GenericEvent', eventSchema);

        var clickedLinkSchema = new mongoose.Schema({url: String}, options);
        var clickedSchemaCalls = 0;
        clickedLinkSchema.pre('validate', function (next) {
          ++clickedSchemaCalls;
          next();
        });
        var ClickedLinkEvent = Event.discriminator('ClickedLinkEvent',
          clickedLinkSchema);

        var event1 = new ClickedLinkEvent();
        event1.validate(function() {
          assert.equal(eventSchemaCalls, 1);
          assert.equal(clickedSchemaCalls, 1);

          var generic = new Event();
          generic.validate(function() {
            assert.equal(eventSchemaCalls, 2);
            assert.equal(clickedSchemaCalls, 1);
          });
        });



A discriminator's fields are the union of the base schema's fields and the discriminator schema's fields, and the discriminator schema's fields take precedence. There is one exception: the default `_id` field.

You can work around this by setting the `_id` option to false in the discriminator schema as shown below.


        var options = {discriminatorKey: 'kind'};


        var eventSchema = new mongoose.Schema({_id: String, time: Date},
          options);
        var Event = mongoose.model('BaseEvent', eventSchema);

        var clickedLinkSchema = new mongoose.Schema({
          url: String,
          time: String
        }, options);


        assert.ok(clickedLinkSchema.path('_id'));
        assert.equal(clickedLinkSchema.path('_id').instance, 'ObjectID');
        var ClickedLinkEvent = Event.discriminator('ChildEventBad',
          clickedLinkSchema);

        var event1 = new ClickedLinkEvent({ _id: 'custom id', time: '4pm' });


        assert.ok(typeof event1._id === 'string');
        assert.ok(typeof event1.time === 'string');



When you use `Model.create()`, mongoose will pull the correct type from the discriminator key for you.


        var Schema = mongoose.Schema;
        var shapeSchema = new Schema({
          name: String
        }, { discriminatorKey: 'kind' });

        var Shape = db.model('Shape', shapeSchema);

        var Circle = Shape.discriminator('Circle',
          new Schema({ radius: Number }));
        var Square = Shape.discriminator('Square',
          new Schema({ side: Number }));

        var shapes = [
          { name: 'Test' },
          { kind: 'Circle', radius: 5 },
          { kind: 'Square', side: 10 }
        ];
        Shape.create(shapes, function(error, shapes) {
          assert.ifError(error);
          assert.ok(shapes[0] instanceof Shape);
          assert.ok(shapes[1] instanceof Circle);
          assert.equal(shapes[1].radius, 5);
          assert.ok(shapes[2] instanceof Square);
          assert.equal(shapes[2].side, 10);
        });



You can also define discriminators on embedded document arrays. Embedded discriminators are different because the different discriminator types are stored in the same document array (within a document) rather than the same collection. In other words, embedded discriminators let you store subdocuments matching different schemas in the same array.

As a general best practice, make sure you declare any hooks on your schemas **before** you use them. You should **not** call `pre()` or `post()` after calling `discriminator()`


        var eventSchema = new Schema({ message: String },
          { discriminatorKey: 'kind', _id: false });

        var batchSchema = new Schema({ events: [eventSchema] });


        var docArray = batchSchema.path('events');



        var clickedSchema = new Schema({
          element: {
            type: String,
            required: true
          }
        }, { _id: false });


        var Clicked = docArray.discriminator('Clicked', clickedSchema);


        var Purchased = docArray.discriminator('Purchased', new Schema({
          product: {
            type: String,
            required: true
          }
        }, { _id: false }));

        var Batch = db.model('EventBatch', batchSchema);


        var batch = {
          events: [
            { kind: 'Clicked', element: '#hero', message: 'hello' },
            { kind: 'Purchased', product: 'action-figure-1', message: 'world' }
          ]
        };

        Batch.create(batch).
          then(function(doc) {
            assert.equal(doc.events.length, 2);

            assert.equal(doc.events[0].element, '#hero');
            assert.equal(doc.events[0].message, 'hello');
            assert.ok(doc.events[0] instanceof Clicked);

            assert.equal(doc.events[1].product, 'action-figure-1');
            assert.equal(doc.events[1].message, 'world');
            assert.ok(doc.events[1] instanceof Purchased);

            doc.events.push({ kind: 'Purchased', product: 'action-figure-2' });
            return doc.save();
          }).
          then(function(doc) {
            assert.equal(doc.events.length, 3);

            assert.equal(doc.events[2].product, 'action-figure-2');
            assert.ok(doc.events[2] instanceof Purchased);

            done();
          }).
          catch(done);



Recursive embedded discriminators


        var singleEventSchema = new Schema({ message: String },
          { discriminatorKey: 'kind', _id: false });

        var eventListSchema = new Schema({ events: [singleEventSchema] });

        var subEventSchema = new Schema({
           sub_events: [singleEventSchema]
        }, { _id: false });

        var SubEvent = subEventSchema.path('sub_events').discriminator('SubEvent', subEventSchema)
        eventListSchema.path('events').discriminator('SubEvent', subEventSchema);

        var Eventlist = db.model('EventList', eventListSchema);


        var list = {
          events: [
            { kind: 'SubEvent', sub_events: [{kind:'SubEvent', sub_events:[], message:'test1'}], message: 'hello' },
            { kind: 'SubEvent', sub_events: [{kind:'SubEvent', sub_events:[{kind:'SubEvent', sub_events:[], message:'test3'}], message:'test2'}], message: 'world' }
          ]
        };

        Eventlist.create(list).
          then(function(doc) {
            assert.equal(doc.events.length, 2);

            assert.equal(doc.events[0].sub_events[0].message, 'test1');
            assert.equal(doc.events[0].message, 'hello');
            assert.ok(doc.events[0].sub_events[0] instanceof SubEvent);

            assert.equal(doc.events[1].sub_events[0].sub_events[0].message, 'test3');
            assert.equal(doc.events[1].message, 'world');
            assert.ok(doc.events[1].sub_events[0].sub_events[0] instanceof SubEvent);

            doc.events.push({kind:'SubEvent', sub_events:[{kind:'SubEvent', sub_events:[], message:'test4'}], message:'pushed'});
            return doc.save();
          }).
          then(function(doc) {
            assert.equal(doc.events.length, 3);

            assert.equal(doc.events[2].message, 'pushed');
            assert.ok(doc.events[2].sub_events[0] instanceof SubEvent);

            done();
          }).
          catch(done);




