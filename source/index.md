# 入门

<style>
  hr {
    display: block;
    margin-top: 40px;
    margin-bottom: 40px;
    border: 0px;
    height: 1px;
    background-color: #232323;
    width: 100%;
  }
</style>

_首先确保您已安装[mongodb](http://www.mongodb.org/downloads) 和 [Node.js](http://nodejs.org/)._

下一个使用`npm`从命令行安装猫鼬:

```
$ npm install mongoose --save
```

现在说我们喜欢模糊的小猫，并想录制我们在 Mongodb 遇到的每只小猫。
我们需要做的第一件事是在我们的项目中包引入 mongoose，并在我们本地运行的 MongoDB 实例上打开“test”数据库的连接。

```javascript
// getting-started.js
const mongoose = require("mongoose");

main().catch((err) => console.log(err));

async function main() {
  await mongoose.connect("mongodb://127.0.0.1:27017/test");

  // use `await mongoose.connect('mongodb://user:password@127.0.0.1:27017/test');` if your database has auth enabled
}
```

For brevity, let's assume that all following code is within the `main()` function.

使用猫鼬，一切都来自[Schema](guide.md).
让我们参考它并定义我们的小猫。

```javascript
const kittySchema = new mongoose.Schema({
  name: String,
});
```

So far so good. We've got a schema with one property, `name`, which will be a `String`. The next step is compiling our schema into a [Model](models.html).

```javascript
const Kitten = mongoose.model("Kitten", kittySchema);
```

A model is a class with which we construct documents.
In this case, each document will be a kitten with properties and behaviors as declared in our schema.
Let's create a kitten document representing the little guy we just met on the sidewalk outside:

```javascript
const silence = new Kitten({ name: "Silence" });
console.log(silence.name); // 'Silence'
```

Kittens can meow, so let's take a look at how to add "speak" functionality
to our documents:

```javascript
// NOTE: methods must be added to the schema before compiling it with mongoose.model()
kittySchema.methods.speak = function speak() {
  const greeting = this.name ? "Meow name is " + this.name : "I don't have a name";
  console.log(greeting);
};

const Kitten = mongoose.model("Kitten", kittySchema);
```

Functions added to the `methods` property of a schema get compiled into
the `Model` prototype and exposed on each document instance:

```javascript
const fluffy = new Kitten({ name: "fluffy" });
fluffy.speak(); // "Meow name is fluffy"
```

We have talking kittens! But we still haven't saved anything to MongoDB.
Each document can be saved to the database by calling its [save](api/model.html#model_Model-save) method. The first argument to the callback will be an error if any occurred.

```javascript
await fluffy.save();
fluffy.speak();
```

Say time goes by and we want to display all the kittens we've seen.
We can access all of the kitten documents through our Kitten [model](models.html).

```javascript
const kittens = await Kitten.find();
console.log(kittens);
```

We just logged all of the kittens in our db to the console.
If we want to filter our kittens by name, Mongoose supports MongoDBs rich [querying](queries.html) syntax.

```javascript
await Kitten.find({ name: /^fluff/ });
```

This performs a search for all documents with a name property that begins
with "fluff" and returns the result as an array of kittens to the callback.

## Congratulations

那是我们快速起步的终点。
我们创建了一个模式，添加了一种自定义文档方法，使用 Mongoose 在 MongoDB 中保存和查询小猫。
Head over to the [guide](guide.md), or [API docs](api/mongoose.md) for more.
