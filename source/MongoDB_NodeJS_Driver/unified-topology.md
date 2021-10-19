# 统一拓扑设计

Unified Topology Design

在编写节点驱动程序时，它有 7 个拓扑类，包括新引入的统一拓扑。

每个来自核心模块的遗留拓扑类型都以一个受支持的拓扑类为目标:副本集、分片部署(mongos)和独立服务器。

在它们之上是一个来自“本机”层的瘦拓扑包装器，它引入了“断开处理程序”的概念，本质上是一个用于处理简单的重试性的回调队列。

统一拓扑的目标有三个方面:——完全支持司机发现和监控服务器,服务器选择和马克斯过时规范——减轻维护负担支持建模中的拓扑层驱动程序的所有支持拓扑类型的单引擎——删除混淆功能可以为我们的用户潜在的危险

## 如何使用它

统一拓扑现在在 useUnifiedTopology 特性标志后面可用。
你可以通过将这个选项传递给你的 MongoClient 构造函数来选择使用它:

```js
const client = MongoClient("mongodb://localhost:27017", {
  useUnifiedTopology: true,
});
```

!!! note

    在即将到来的小版本中，useUnifiedTopology 将默认为true，在驱动程序的下一个主要版本中，这个拓扑将完全取代遗留拓扑。

## 行为修改

### MongoClient.connect, isConnected

从使用连接方法“connecting”到 MongoDB 部署的概念的范例转变中，统一拓扑是第一步。
考虑一下连接到复制集意味着什么:当连接到主集时，我们会触发这种状态吗?
一个主要的和一个次要的?
当连接到所有已知节点时?
目前还不清楚是否可以在不向 connect 方法引入 ReadPreference 参数的情况下回答这个问题。
此时，“connecting”只是“operation execution”的一半——你传入一个 ReadPreference，并等待一个可选择的服务器进行操作，现在我们连接了!
但你不能把这些都作为你第一次手术的一部分吗?
我们的目标是让代码看起来更像下面这样:

```js
const client = new MongoClient(
  "mongodb://llama:drama@localhost:27017/?replicaSet=rs"
);
const coll = client.db("test").collection("foo");
await coll.insert({ test: "document" });
const docs = coll.find({ test: 1 }, { readPreference: "secondary" }).toArray();
console.dir({ docs });
await client.close();
```

默认的 ReadPreference 为“primary”用于第一次写操作, 等待插入的一部分包括启动到集群中所有服务器的连接, 选择服务器并执行操作.
错误将出现在任何给定操作的调用站点，使用户可以更细粒度地控制错误处理.

### 为什么 MongoClient.isConnected 总是返回 true?

我们认为“连接”含义的模糊性会导致更多的问题，而不是它想要解决的问题。
应用程序开发人员主要关心的是操作的成功执行。
isConnected 方法通常用于对 MongoClient 进行“健康检查”，以确定操作是否可以成功执行。
统一拓扑通过引入“服务器选择循环”(下面将详细讨论)将这个问题直接推到操作执行中。
因此，MongoClient 总是“连接”的，因为它总是接受操作并尝试执行它们。

!!! note

    在驱动程序的下一个主要版本中，isConnected将被完全删除。

## 服务器选择

操作执行的伪代码如下所示:

```js
function executeOperation(topology, operation, callback) {
  const readPreference = resolveReadPreference(operation);
  topology.selectServer(readPreference, (err, server) => {
    if (err) {
      // This error is most likely a "Server selection timed out after Xms"
      return callback(err);
    }

    // checks a connection out of the server to execute the operation, then checks it back in
    server.withConnection((conn) => operation.execute(conn, callback));
  });
}
```

上面的 serverSelection 方法将一直循环到 serverSelectionTimeoutMS(默认为 30s)，等待驱动程序成功连接到一个可行的服务器，以执行请求的操作。

如果服务器选择没有产生可行的服务器，则将控制权传递给用户，以确定下一个最佳操作方案是什么。

这并不一定意味着客户机通常与集群断开连接，但它目前没有连接到任何满足指定 ReadPreference 的服务器。

### disconnectHandler

来自“本机”层(在 lib/拓扑中)的三种拓扑类型主要为回调存储提供支持，称为“断开处理程序”。

遗留拓扑不是使用服务器选择循环，而是在没有合适的服务器可用的情况下在这个存储上放置回调，以便在稍后运行操作。

这个回调存储还提供了一种朴素的可重试性形式，但是在实践中，这可能会导致意想不到的，甚至意想不到的结果:

- 回调存储只与单个服务器相关联，因此只有在最初选择的服务器上才尝试重新执行操作。

如果该服务器再也不能恢复(例如，它已经退出或退役)，那么该操作将处于不确定状态。

- 没有与服务器协作来确保排队写操作只发生一次。

假设运行一个 updateOne 操作被网络错误中断。

该操作已成功发送到服务器，但服务器响应在中断期间丢失，这意味着该操作被放置在回调存储区中，等待重试。
与此同时，另一个微服务允许用户更新写入的数据。
一旦原始客户端重新连接到服务器，它将自动重新执行操作并使用较旧的值更新较新的数据。

统一拓扑完全消除了断开连接的处理程序，支持更健壮和一致的“可重试读取”和“可重试写入”特性。
操作现在将尝试在服务器选择循环中执行，直到 serverSelectionTimeoutMS(默认:30s)，并将在发生可重试错误时重试一次操作。
这个循环之外的所有错误都返回给用户，因为他们知道在这些场景中最好做什么。
