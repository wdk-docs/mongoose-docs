# MongoDB服务器版本兼容性

[源](http://mongoosejs.com/docs/compatibility.html "Mongoose v5.0.1的永久链接：MongoDB版本兼容性")

Mongoose依靠[MongoDB Node.js 驱动器][1]与MongoDB进行通信。 您可以参考[此表][2]获取哪个版本的MongoDB驱动程序支持哪个版本的MongoDB的最新信息。

下面是代表哪个版本的mongoose与列出的MongoDB版本兼容的[semver][3]范围

* MongoDB 服务器 2.4.x: mongoose `~3.8` 或者 `4.x`
* MongoDB 服务器 2.6.x: mongoose `~3.8.8` 或者 `4.x`
* MongoDB 服务器 3.0.x: mongoose `~3.8.22`, `4.x` 或者 `5.x`
* MongoDB 服务器 3.2.x: mongoose `>=4.3.0` 或者 `5.x`
* MongoDB 服务器 3.4.x: mongoose `>=4.7.3` 或者 `5.x`
* MongoDB 服务器 3.6.x: mongoose `5.x`, 或者 `>=4.11.0` with `useMongoClient` and `usePushEach`

[1]: http://mongodb.github.io/node-mongodb-native/
[2]: http://docs.mongodb.org/ecosystem/drivers/node-js/
[3]: http://semver.org/

