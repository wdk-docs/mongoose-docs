
### [VirtualType()][278]

VirtualType constructor

This is what mongoose uses to define virtual attributes via `Schema.prototype.virtual`.

#### Example:

    var fullname = schema.virtual('fullname');
    fullname instanceof mongoose.VirtualType

* * *

### [VirtualType.prototype.get()][279]

##### Parameters

##### Returns:

Defines a getter.

#### Example:

    var virtual = schema.virtual('fullname');
    virtual.get(function () {
      return this.name.first + ' ' + this.name.last;
    });

* * *

### [VirtualType.prototype.set()][280]

##### Parameters

##### Returns:

Defines a setter.

#### Example:

    var virtual = schema.virtual('fullname');
    virtual.set(function (v) {
      var parts = v.split(' ');
      this.name.first = parts[0];
      this.name.last = parts[1];
    });

* * *

### [VirtualType.prototype.applyGetters()][281]

##### Parameters

##### Returns:

* «any» the value after applying all getters

Applies getters to `value` using optional `scope`.

* * *

### [VirtualType.prototype.applySetters()][282]

##### Parameters

##### Returns:

* «any» the value after applying all setters

Applies setters to `value` using optional `scope`.

* * *
* * *
