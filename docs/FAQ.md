# 常见问题

[源](http://mongoosejs.com/docs/faq.html "Mongoose v5.0.1的永久链接：FAQ")

??? faq "当我直接更新数组元素时​​，为什么不更改？"

    ```js
    doc.array[3] = 'changed';
    doc.save();
    ```

    !!! info "答案"

        Mongoose不会为数组索引创建getter/setter; 没有他们猫鼬从来没有得到通知的变化，所以不知道坚持新的价值。 解决方法是使用** Mongoose> = 3.2.0 **中的[MongooseArray＃set][1]。

        ```js
        doc.array.set(3, 'changed');
        doc.save();
        doc.array[3] = 'changed';
        doc.markModified('array');
        doc.save();
        ```

??? faq "我定义模式属性为“unique”，但我仍然可以保存了重复数据,是什么导致的？"

    !!! info "答案"

        Mongoose本身并不处理`unique`：`{name：{type：String，unique：true}}`只是在`name`上创建一个[MongoDB唯一索引]的简写[2] 例如，如果MongoDB在`name`上没有唯一的索引， 尽管“unique”是真实的，但下面的代码不会出错。

        ```js
            var schema = new mongoose.Schema({
              name: { type: String, unique: true }
            });
            var Model = db.model('Test', schema);

            Model.create([{ name: 'Val' }, { name: 'Val' }], function(err) {
              console.log(err);
            });
        ```

        然而， 如果您使用`Model.on（'index'）`事件等待索引建立，尝试保存重复将会抛错

        ```js
            var schema = new mongoose.Schema({
              name: { type: String, unique: true }
            });
            var Model = db.model('Test', schema);

            Model.on('index', function(err) {
              assert.ifError(err);
              Model.create([{ name: 'Val' }, { name: 'Val' }], function(err) {
                console.log(err);
              });
            });

            Model.init().then(function() {
              assert.ifError(err);
              Model.create([{ name: 'Val' }, { name: 'Val' }], function(err) {
                console.log(err);
              });
            });
        ```

        MongoDB坚持索引， 所以你只需要重新建立索引，如果你开始一个新的数据库，或者你运行`db.dropDatabase（）`。在生产环境中， 你应该[使用MongoDB shell]）（）创建索引，而不是依靠猫鼬为你做。 模式的`unique`选项方便开发和文档化，但猫鼬不是索引管理解决方案

??? faq "当我在模式中有一个嵌套属性时，mongoose默认添加空对象,为什么？"

    ```js
    var schema = new mongoose.Schema({
      nested: {
        prop: String
      }
    });
    var Model = db.model('Test', schema);
    console.log(new Model());
    ```

    !!! info "答案"

        这是一个性能优化 这些空的对象不会保存到数据库中， 也不是在结果`toObject（）`中， 除非关闭[`minimize`选项] [3]，否则它们不会出现在`JSON.stringify（）`输出中。

        这种行为的原因是Mongoose的变化检测和getter / setter是基于[`Object.defineProperty（）`] [4]。为了支持对嵌套属性的更改检测，而不会在每次创建文档时招致运行Object.defineProperty（）的开销， mongoose在模型编译时定义`Model`原型的属性。,由于猫鼬需要为`nested.prop`定义getter和setter， 必须始终将“嵌套”定义为猫鼬文档上的对象， 即使`nested`在底层的[POJO] [3]上是未定义的。

??? faq "我在[virtual][5]，getter/setter或[method][6]使用箭头函数，this的值是为什么是错误的？"

    !!! info "答案"

        箭头函数[处理`this`关键字与传统函数大不相同][7]. Mongoose getter/setter依赖`this`来让你访问你正在写的文档，但是这个功能不能使用箭头函数。 除非不打算访问getter/setter中的文档，否则不要使用箭头函数来取得 Mongoose getter/setter。

        ```js
            var schema = new mongoose.Schema({
              propWithGetter: {
                type: String,
                get: v => {
                  console.log(this);
                  return v;
                }
              }
            });

            schema.method.arrowMethod = () => this;
            schema.virtual('virtualWithArrow').get(() => {
              console.log(this);
            });
        ```

??? faq "我有一个名为`type`的嵌入式属性是这样的："

    ```js
    const holdingSchema = new Schema({
      asset: {
        type: String,
        ticker: String
      }
    });
    ```

    但mongoose给了我一个CastError提示，当我试图用一个`asset`对象来保存`Holding`时，它不能把一个对象转换成一个字符串。

    ```js
      Holding.create({ asset: { type: 'stock', ticker: 'MDB' } }).catch(error => {
        console.error(error);
      });
    ```

    !!! info "答案"

        “类型”属性在猫鼬是特别的，所以当你说`type：String`时，猫鼬把它解释为一个类型声明. 在上面的模式中，猫鼬认为“asset”是一个字符串，而不是一个对象。,做这个，而不是：

        ```js
          const holdingSchema = new Schema({
            asset: {
              type: { type: String },
              ticker: String
            }
          });
        ```

??? faq "为什么不能对日期对象（例如`date.setMonth（1）;`）进行就地修改？"

    ```js
    doc.createdAt.setDate(2011, 5, 1);
    doc.save();
    ```

    !!! info "答案"

        Mongoose目前不会监视日期对象的就地更新,如果您需要这个功能，请随时在[此GitHub问题][8]上讨论。,有几个解决方法：

        ```js
            doc.createdAt.setDate(2011, 5, 1);
            doc.markModified('createdAt');
            doc.save();

            doc.createdAt = new Date(2011, 5, 1).setHours(4);
            doc.save();
        ```

??? faq "我在下面的代码中填充数组下的嵌套属性："

    ```js
      new Schema({
        arr: [{
          child: { ref: 'OtherModel', type: Schema.Types.ObjectId }
        }]
      });
    ```

    `.populate（{path：'arr.child'，options：{sort：'name'}}）`不会被`arr.child.name`排序

    !!! info "答案"

        见[这个GitHub问题] [9]。,这是一个已知的问题，但是这是一个非常难以解决的问题

??? faq "我的模型上的所有函数调用挂起，我做错了什么？"

    !!! info "答案"

        默认情况下，mongoose会缓冲你的函数调用，直到它可以连接到MongoDB。,阅读[连接文档的缓冲部分][10]了解更多信息。

??? faq "我怎样才能启用调试？"

    !!! info "答案"

        将`debug`选项设置为`true`

        ```js
          mongoose.set('debug', true)
        ```

        所有执行的收集方法都会将其参数的输出记录到控制台。

??? faq "我的`save（）`回调从不执行。 我究竟做错了什么？"

    !!! info "答案"

        所有的“集合”操作（插入，删除，查询等）都被排队，直到“连接”打开. 尝试连接时可能发生错误。尝试添加一个错误处理程序到您的连接。

        ```js
            mongoose.connect(..);
            mongoose.connection.on('error', handleError);

            var conn = mongoose.createConnection(..);
            conn.on('error', handleError);
        ```

        如果您想要在整个应用程序中选择不使用猫鼬的缓冲机制，请将全局`bufferCommands`选项设置为false

        ```js
            mongoose.set('bufferCommands', false);
        ```

??? faq "我应该为每个数据库操作创建/销毁一个新的连接吗？"

    !!! info "答案"

        不用。在应用程序启动时打开连接，并保持打开状态，直到应用程序关闭。

??? faq "为什么我得到"OverwriteModelError: Cannot overwrite .. model once compiled" 当我使用nodemon(一个测试框架)？"

    !!! info "答案"

        `mongoose.model('ModelName',schema)'要求`ModelName`是唯一的，
        所以你可以通过使用`mongoose.model('ModelName')`来访问模型。
        如果你把mongoose.model('ModelName',schema);`放在[mocha`beforeEach()`hook][11]中，
        此代码将尝试在**每**测试之前创建一个名为“ModelName”的新模型， 所以你会得到一个错误。
        确保你只用一个给定的名字*once*创建一个新的模型。
        如果您需要创建具有相同名称的多个模型，请创建一个新的连接并将模型绑定到连接。

        ```js
            var mongoose = require('mongoose');
            var connection = mongoose.createConnection(..);
            var kittySchema = mongoose.Schema({ name: String });
            var Kitten = connection.model('Kitten', kittySchema);
        ```

**要添加的东西?**

如果您想贡献此页面，请在github上[访问它][12]，并使用[编辑][13]按钮发送拉取请求。

[1]: http://mongoosejs.com/api.html#types_array_MongooseArray.set
[2]: https://docs.mongodb.com/manual/core/index-unique/
[3]: http://mongoosejs.com/docs/guide.html#minimize
[4]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty
[5]: http://mongoosejs.com/docs/guide.html#virtuals
[6]: http://mongoosejs.com/docs/guide.html#methods
[7]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#No_binding_of_this
[8]: https://github.com/Automattic/mongoose/issues/3738
[9]: https://github.com/Automattic/mongoose/issues/2202
[10]: http://mongoosejs.com/docs/connections.html#buffering
[11]: https://mochajs.org/#hooks
[12]: https://github.com/Automattic/mongoose/tree/master/docs/faq.jade
[13]: https://github.com/blog/844-forking-with-the-edit-button

