# mongoose

为node.js优雅的mongodb对象建模

[阅读文档](http://mongoosejs.com/docs/guide.html)
[发现插件](http://plugins.mongoosejs.io/)

Version 5.0.1

面对现实吧,**编写MongoDB验证，铸造和业务逻辑样板是一个拖累**. 这就是为什么我们写了mongoose.

```js
const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/test');

const Cat = mongoose.model('Cat', { name: String });

const kitty = new Cat({ name: 'Zildjian' });
kitty.save().then(() => console.log('meow'));
```

Mongoose提供了一个简单的，基于模式的解决方案来建模您的应用程序数据。它包括内置的类型转换，验证，查询构建，业务逻辑钩子等等。

## 入门

[快速入门指南](http://mongoosejs.com/docs/index.html)

## 支持

* [堆栈溢出](http://stackoverflow.com/questions/tagged/mongoose)
* [GitHub问题](https://github.com/Automattic/mongoose/issues)
* [格子聊天](https://gitter.im/Automattic/mongoose)
* [MongoDB支持](http://www.mongodb.org/about/support/)

## 新闻

[推特](https://twitter.com/mongoosejs)

## 更新日志

[更新日志](https://github.com/Automattic/mongoose/blob/master/History.md)

## 赞助商

![MixMax.com](http://mongoosejs.com/docs/images/mixmax.png)

在OpenCollective上赞助Mongoose以获得您公司的标识！
