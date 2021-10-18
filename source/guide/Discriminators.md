# 鉴别器

[资源](http://mongoosejs.com/docs/discriminators.html "Mongoose v5.0.1的永久链接：判别者")

鉴别器是一个模式继承机制。 它们使您能够在相同的基础MongoDB集合之上拥有多个具有重叠模式的模型。

假设您想要在单个集合中跟踪不同类型的事件。 每个事件都会有一个时间戳，但代表点击链接​​的事件应该有一个URL。 你可以使用`model.discriminator（）`函数实现这个功能。 该功能需要2个参数， 一个模型名称和一个鉴别器模式 它返回一个模式，其模式是基本模式和鉴别器模式的联合。

```js
  var options = {discriminatorKey: 'kind'};
  var eventSchema = new mongoose.Schema({time: Date}, options);
  var Event = mongoose.model('Event', eventSchema);
  var ClickedLinkEvent = Event.discriminator('ClickedLink', new mongoose.Schema({url: String}, options));
  var genericEvent = new Event({time: Date.now(), url: 'google.com'});
  assert.ok(!genericEvent.url);
  var clickedEvent = new ClickedLinkEvent({time: Date.now(), url: 'google.com'});
  assert.ok(clickedEvent.url);
```

假设您创建了另一个鉴别器来跟踪新用户注册的事件。这些`SignedUpEvent`实例将被存储在与通用事件和`ClickedLinkEvent`实例相同的集合中。

```js
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
```

猫鼬的方式告诉不同的鉴别器模型之间的差异是由“鉴别钥匙”, 默认是`__t`。 ,Mongoose将一个名为`__t`的String路径添加到您的模式中，用于跟踪该文档是哪个鉴别器的实例。

```js
  var event1 = new Event({time: Date.now()});
  var event2 = new ClickedLinkEvent({time: Date.now(), url: 'google.com'});
  var event3 = new SignedUpEvent({time: Date.now(), user: 'testuser'});

  assert.ok(!event1.__t);
  assert.equal(event2.__t, 'ClickedLink');
  assert.equal(event3.__t, 'SignedUp');
```

辨别器模型是特殊的;他们将鉴别键附加到查询。 换一种说法， `find()`, `count()`, `aggregate()`, 等等都足够聪明来解释鉴别者。

```js
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
```

鉴别者也采取他们的基本模式的前后中间件。 但是，您也可以将中间件附加到鉴别器模式，而不会影响基本模式。

```js
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
```

鉴别器的字段是基本模式字段和鉴别器模式字段的联合， 并且鉴别器模式的字段优先。,有一个例外：默认的`_id`字段。

您可以通过在鉴别器模式中将_id选项设置为false来解决这个问题，如下所示。

```js
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
```

当你使用`Model.create（）`时，猫鼬会从你的鉴别器键中取出正确的类型。

```js
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
```

您也可以在嵌入式文档数组上定义鉴别器。 嵌入式鉴别器是不同的，因为不同的鉴别器类型存储在相同的文档数组（在文档中）而不是相同的集合。换句话说，嵌入式鉴别器可让您将与不同模式匹配的子文档存储在同一个数组中。

作为一个通用的最佳实践，确保你在使用它们之前，在模式**上声明任何钩子。 在调用discriminator（）之后，你不应该调用`pre（）`或`post（）`

```js
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
```

嵌入式递归鉴别器

```js
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
```