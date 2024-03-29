子文档是嵌入在其他文档中的文档。
在 Mongoose 中，这意味着您可以在其他模式中嵌套模式。
Mongoose 有两个不同的子文档概念:[子文档数组](https://masteringjs.io/tutorials/mongoose/array#document-arrays)和单个嵌套子文档。

```javascript
const childSchema = new Schema({ name: "string" });

const parentSchema = new Schema({
  // Array of subdocuments
  children: [childSchema],
  // Single nested subdocuments.
Caveat: single nested subdocs only work
  // in mongoose >= 4.2.0
  child: childSchema,
});
```

除了代码重用之外，使用子文档的一个重要原因是创建一个路径，否则就无法对一组字段进行验证(例如 `dateRange.fromDate <= dateRange.toDate`)。

## 什么是子文档?

子文档类似于普通文档。
嵌套模式可以有[middleware](./middleware.html)、[自定义验证逻辑](./validation.html)、virtual，以及顶级模式可以使用的任何其他特性。
主要的区别是子文档不是单独保存的，它们是在它们的顶级父文档保存时保存的。

```javascript
const Parent = mongoose.model("Parent", parentSchema);
const parent = new Parent({ children: [{ name: "Matt" }, { name: "Sarah" }] });
parent.children[0].name = "Matthew";

// `parent.children[0].save()` is a no-op, it triggers middleware but
// does **not** actually save the subdocument.
You need to save the parent
// doc.
parent.save(callback);
```

和顶级文档一样，子文档也有 `save` 和 `validate` [middleware](./middleware.html)。
在父文档上调用 `save()` 会为它的所有子文档触发 `save()` 中间件，在 `validate()` 中间件上也是如此。

```javascript
childSchema.pre("save", function (next) {
  if ("invalid" == this.name) {
    return next(new Error("#sadpanda"));
  }
  next();
});

const parent = new Parent({ children: [{ name: "invalid" }] });
parent.save(function (err) {
  console.log(err.message); // #sadpanda
});
```

子文档 `pre('save')` 和 `pre('validate')` 中间件在顶级文档的 `pre('save')` **之前**执行，在顶级文档的 `pre('validate')` **之后**执行。
这是因为在 `save()` 之前进行验证实际上是内置中间件的一部分。

```javascript
// Below code will print out 1-4 in order
const childSchema = new mongoose.Schema({ name: "string" });

childSchema.pre("validate", function (next) {
  console.log("2");
  next();
});

childSchema.pre("save", function (next) {
  console.log("3");
  next();
});

const parentSchema = new mongoose.Schema({
  child: childSchema,
});

parentSchema.pre("validate", function (next) {
  console.log("1");
  next();
});

parentSchema.pre("save", function (next) {
  console.log("4");
  next();
});
```

## 子文档与嵌套路径

在 Mongoose 中，嵌套路径与子文档略有不同。
例如，下面是两个模式:一个是将 `child` 作为子文档，另一个是将 `child` 作为嵌套路径。

```javascript
// 子文档
const subdocumentSchema = new mongoose.Schema({
  child: new mongoose.Schema({ name: String, age: Number }),
});
const Subdoc = mongoose.model("Subdoc", subdocumentSchema);

// 嵌套路径
const nestedSchema = new mongoose.Schema({
  child: { name: String, age: Number },
});
const Nested = mongoose.model("Nested", nestedSchema);
```

这两个模式看起来很相似，MongoDB 中的文档将具有与这两个模式相同的结构。
但 Mongoose 也有一些特定的差异:

首先，`Nested` 的实例从来没有`child === undefined`。
你总是可以设置 `child` 的子属性，即使你没有设置 `child` 属性。
但 `Subdoc` 的实例可以有`child === undefined` 。

```javascript
const doc1 = new Subdoc({});
doc1.child === undefined; // true
doc1.child.name = "test"; // Throws TypeError: cannot read property...

const doc2 = new Nested({});
doc2.child === undefined; // false
console.log(doc2.child); // Prints 'MongooseDocument { undefined }'
doc2.child.name = "test"; // Works
```

## 子文档的默认

默认情况下，子文档路径是未定义的，除非您将子文档路径设置为非空值，否则 Mongoose 不会应用子文档默认值。

```javascript
const subdocumentSchema = new mongoose.Schema({
  child: new mongoose.Schema({
    name: String,
    age: {
      type: Number,
      default: 0,
    },
  }),
});
const Subdoc = mongoose.model("Subdoc", subdocumentSchema);

// 注意' age '默认值没有影响，因为' child '是' undefined '。
const doc = new Subdoc();
doc.child; // undefined
```

然而，如果你将 `doc.child` 设置为任何对象，Mongoose 将在必要时应用 `age` 默认值。

```javascript
doc.child = {};
// Mongoose应用默认的`age` :
doc.child.age; // 0
```

Mongoose 递归地应用默认值，这意味着如果您想确保 Mongoose 应用子文档默认值，有一个很好的解决方案:将子文档路径设置为空对象的默认值。

```javascript
const childSchema = new mongoose.Schema({
  name: String,
  age: {
    type: Number,
    default: 0,
  },
});
const subdocumentSchema = new mongoose.Schema({
  child: {
    type: childSchema,
    default: () => ({}),
  },
});
const Subdoc = mongoose.model("Subdoc", subdocumentSchema);

// 请注意，Mongoose将 `age` 设置为默认值0，因为 `child` 默认为空对象，而Mongoose将默认值应用于该空对象。
const doc = new Subdoc();
doc.child; // { age: 0 }
```

## 找到一个子文档

默认情况下，每个子文档都有一个 `_id`。
Mongoose 文档数组有一个特殊的 [id](./api.html#types_documentarray_MongooseDocumentArray-id) 方法来搜索文档数组，以找到一个给定的 `_id` 文档。

```javascript
const doc = parent.children.id(_id);
```

## 向数组添加子文档

MongooseArray 方法，如
[push](./api.html#mongoosearray_MongooseArray-push)，
[unshift](./api.html#mongoosearray_MongooseArray-unshift)，
[addToSet](./api.html#mongoosearray_MongooseArray-addToSet)，
以及其他一些方法透明地将参数转换为正确的类型:

```javascript
const Parent = mongoose.model("Parent");
const parent = new Parent();

// 创建一个评论
parent.children.push({ name: "Liesl" });
const subdoc = parent.children[0];
console.log(subdoc); // { _id: '501d86090d371bab2c0341c5', name: 'Liesl' }
subdoc.isNew; // true

parent.save(function (err) {
  if (err) return handleError(err);
  console.log("Success!");
});
```

你也可以使用 Document Arrays 的[`create()` method](./api/mongoosedocumentarray.html#mongoosedocumentarray_MongooseDocumentArray-create)创建子文档，而不将其添加到数组中。

```javascript
const newdoc = parent.children.create({ name: "Aaron" });
```

## 删除子文档

每个子文档都有自己的[remove](./api.html#types_embedded_EmbeddedDocument-remove)方法。
对于数组子文档，这相当于在子文档上调用`.pull()`。
对于单个嵌套子文档，`remove()`相当于将子文档设置为`null`。

```javascript
// 相当于 `parent.children.pull(_id)`
parent.children.id(_id).remove();
// 相当于 `parent.child = null`
parent.child.remove();
parent.save(function (err) {
  if (err) return handleError(err);
  console.log("the subdocs were removed");
});
```

<h3 id="subdoc-parents">子文档父</h3>

有时，您需要获取子文档的父文档。
你可以使用 `parent()` 函数来访问父类。

```javascript
const schema = new Schema({
  docArr: [{ name: String }],
  singleNested: new Schema({ name: String }),
});
const Model = mongoose.model("Test", schema);

const doc = new Model({
  docArr: [{ name: "foo" }],
  singleNested: { name: "bar" },
});

doc.singleNested.parent() === doc; // true
doc.docArr[0].parent() === doc; // true
```

如果你有一个深度嵌套的子文档，你可以使用 `ownerDocument()` 函数访问顶级文档。

```javascript
const schema = new Schema({
  level1: new Schema({
    level2: new Schema({
      test: String,
    }),
  }),
});
const Model = mongoose.model("Test", schema);

const doc = new Model({ level1: { level2: "test" } });

doc.level1.level2.parent() === doc; // false
doc.level1.level2.parent() === doc.level1; // true
doc.level1.level2.ownerDocument() === doc; // true
```

<h4 id="altsyntaxarrays"><a href="#altsyntaxarrays">数组的替代声明语法</a></h4>

如果你创建了一个包含对象数组的模式，Mongoose 会自动将该对象转换为一个模式:

```javascript
const parentSchema = new Schema({
  children: [{ name: "string" }],
});
// 等效
const parentSchema = new Schema({
  children: [new Schema({ name: "string" })],
});
```

<h4 id="altsyntaxsingle"><a href="#altsyntaxsingle">单嵌套子文档的可选声明语法</a></h4>

与文档数组不同，Mongoose 5 不会将模式中的对象转换为嵌套模式。
在下面的例子中， `nested` 是一个 _nested path_ 而不是子文档。

```javascript
const schema = new Schema({
  nested: {
    prop: String,
  },
});
```

当您试图用验证器或 getter/setter 定义嵌套路径时，这会导致一些令人惊讶的行为。

```javascript
const schema = new Schema({
  nested: {
    // 不要这样做!这使得“嵌套”在Mongoose 5中成为一个混合路径
    type: { prop: String },
    required: true,
  },
});

const schema = new Schema({
  nested: {
    // 这是正确的
    type: new Schema({ prop: String }),
    required: true,
  },
});
```

## 接下来

现在我们已经讨论了子文档，让我们看看[查询](./queries.html).
