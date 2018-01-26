# 验证

[Source](http://mongoosejs.com/docs/validation.html "Permalink to Mongoose v5.0.1: Validation")

## 验证语法规则

* 验证在[SchemaType][1]中定义
* 验证是[中间件][2]. 在默认情况下，Mongoose将验证注册为每个模式的“pre”（'save'）`钩子。
* 您可以使用`doc.validate（callback）`或`doc.validateSync（）`手动运行验证
* 验证器不在未定义的值上运行。 唯一的例外是[`required` validator] [3]。
* 验证是异步递归的; 当你调用[Model＃save][4], 子文档验证也被执行. 如果发生错误，你的[Model＃save][4]回调接收到它
* 验证是可定制的

```js
  var schema = new Schema({
    name: {
      type: String,
      required: true
    }
  });
  var Cat = db.model('Cat', schema);

  var cat = new Cat();
  cat.save(function(error) {
    assert.equal(error.errors['name'].message,
      'Path `name` is required.');

    error = cat.validateSync();
    assert.equal(error.errors['name'].message,
      'Path `name` is required.');
  });
```

## 内置验证器

* 所有的`SchemaTypes`都有内置的`required`验证器. 所需的验证程序使用`SchemaType`的`checkRequired()`函数来确定该值是否满足所需的验证程序
* `Numbers` 有 `min` 和 `max` 验证器.
* `Strings` 有 `enum`, `match`, `maxlength` 何 `minlength` 验证器.

上面的每个验证器链接都提供了有关如何启用它们并自定义错误消息的更多信息.

```js
  var breakfastSchema = new Schema({
    eggs: {
      type: Number,
      min: [6, 'Too few eggs'],
      max: 12
    },
    bacon: {
      type: Number,
      required: [true, 'Why no bacon?']
    },
    drink: {
      type: String,
      enum: ['Coffee', 'Tea'],
      required: function() {
        return this.bacon > 3;
      }
    }
  });
  var Breakfast = db.model('Breakfast', breakfastSchema);

  var badBreakfast = new Breakfast({
    eggs: 2,
    bacon: 0,
    drink: 'Milk'
  });
  var error = badBreakfast.validateSync();
  assert.equal(error.errors['eggs'].message,
    'Too few eggs');
  assert.ok(!error.errors['bacon']);
  assert.equal(error.errors['drink'].message,
    '`Milk` is not a valid enum value for path `drink`.');

  badBreakfast.bacon = 5;
  badBreakfast.drink = null;

  error = badBreakfast.validateSync();
  assert.equal(error.errors['drink'].message, 'Path `drink` is required.');

  badBreakfast.bacon = null;
  error = badBreakfast.validateSync();
  assert.equal(error.errors['bacon'].message, 'Why no bacon?');
```

## unique

对于初学者来说，一个常见的问题是模式的`unique`选项不是验证器。 这是构建[MongoDB独特索引][5]的便利帮手。 请参阅[常见问题][6]了解更多信息。

```js
  var uniqueUsernameSchema = new Schema({
    username: {
      type: String,
      unique: true
    }
  });
  var U1 = db.model('U1', uniqueUsernameSchema);
  var U2 = db.model('U2', uniqueUsernameSchema);

  var dup = [{ username: 'Val' }, { username: 'Val' }];
  U1.create(dup, function(error) {

  });

  U2.once('index', function(error) {
    assert.ifError(error);
    U2.create(dup, function(error) {

      assert.ok(error);
      assert.ok(!error.errors);
      assert.ok(error.message.indexOf('duplicate key error') !== -1);
    });
  });

  U2.init().then(function() {
    U2.create(dup, function(error) {

      assert.ok(error);
      assert.ok(!error.errors);
      assert.ok(error.message.indexOf('duplicate key error') !== -1);
    });
  });
```

## 自定义验证器

如果内置的验证器是不够的, 您可以定义自定义验证器以满足您的需求.

自定义验证是通过传递验证函数来声明的. 你可以找到详细的说明 关于如何在[SchemaType＃validate()API文档][7]中做到这一点.

```js
  var userSchema = new Schema({
    phone: {
      type: String,
      validate: {
        validator: function(v) {
          return /d{3}-d{3}-d{4}/.test(v);
        },
        message: '{VALUE} is not a valid phone number!'
      },
      required: [true, 'User phone number required']
    }
  });

  var User = db.model('user', userSchema);
  var user = new User();
  var error;

  user.phone = '555.0123';
  error = user.validateSync();
  assert.equal(error.errors['phone'].message,
    '555.0123 is not a valid phone number!');

  user.phone = '';
  error = user.validateSync();
  assert.equal(error.errors['phone'].message,
    'User phone number required');

  user.phone = '201-555-0123';

  error = user.validateSync();
  assert.equal(error, null);
```

### 回调

自定义验证器也可以是异步的。如果你的验证器函数有两个参数，猫鼬会假设第二个参数是一个回调。

即使你不想使用异步验证器，也要小心，因为mongoose 4会假设**所有带有2个参数的函数都是异步的，比如[`validator.isEmail`函数][8]。 这种行为从4.9.0开始被视为弃用，您可以通过在自定义验证器上指定“isAsync：false”来关闭它。

```js
  var userSchema = new Schema({
    phone: {
      type: String,
      validate: {

        isAsync: true,
        validator: function(v, cb) {
          setTimeout(function() {
            var phoneRegex = /d{3}-d{3}-d{4}/;
            var msg = v + ' is not a valid phone number!';

            cb(phoneRegex.test(v), msg);
          }, 5);
        },

        message: 'Default error message'
      },
      required: [true, 'User phone number required']
    },
    name: {
      type: String,

      validate: function(v) {
        return new Promise(function(resolve, reject) {
          setTimeout(function() {
            resolve(false);
          }, 5);
        });
      }
    }
  });

  var User = db.model('User', userSchema);
  var user = new User();
  var error;

  user.phone = '555.0123';
  user.name = 'test';
  user.validate(function(error) {
    assert.ok(error);
    assert.equal(error.errors['phone'].message,
      '555.0123 is not a valid phone number!');
    assert.equal(error.errors['name'].message,
      'Validator failed for path `name` with value `test`');
  });
```

### 返回错误

验证失败后返回的错误包含`error`对象，其值是`ValidatorError`对象。 每个[ValidatorError] [9]都有`kind`，`path`，`value`, 和 `message` 属性. 一个`ValidatorError`也可能有一个`reason`属性。 如果在验证器中发生错误， 这个属性将包含抛出的错误。

```js
  var toySchema = new Schema({
    color: String,
    name: String
  });

  var validator = function(value) {
    return /red|white|gold/i.test(value);
  };
  toySchema.path('color').validate(validator,
    'Color `{VALUE}` not valid', 'Invalid color');
  toySchema.path('name').validate(function(v) {
    if (v !== 'Turbo Man') {
      throw new Error('Need to get a Turbo Man for Christmas');
    }
    return true;
  }, 'Name `{VALUE}` is not valid');

  var Toy = db.model('Toy', toySchema);

  var toy = new Toy({ color: 'Green', name: 'Power Ranger' });

  toy.save(function (err) {

    assert.equal(err.errors.color.message, 'Color `Green` not valid');
    assert.equal(err.errors.color.kind, 'Invalid color');
    assert.equal(err.errors.color.path, 'color');
    assert.equal(err.errors.color.value, 'Green');

    assert.equal(err.errors.name.message,
      'Need to get a Turbo Man for Christmas');
    assert.equal(err.errors.name.value, 'Power Ranger');

    assert.equal(err.errors.name.reason.message,
      'Need to get a Turbo Man for Christmas');

    assert.equal(err.name, 'ValidationError');
  });
```

### 嵌套对象

在猫鼬的嵌套对象定义验证是棘手的， 因为嵌套的对象不是完全成熟的路径。

```js
  var personSchema = new Schema({
    name: {
      first: String,
      last: String
    }
  });

  assert.throws(function() {

    personSchema.path('name').required(true);
  }, /Cannot.*'required'/);

  var nameSchema = new Schema({
    first: String,
    last: String
  });

  personSchema = new Schema({
    name: {
      type: nameSchema,
      required: true
    }
  });

  var Person = db.model('Person', personSchema);

  var person = new Person();
  var error = person.validateSync();
  assert.ok(error.errors['name']);
```

在上面的例子中，您了解了文档验证。 Mongoose also supports validation for `update()` and `findOneAndUpdate()` operations. In Mongoose 4.x, update validators are off by default - you need to specify the `runValidators` option.

要打开更新验证器，为`update（）`或`findOneAndUpdate（）`设置`runValidators`选项。 小心：更新验证器默认关闭，因为他们有几个警告。

```js
  var toySchema = new Schema({
    color: String,
    name: String
  });

  var Toy = db.model('Toys', toySchema);

  Toy.schema.path('color').validate(function (value) {
    return /blue|green|white|red|orange|periwinkle/i.test(value);
  }, 'Invalid color');

  var opts = { runValidators: true };
  Toy.update({}, { color: 'bacon' }, opts, function (err) {
    assert.equal(err.errors.color.message,
      'Invalid color');
  });
```

更新验证器和文档验证器之间有几个关键的区别。 在上面的颜色验证功能中， `this`是指在使用文档验证时正在验证的文档。 但是，当运行更新验证器时， 正在更新的文档可能不在服务器的内存中，所以默认情况下“this”的值没有被定义。

```js
  var toySchema = new Schema({
    color: String,
    name: String
  });

  toySchema.path('color').validate(function(value) {

    if (this.name.toLowerCase().indexOf('red') !== -1) {
      return value !== 'red';
    }
    return true;
  });

  var Toy = db.model('ActionFigure', toySchema);

  var toy = new Toy({ color: 'red', name: 'Red Power Ranger' });
  var error = toy.validateSync();
  assert.ok(error.errors['color']);

  var update = { color: 'red', name: 'Red Power Ranger' };
  var opts = { runValidators: true };

  Toy.update({}, update, opts, function(error) {

    assert.ok(error);
  });
```

### context

`context`选项可以让你在更新验证器中将`this`的值设置为底层查询。

```js
  toySchema.path('color').validate(function(value) {

    if (this.getUpdate().$set.name.toLowerCase().indexOf('red') !== -1) {
      return value === 'red';
    }
    return true;
  });

  var Toy = db.model('Figure', toySchema);

  var update = { color: 'blue', name: 'Red Power Ranger' };

  var opts = { runValidators: true, context: 'query' };

  Toy.update({}, update, opts, function(error) {
    assert.ok(error.errors['color']);
  });
```

### $unset

更新验证程序的另一个关键区别仅在更新中指定的路径上运行。 例如，在下面的例子中，因为更新操作中没有指定“name”，所以更新验证将成功。

当使用更新验证器时，只有当您试图显式地使用`$unset`时，所需的验证器**失败。

```js
  var kittenSchema = new Schema({
    name: { type: String, required: true },
    age: Number
  });

  var Kitten = db.model('Kitten', kittenSchema);

  var update = { color: 'blue' };
  var opts = { runValidators: true };
  Kitten.update({}, update, opts, function(err) {

  });

  var unset = { $unset: { name: 1 } };
  Kitten.update({}, unset, opts, function(err) {

    assert.ok(err);
    assert.ok(err.errors['name']);
  });
```

最后一个细节值得注意：仅更新验证程序**运行在以下更新操作符上：

* `$set` * `$unset` * `$push` (>= 4.8.0) * `$addToSet` (>= 4.8.0) * `$pull` (>= 4.12.0) * `$pullAll` (>= 4.12.0)

例如，下面的更新会成功，不管`number`的值是什么，因为update验证器忽略了`$inc`。 另外，`$push`，`$addToSet`，`$pull`和`$pullAll`验证不会**在数组本身上运行任何验证， 只有数组的单个元素。

```js
  var testSchema = new Schema({
    number: { type: Number, max: 0 },
    arr: [{ message: { type: String, maxLength: 10 } }]
  });

  testSchema.path('arr').validate(function(v) {
    return v.length < 2;
  });

  var Test = db.model('Test', testSchema);

  var update = { $inc: { number: 1 } };
  var opts = { runValidators: true };
  Test.update({}, update, opts, function(error) {

    update = { $push: [{ message: 'hello' }, { message: 'world' }] };
    Test.update({}, update, opts, function(error) {

    });
  });
```

## `$push`和`$addToSet`

4.8.0中的新增功能：更新验证器也在`$push`和`$addToSet`上运行

```js
  var testSchema = new Schema({
    numbers: [{ type: Number, max: 0 }],
    docs: [{
      name: { type: String, required: true }
    }]
  });

  var Test = db.model('TestPush', testSchema);

  var update = {
    $push: {
      numbers: 1,
      docs: { name: null }
    }
  };
  var opts = { runValidators: true };
  Test.update({}, update, opts, function(error) {
    assert.ok(error.errors['numbers']);
    assert.ok(error.errors['docs']);
  });
```

[1]: http://mongoosejs.com/schematypes.html
[2]: http://mongoosejs.com/middleware.html
[3]: http://mongoosejs.com/api.html#schematype_SchemaType-required
[4]: http://mongoosejs.com/api.html#model_Model-save
[5]: https://docs.mongodb.com/manual/core/index-unique/
[6]: http://mongoosejs.com/docs/faq.html
[7]: http://mongoosejs.com/api.html#schematype_SchemaType-validate
[8]: https://www.npmjs.com/package/validator
[9]: http://mongoosejs.com/api.html#error-validation-js

