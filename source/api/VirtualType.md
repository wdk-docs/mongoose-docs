
# 虚拟类型

## VirtualType()

VirtualType constructor

This is what mongoose uses to define virtual attributes via `Schema.prototype.virtual`.

**示例**

    var fullname = schema.virtual('fullname');
    fullname instanceof mongoose.VirtualType

## VirtualType.prototype.get()

**参数**

**返回**

Defines a getter.

**示例**

    var virtual = schema.virtual('fullname');
    virtual.get(function () {
      return this.name.first + ' ' + this.name.last;
    });

## VirtualType.prototype.set()

**参数**

**返回**

Defines a setter.

**示例**

    var virtual = schema.virtual('fullname');
    virtual.set(function (v) {
      var parts = v.split(' ');
      this.name.first = parts[0];
      this.name.last = parts[1];
    });

## VirtualType.prototype.applyGetters()

**参数**

**返回**

* «any» the value after applying all getters

Applies getters to `value` using optional `scope`.

## VirtualType.prototype.applySetters()

**参数**

**返回**

* «any» the value after applying all setters

Applies setters to `value` using optional `scope`.

* * *
* * *
