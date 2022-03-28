# 模式

SchemaTypes handle definition of path
[defaults](./api.html#schematype_SchemaType-default),
[validation](./api.html#schematype_SchemaType-validate),
[getters](#getters),
[setters](./api.html#schematype_SchemaType-set),
[field selection defaults](./api.html#schematype_SchemaType-select) for
[queries](./api.html#query-js),and other general characteristics for Mongoose document properties.

## 什么是 SchemaType?

You can think of a Mongoose schema as the configuration object for a
Mongoose model. A SchemaType is then a configuration object for an individual
property. A SchemaType says what type a given
path should have, whether it has any getters/setters, and what values are
valid for that path.

```javascript
const schema = new Schema({ name: String });
schema.path("name") instanceof mongoose.SchemaType; // true
schema.path("name") instanceof mongoose.Schema.Types.String; // true
schema.path("name").instance; // 'String'
```

A SchemaType is different from a type. In other words, `mongoose.ObjectId !== mongoose.Types.ObjectId`.
A SchemaType is just a configuration object for Mongoose. An instance of
the `mongoose.ObjectId` SchemaType doesn't actually create MongoDB ObjectIds,
it is just a configuration for a path in a schema.

The following are all the valid SchemaTypes in Mongoose. Mongoose plugins
can also add custom SchemaTypes like [int32](http://plugins.mongoosejs.io/plugins/int32).
Check out [Mongoose's plugins search](http://plugins.mongoosejs.io) to find plugins.

- [String](#strings)
- [Number](#numbers)
- [Date](#dates)
- [Buffer](#buffers)
- [Boolean](#booleans)
- [Mixed](#mixed)
- [ObjectId](#objectids)
- [Array](#arrays)
- [Decimal128](./api.html#mongoose_Mongoose-Decimal128)
- [Map](#maps)
- [Schema](#schemas)

### 示例

```javascript
const schema = new Schema({
  name: String,
  binary: Buffer,
  living: Boolean,
  updated: { type: Date, default: Date.now },
  age: { type: Number, min: 18, max: 65 },
  mixed: Schema.Types.Mixed,
  _someId: Schema.Types.ObjectId,
  decimal: Schema.Types.Decimal128,
  array: [],
  ofString: [String],
  ofNumber: [Number],
  ofDates: [Date],
  ofBuffer: [Buffer],
  ofBoolean: [Boolean],
  ofMixed: [Schema.Types.Mixed],
  ofObjectId: [Schema.Types.ObjectId],
  ofArrays: [[]],
  ofArrayOfNumbers: [[Number]],
  nested: {
    stuff: { type: String, lowercase: true, trim: true },
  },
  map: Map,
  mapOfString: {
    type: Map,
    of: String,
  },
});

// example use

const Thing = mongoose.model("Thing", schema);

const m = new Thing();
m.name = "Statue of Liberty";
m.age = 125;
m.updated = new Date();
m.binary = Buffer.alloc(0);
m.living = false;
m.mixed = { any: { thing: "i want" } };
m.markModified("mixed");
m._someId = new mongoose.Types.ObjectId();
m.array.push(1);
m.ofString.push("strings!");
m.ofNumber.unshift(1, 2, 3, 4);
m.ofDates.addToSet(new Date());
m.ofBuffer.pop();
m.ofMixed = [1, [], "three", { four: 5 }];
m.nested.stuff = "good";
m.map = new Map([["key", "value"]]);
m.save(callback);
```

## `type` 键

`type` is a special property in Mongoose schemas. When Mongoose finds
a nested property named `type` in your schema, Mongoose assumes that
it needs to define a SchemaType with the given type.

```javascript
// 3 string SchemaTypes: 'name', 'nested.firstName', 'nested.lastName'
const schema = new Schema({
  name: { type: String },
  nested: {
    firstName: { type: String },
    lastName: { type: String },
  },
});
```

As a consequence, [you need a little extra work to define a property named `type` in your schema](/docs/faq.html#type-key).
For example, suppose you're building a stock portfolio app, and you
want to store the asset's `type` (stock, bond, ETF, etc.). Naively,
you might define your schema as shown below:

```javascript
const holdingSchema = new Schema({
  // You might expect `asset` to be an object that has 2 properties,
  // but unfortunately `type` is special in Mongoose so mongoose
  // interprets this schema to mean that `asset` is a string
  asset: {
    type: String,
    ticker: String,
  },
});
```

However, when Mongoose sees `type: String`, it assumes that you mean
`asset` should be a string, not an object with a property `type`.
The correct way to define an object with a property `type` is shown
below.

```javascript
const holdingSchema = new Schema({
  asset: {
    // Workaround to make sure Mongoose knows `asset` is an object
    // and `asset.type` is a string, rather than thinking `asset`
    // is a string.
    type: { type: String },
    ticker: String,
  },
});
```

## SchemaType 选项

You can declare a schema type using the type directly, or an object with
a `type` property.

```javascript
const schema1 = new Schema({
  test: String, // `test` is a path of type String
});

const schema2 = new Schema({
  // The `test` object contains the "SchemaType options"
  test: { type: String }, // `test` is a path of type string
});
```

In addition to the type property, you can specify additional properties
for a path. For example, if you want to lowercase a string before saving:

```javascript
const schema2 = new Schema({
  test: {
    type: String,
    lowercase: true, // Always convert `test` to lowercase
  },
});
```

You can add any property you want to your SchemaType options. Many plugins
rely on custom SchemaType options. For example, the [mongoose-autopopulate](http://plugins.mongoosejs.io/plugins/autopopulate)
plugin automatically populates paths if you set `autopopulate: true` in your
SchemaType options. Mongoose comes with support for several built-in
SchemaType options, like `lowercase` in the above example.

The `lowercase` option only works for strings. There are certain options
which apply for all schema types, and some that apply for specific schema
types.

### 模式选项

- `required`: boolean 或函数，如果为 true，则为该属性添加[需要验证器](./validation.html#built-in-validators)
- `default`: 任意或函数，为路径设置默认值。如果该值是一个函数，则默认使用该函数的返回值。
- `select`: boolean, 为查询指定默认的[投影](https://docs.mongodb.com/manual/tutorial/project-fields-from-query-results/)
- `validate`: 函数，为这个属性添加一个[验证器函数](./validation.html#built-in-validators)
- `get`: 函数，使用 [`Object.defineProperty()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 为该属性定义一个自定义 getter.
- `set`: 函数，使用 [`Object.defineProperty()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 为该属性定义自定义 setter.
- `alias`: 字符串，仅`mongoose >= 4.10.0`。用给定的名称定义一个[virtual](./guide.html#virtuals)来获取/设置这个路径。
- `immutable`: 布尔值，定义路径为不可变的。Mongoose 阻止你改变不可变的路径，除非父文档有`isNew: true`。
- `transform`: 当你调用[`Document#toJSON()`](/docs/api/document.html#document_Document-toJSON)函数时，包括当你[`JSON.stringify()`](https://thecodebarbarian.com/the-80-20-guide-to-json-stringify-in-javascript)一个文档时，Mongoose 调用这个函数。

```javascript
const numberSchema = new Schema({
  integerOnly: {
    type: Number,
    get: (v) => Math.round(v),
    set: (v) => Math.round(v),
    alias: "i",
  },
});

const Number = mongoose.model("Number", numberSchema);

const doc = new Number();
doc.integerOnly = 2.001;
doc.integerOnly; // 2
doc.i; // 2
doc.i = 3.001;
doc.integerOnly; // 3
doc.i; // 3
```

#### 索引 Indexes

You can also define [MongoDB indexes](https://docs.mongodb.com/manual/indexes/)
using schema type options.

- `index`: boolean, whether to define an [index](https://docs.mongodb.com/manual/indexes/) on this property.
- `unique`: boolean, whether to define a [unique index](https://docs.mongodb.com/manual/core/index-unique/) on this property.
- `sparse`: boolean, whether to define a [sparse index](https://docs.mongodb.com/manual/core/index-sparse/) on this property.

```javascript
const schema2 = new Schema({
  test: {
    type: String,
    index: true,
    unique: true, // Unique index. If you specify `unique: true`
    // specifying `index: true` is optional if you do `unique: true`
  },
});
```

#### 字符 String

- `lowercase`: boolean, whether to always call `.toLowerCase()` on the value
- `uppercase`: boolean, whether to always call `.toUpperCase()` on the value
- `trim`: boolean, whether to always call [`.trim()`](https://masteringjs.io/tutorials/fundamentals/trim-string) on the value
- `match`: RegExp, creates a [validator](./validation.html) that checks if the value matches the given regular expression
- `enum`: Array, creates a [validator](./validation.html) that checks if the value is in the given array.
- `minLength`: Number, creates a [validator](./validation.html) that checks if the value length is not less than the given number
- `maxLength`: Number, creates a [validator](./validation.html) that checks if the value length is not greater than the given number
- `populate`: Object, sets default [populate options](/docs/populate.html#query-conditions)

#### 数字 Number

- `min`: Number, creates a [validator](./validation.html) that checks if the value is greater than or equal to the given minimum.
- `max`: Number, creates a [validator](./validation.html) that checks if the value is less than or equal to the given maximum.
- `enum`: Array, creates a [validator](./validation.html) that checks if the value is strictly equal to one of the values in the given array.
- `populate`: Object, sets default [populate options](/docs/populate.html#query-conditions)

#### 日期 Date

- `min`: Date
- `max`: Date

#### 对象 Id ObjectId

- `populate`: Object, sets default [populate options](/docs/populate.html#query-conditions)

## 使用笔记

### String

To declare a path as a string, you may use either the `String` global
constructor or the string `'String'`.

```javascript
const schema1 = new Schema({ name: String }); // name will be cast to string
const schema2 = new Schema({ name: "String" }); // Equivalent

const Person = mongoose.model("Person", schema2);
```

If you pass an element that has a `toString()` function, Mongoose will call it,
unless the element is an array or the `toString()` function is strictly equal to
`Object.prototype.toString()`.

```javascript
new Person({ name: 42 }).name; // "42" as a string
new Person({ name: { toString: () => 42 } }).name; // "42" as a string

// "undefined", will get a cast error if you `save()` this document
new Person({ name: { foo: 42 } }).name;
```

### Number

To declare a path as a number, you may use either the `Number` global
constructor or the string `'Number'`.

```javascript
const schema1 = new Schema({ age: Number }); // age will be cast to a Number
const schema2 = new Schema({ age: "Number" }); // Equivalent

const Car = mongoose.model("Car", schema2);
```

There are several types of values that will be successfully cast to a Number.

```javascript
new Car({ age: "15" }).age; // 15 as a Number
new Car({ age: true }).age; // 1 as a Number
new Car({ age: false }).age; // 0 as a Number
new Car({ age: { valueOf: () => 83 } }).age; // 83 as a Number
```

If you pass an object with a `valueOf()` function that returns a Number, Mongoose will
call it and assign the returned value to the path.

The values `null` and `undefined` are not cast.

NaN, strings that cast to NaN, arrays, and objects that don't have a `valueOf()` function
will all result in a [CastError](/docs/validation.html#cast-errors) once validated, meaning that it will not throw on initialization, only when validated.

### Dates

[Built-in `Date` methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) are [**not** hooked into](https://github.com/Automattic/mongoose/issues/1598) the mongoose change tracking logic which in English means that if you use a `Date` in your document and modify it with a method like `setMonth()`, mongoose will be unaware of this change and `doc.save()` will not persist this modification. If you must modify `Date` types using built-in methods, tell mongoose about the change with `doc.markModified('pathToYourDate')` before saving.

```javascript
const Assignment = mongoose.model("Assignment", { dueDate: Date });
Assignment.findOne(function (err, doc) {
  doc.dueDate.setMonth(3);
  doc.save(callback); // THIS DOES NOT SAVE YOUR CHANGE

  doc.markModified("dueDate");
  doc.save(callback); // works
});
```

### Buffer

To declare a path as a Buffer, you may use either the `Buffer` global
constructor or the string `'Buffer'`.

```javascript
const schema1 = new Schema({ binData: Buffer }); // binData will be cast to a Buffer
const schema2 = new Schema({ binData: "Buffer" }); // Equivalent

const Data = mongoose.model("Data", schema2);
```

Mongoose will successfully cast the below values to buffers.

```
const file1 = new Data({ binData: 'test'}); // {"type":"Buffer","data":[116,101,115,116]}
const file2 = new Data({ binData: 72987 }); // {"type":"Buffer","data":[27]}
const file4 = new Data({ binData: { type: 'Buffer', data: [1, 2, 3]}}); // {"type":"Buffer","data":[1,2,3]}
```

### Mixed

An "anything goes" SchemaType. Mongoose will not do any casting on mixed paths.
You can define a mixed path using `Schema.Types.Mixed` or by passing an empty
object literal. The following are equivalent.

```javascript
const Any = new Schema({ any: {} });
const Any = new Schema({ any: Object });
const Any = new Schema({ any: Schema.Types.Mixed });
const Any = new Schema({ any: mongoose.Mixed });
```

Since Mixed is a schema-less type, you can change the value to anything else you
like, but Mongoose loses the ability to auto detect and save those changes.
To tell Mongoose that the value of a Mixed type has changed, you need to
call `doc.markModified(path)`, passing the path to the Mixed type you just changed.

To avoid these side-effects, a [Subdocument](./subdocs.html) path may be used
instead.

```javascript
person.anything = { x: [3, 4, { y: "changed" }] };
person.markModified("anything");
person.save(); // Mongoose will save changes to `anything`.
```

### ObjectIds

An [ObjectId](https://docs.mongodb.com/manual/reference/method/ObjectId/)
is a special type typically used for unique identifiers. Here's how
you declare a schema with a path `driver` that is an ObjectId:

```javascript
const mongoose = require("mongoose");
const carSchema = new mongoose.Schema({ driver: mongoose.ObjectId });
```

`ObjectId` is a class, and ObjectIds are objects. However, they are
often represented as strings. When you convert an ObjectId to a string
using `toString()`, you get a 24-character hexadecimal string:

```javascript
const Car = mongoose.model("Car", carSchema);

const car = new Car();
car.driver = new mongoose.Types.ObjectId();

typeof car.driver; // 'object'
car.driver instanceof mongoose.Types.ObjectId; // true

car.driver.toString(); // Something like "5e1a0651741b255ddda996c4"
```

### Boolean

Booleans in Mongoose are [plain JavaScript booleans](https://www.w3schools.com/js/js_booleans.asp).
By default, Mongoose casts the below values to `true`:

- `true`
- `'true'`
- `1`
- `'1'`
- `'yes'`

Mongoose casts the below values to `false`:

- `false`
- `'false'`
- `0`
- `'0'`
- `'no'`

Any other value causes a [CastError](/docs/validation.html#cast-errors).
You can modify what values Mongoose converts to true or false using the
`convertToTrue` and `convertToFalse` properties, which are [JavaScript sets](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set).

```javascript
const M = mongoose.model("Test", new Schema({ b: Boolean }));
console.log(new M({ b: "nay" }).b); // undefined

// Set { false, 'false', 0, '0', 'no' }
console.log(mongoose.Schema.Types.Boolean.convertToFalse);

mongoose.Schema.Types.Boolean.convertToFalse.add("nay");
console.log(new M({ b: "nay" }).b); // false
```

### Arrays

Mongoose supports arrays of [SchemaTypes](./api.html#schema_Schema.Types)
and arrays of [subdocuments](./subdocs.html). Arrays of SchemaTypes are
also called _primitive arrays_, and arrays of subdocuments are also called
_document arrays_.

```javascript
const ToySchema = new Schema({ name: String });
const ToyBoxSchema = new Schema({
  toys: [ToySchema],
  buffers: [Buffer],
  strings: [String],
  numbers: [Number],
  // ... etc
});
```

Arrays are special because they implicitly have a default value of `[]` (empty array).

```javascript
const ToyBox = mongoose.model("ToyBox", ToyBoxSchema);
console.log(new ToyBox().toys); // []
```

To overwrite this default, you need to set the default value to `undefined`

```javascript
const ToyBoxSchema = new Schema({
  toys: {
    type: [ToySchema],
    default: undefined,
  },
});
```

Note: specifying an empty array is equivalent to `Mixed`. The following all create arrays of
`Mixed`:

```javascript
const Empty1 = new Schema({ any: [] });
const Empty2 = new Schema({ any: Array });
const Empty3 = new Schema({ any: [Schema.Types.Mixed] });
const Empty4 = new Schema({ any: [{}] });
```

### Maps

_在 Mongoose 5.1.0 中新增_

MongooseMap 是[JavaScript `Map` 类](http://thecodebarbarian.com/the-80-20-guide-to-maps-in-javascript.html)的子类.
在这些文档中，我们将交替使用术语`map`和`MongooseMap`。
在 Mongoose 中，映射是使用任意键创建嵌套文档的方式。

!!! note

    在 Mongoose Maps 中，键必须是字符串，以便在 MongoDB 中存储文档。

```js linenums="1" title="user.schema.js"
const userSchema = new Schema({
  // `socialMediaHandles` is a map whose values are strings. A map's
  // keys are always strings. You specify the type of values using `of`.
  socialMediaHandles: {
    type: Map,
    of: String,
  },
});

const User = mongoose.model("User", userSchema);
// Map { 'github' => 'vkarpov15', 'twitter' => '@code_barbarian' }
console.log(
  new User({
    socialMediaHandles: {
      github: "vkarpov15",
      twitter: "@code_barbarian",
    },
  }).socialMediaHandles
);
```

上面的例子并没有明确地将`github` or `twitter`声明为路径，
但是，由于`socialMediaHandles`是一个映射，你可以存储任意键/值对。
然而，由于`socialMediaHandles`是一个映射，你**必须**使用`.get()` 来获取键的值，而`.set()`来设置键的值。

```javascript
const user = new User({ socialMediaHandles: {} });

// Good
user.socialMediaHandles.set("github", "vkarpov15");
// Works too
user.set("socialMediaHandles.twitter", "@code_barbarian");
// Bad, the `myspace` property will **not** get saved
user.socialMediaHandles.myspace = "fail";

// 'vkarpov15'
console.log(user.socialMediaHandles.get("github"));
// '@code_barbarian'
console.log(user.get("socialMediaHandles.twitter"));
// undefined
user.socialMediaHandles.github;

// Will only save the 'github' and 'twitter' properties
user.save();
```

映射类型存储为[BSON 对象在 MongoDB](https://en.wikipedia.org/wiki/BSON#Data_types_and_syntax).
BSON 对象中的键是有序的，所以这意味着映射的[插入顺序](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map#Description)属性得到了维护。

Mongoose 支持一个特殊的`$*`语法来[填充](/docs/populate.html) 映射中的所有元素。
例如，假设你的`socialMediaHandles`地图包含一个`ref`:

```javascript
const userSchema = new Schema({
  socialMediaHandles: {
    type: Map,
    of: new Schema({
      handle: String,
      oauth: { type: ObjectId, ref: "OAuth" },
    }),
  },
});
const User = mongoose.model("User", userSchema);
```

为了填充每个`socialMediaHandles`条目的`oauth`属性，你应该在`socialMediaHandles.$*.oauth`上填充:

```javascript
const user = await User.findOne().populate("socialMediaHandles.$*.oauth");
```

## Getters

Getters are like virtuals for paths defined in your schema. For example,
let's say you wanted to store user profile pictures as relative paths and
then add the hostname in your application. Below is how you would structure
your `userSchema`:

```javascript
const root = "https://s3.amazonaws.com/mybucket";

const userSchema = new Schema({
  name: String,
  picture: {
    type: String,
    get: (v) => `${root}${v}`,
  },
});

const User = mongoose.model("User", userSchema);

const doc = new User({ name: "Val", picture: "/123.png" });
doc.picture; // 'https://s3.amazonaws.com/mybucket/123.png'
doc.toObject({ getters: false }).picture; // '/123.png'
```

Generally, you only use getters on primitive paths as opposed to arrays
or subdocuments. Because getters override what accessing a Mongoose path returns,
declaring a getter on an object may remove Mongoose change tracking for
that path.

```javascript
const schema = new Schema({
  arr: [{ url: String }],
});

const root = "https://s3.amazonaws.com/mybucket";

// Bad, don't do this!
schema.path("arr").get((v) => {
  return v.map((el) => Object.assign(el, { url: root + el.url }));
});

// Later
doc.arr.push({ key: String });
doc.arr[0]; // 'undefined' because every `doc.arr` creates a new array!
```

Instead of declaring a getter on the array as shown above, you should
declare a getter on the `url` string as shown below. If you need to declare
a getter on a nested document or array, be very careful!

```javascript
const schema = new Schema({
  arr: [{ url: String }],
});

const root = "https://s3.amazonaws.com/mybucket";

// Good, do this instead of declaring a getter on `arr`
schema.path("arr.0.url").get((v) => `${root}${v}`);
```

## Schemas

To declare a path as another [schema](./guide.html#definition),
set `type` to the sub-schema's instance.

To set a default value based on the sub-schema's shape, simply set a default value,
and the value will be cast based on the sub-schema's definition before being set
during document creation.

```javascript
const subSchema = new mongoose.Schema({
  // some schema definition here
});

const schema = new mongoose.Schema({
  data: {
    type: subSchema
    default: {}
  }
});
```

## 创建自定义类型

Mongoose can also be extended with [custom SchemaTypes](customschematypes.html). Search the
[plugins](http://plugins.mongoosejs.io)
site for compatible types like
[mongoose-long](https://github.com/aheckmann/mongoose-long),
[mongoose-int32](https://github.com/vkarpov15/mongoose-int32),
and
[other](https://github.com/aheckmann/mongoose-function)
[types](https://github.com/OpenifyIt/mongoose-types).

Read more about creating [custom SchemaTypes here](customschematypes.html).

## `schema.path()` 函数

The `schema.path()` function returns the instantiated schema type for a
given path.

```javascript
const sampleSchema = new Schema({ name: { type: String, required: true } });
console.log(sampleSchema.path("name"));
// Output looks like:
/**
 * SchemaString {
 *   enumValues: [],
 *   regExp: null,
 *   path: 'name',
 *   instance: 'String',
 *   validators: ...
 */
```

You can use this function to inspect the schema type for a given path,
including what validators it has and what the type is.

## 阅读更多

<ul>
  <li><a href="https://masteringjs.io/tutorials/mongoose/schematype">An Introduction to Mongoose SchemaTypes</a></li>
  <li><a href="https://kb.objectrocket.com/mongo-db/mongoose-schema-types-1418">Mongoose Schema Types</a></li>
</ul>
