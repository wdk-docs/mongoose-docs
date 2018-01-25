# 入门

## 入门使用

[源](http://mongoosejs.com/docs/index.html "Mongoose v5.0.1的永久链接：入门")

_首先安装[MongoDB][1]和[Node.js][2]。_

接下来使用`npm`安装Mongoose：

```sh
    $ npm install mongoose
```

做个小猫模型，先连接本地数据库`test`.

```js
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/test');
```

添加错误通知，以及连接成功方法:

```js
    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {

    });
```

一旦我们的连接打开，我们的回调将被调用，为简洁起见，我们假设以下所有代码都位于此回调中。

定义小猫[模式][3].

```js
    var kittySchema = mongoose.Schema({
        name: String
    });
```

我们有定义了一个属性 `name`, 类型 `String`. 下一步是将我们的模式编译成一个[模型][4].

```js
    var Kitten = mongoose.model('Kitten', kittySchema);
```

模型是我们构造文档的类。 在这种情况下， 每个文档将成为一个小猫，其特性和行为在我们的模式中声明。 让我们创建一个小猫文档，代表我们刚刚在外面的人行道上遇到的小家伙：

```js
    var silence = new Kitten({ name: 'Silence' });
    console.log(silence.name);
```

小猫可以喵喵叫，那么让我们来看看如何在我们的文档中添加“speak”功能：

```js
    kittySchema.methods.speak = function () {
      var greeting = this.name
        ? "Meow name is " + this.name
        : "I don't have a name";
      console.log(greeting);
    }
    var Kitten = mongoose.model('Kitten', kittySchema);
```

添加到模式的“方法”属性的函数被编译到“模型”原型中，并显示在每个文档实例上：

```js
    var fluffy = new Kitten({ name: 'fluffy' });
    fluffy.speak();
```

我们有说话的小猫！但是我们还没有保存任何东西给MongoDB。 每个文档可以通过调用[save][5]方法保存到数据库中。 回调的第一个参数将是一个错误，如果有错误将显示出来。

```js
    fluffy.save(function (err, fluffy) {
    if (err) return console.error(err);
    fluffy.speak();
    });
```

说时间过去了，我们想要显示我们看到的所有小猫。 我们可以通过我们的小猫[模型][4]访问所有的小猫文档。

```js
    Kitten.find(function (err, kittens) {
      if (err) return console.error(err);
      console.log(kittens);
    })
```

我们只把我们的数据库中的所有小猫记录到控制台。 如果我们想按名字过滤我们的小猫，Mongoose支持MongoDB丰富的[查询][6]语法。

```js
    Kitten.find({ name: /^fluff/ }, callback);
```

这将搜索名称属性以“Fluff”开头的所有文档，并将结果作为小猫数组返回给回调。

## 祝贺

这是我们快速启动的结束。,我们创建了一个模式，添加了一个自定义的文档方法，使用Mongoose在MongoDB中保存并查询了小猫。,请转到[guide][7]或[API文档][8]了解更多信息。

[1]: http://www.mongodb.org/downloads
[2]: http://nodejs.org/
[3]: http://mongoosejs.com/docs/guide.html
[4]: http://mongoosejs.com/docs/models.html
[5]: http://mongoosejs.com/docs/api.html#model_Model-save
[6]: http://mongoosejs.com/docs/queries.html
[7]: http://mongoosejs.com/guide.html
[8]: http://mongoosejs.com/api.html