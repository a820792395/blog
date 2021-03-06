# Proxy

## 概述
`Proxy`对象用于定义基本操作的自定义行为（例如：属性查找，赋值、枚举、函数调用等）。

`Proxy`可以理解成，在对象之前设了一层拦截，对该对象的访问都必须通过这层拦截。因此提供了一种机制，可以对外界的访问
进行过滤和改写。
```js
let obj = new Proxy({}, {
  get(target, propKey, receiver) {
    console.log("getting" + propKey);
    return Reflect.get(target, propKey, receiver);
  },

  set(target, propKey, value, receiver) {
    console.log("setting" + propKey);
    return Reflect.set(target, propKey, value, receiver);
  }
});

obj.count = 1;
// settingcount
console.log(++obj.count); 
// gettingcount
// settingcount
// 2
```
上面的代码中，对一个空对象设了一层拦截，重定义了属性的读取（`get`）和设置（`set`）行为。对设置了拦截行为的对象`obj`，读写它的属性时，就得到以上的结果。

创建Proxy语法如下：
```js
var proxy = new Proxy(target, handler);
```
`Proxy`对象的所有用法，都像上面的形式。`target`表示要拦截的目标对象，`handler`参数也是一个对象，用来定义拦截行为。

来看个例子。
```js
let p = new Proxy({}, {
  get(target, propKey) {
    console.log(propKey);
    // "time"
    // "name"
    // "title"
    return 35;
  }
});

console.log(p.time); // 35
console.log(p.name); // 35
console.log(p.title); // 35
```
上面的代码中，`Proxy`接受两个参数。第一个参数就是要代理的对象，也就是上面例子中的空对象。如果没有`Proxy`的介入，操作原来要访问的就是这个对象；第二个参数就是一个配置对象，对于每一个被代理的操作，需要提供一个对应的处理函数，这个函数将拦截对应的操作。例如：上面的代码中，配置对象有一个`get`方法，用来拦截对目标对象属性的访问。`get`方法的两个参数分别是目标对象和所要访问的属性。由于拦截函数总是返回`35`，所以访问任何属性都是返回`35`。

如果要使得`Proxy`起作用，必须是针对`Proxy`实例进行操作（也就是上面的`p`对象），而不是针对目标对象（上例中的空对象）进行操作。

如果`handler`没有设置任何拦截的情况下，就等于直接通向原对象。
```js
let target = {};
let handler = {};
let p = new Proxy(target, handler);

p.name = "tutu";
console.log(target.name); // tutu
```
上面的代码中，`handler`是一个空对象，没有任何拦截效果，访问`p`就等于访问`target`。

把`Proxy`对象，设置到`object.proxy`属性，从而可以在`object`对象上调用。
```js
const target = {};
const handler = {
  get(target, propKey) {
    console.log(`getting ${propKey}`);
    return target[propKey];
  }
};
const obj = { proxy: new Proxy(target, handler) };

obj.proxy.time = 3;
console.log(obj.proxy.time);
// getting time
// 3
```
`Proxy`实例也可以作为其他对象的原型对象。
```js
let p = new Proxy({}, {
  get(target, propKey) {
    return 50;
  }
});
let obj = Object.create(p);
console.log(obj.time); // 50
console.log(obj.prototype === p.prototype); // true
```
上面的代码中，`p`对象是`obj`对象的原型，`obj`对象本身没有`time`属性，所以根据原型链，就可以在`proxy`上获取到该属性，导致被拦截。

同一个拦截器函数，可以设置拦截多个操作。
```js
let handler = {
  get(target, name) {
    if (name === 'prototype') {
      return Object.prototype;
    }
    return `Hello, ${name}`;
  },

  apply(target, thisBinding, args) {
    return args[0];
  },

  construct(target, args) {
    return {value: args[1]};
  }
};

let f = new Proxy(function(x, y) {
  return x + y;
}, handler);

f(1, 2);
console.log(new f(1, 2)); // 2
console.log(f.prototype === Object.prototype); // true
console.log(f.foo === "Hello, foo"); // true
```
## Proxy实例的方法
`Proxy`一共有13种拦截方法操作如下：

`get(target, propKey, receiver)`：拦截对象属性的读取，比如`p.foo`和`proxy['foo']`。最后一个参数`receiver`是一个可选对象。

`set(target, propKey, vlaue)`：拦截对象属性的设置，比如`p.foo = v`或`p['foo'] = v`，返回一个布尔值。

`has(target, propKey)`：拦截`propKey in p`的操作，返回一个布尔值。

`deleteProperty(target, propKey)`：拦截`delete p[propKey]`的操作，返回一个布尔值。

`ownKeys(target)`：拦截`Object.getOwnPropertyNames(p)`、`Object.getOwnPropertySymbols(p)`、`Object.keys(p)`、`for...in`循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而`Object.keys()`的返回结果仅包括目标对象自身的可遍历属性。

`getOwnPropertyDescriptor(target, propKey)`：拦截`Object.getOwnPropertyDescriptor(p, propKey)`，返回属性的描述对象。

`defineProperty(target, propKey, propDesc)`：拦截`Object.defineProperty(p, propKey, propDesc)`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。

`preventExtensions(target)`：拦截`Object.preventExtensions(p)`，返回一个布尔值。

`getPrototypeOf(target)`：拦截`Object.getPrototypeOf(p)`，返回一个对象。

`isExtensible(target)`：拦截`Object.isExtensible(p)`，返回一个布尔值。

`setPrototypeOf(target, proto)`：拦截`Object.setPrototypeOf(p, proto)`，返回一个布尔值。如果目标对象是函数的话，还有两种额外操作可以拦截。

`apply(target, object, args)`：拦截`Proxy`实例作为函数调用的操作，比如`p(...args)`、`p.call(object, ...args)`、`p.apply(...)`。

`construct(target, args)`：拦截`Proxy`实例作为构造函数调用的操作，比如`new p(...args)`。

### get()
`get`方法用于拦截某个属性的读取操作，看下面的例子：
```js
let person = {
  name: "tutu"
};

let proxy = new Proxy(person, {
  get(target, key) {
    if (key in target) {
      return target[key];
    } else {
      console.log(`没有${key}这个属性`);
    }
  }
});

console.log(proxy.name); // tutu
console.log(proxy.age); // 没有age这个属性
```
上面的代码中，如果访问`person`对象不存在的属性，就是在打印出一段话，如果没有这个拦截函数，访问不存在的属性会返回`undefined`。

`get`方法可以继承。
```js
let proto = new Proxy({}, {
  get(target, key, receiver) {
    console.log('GET ' + key);
    return target[key];
  }
});

let obj = Object.create(proto);

console.log(obj.name); // GET name
```
上面的代码中，拦截操作定义在`Prototype`对象上，如果读取`obj`对象继承的属性，拦截就会生效。

来看看使用`get`拦截实现数组读取负数索引。
```js
function createArray(...elements) {
  let handler = {
    get(target, propKey, receiver) {
      let index = Number(propKey);

      if (index < 0) {
        propKey = String(target.length + index);
      }

      return Reflect.get(target, propKey, receiver);
    }
  }

  let target = [];
  target.push(...elements);

  return new Proxy(target, handler);
};

let arr = createArray('a', 'b', 'c');

console.log(arr[-1]); // -c
```
上面的代码中，如果数组的位置参数是`-1`，就会输出数组的最后一个成员。

利用`Proxy`，可以将读取属性操作（`get`）转变为执行某个函数，从而实现属性的链式操作。
```js
var pipe = (function() {
  return function(value) {
    var funcStack = [];
    var oproxy = new Proxy({}, {
      get(pipeObject, fnName) {
        if (fnName === 'get') {
          return funcStack.reduce(function (val, fn) {
            return fn(val);
          }, value);
        }

        funcStack.push(window[fnName]);
        return oproxy;
      }
    });

    return oproxy;
  }
}());

var double = n => n * 2;
var pow = n => n * n;
var reverseInt = n => n.toString().split("").reverse().join("") | 0;

console.log(pipe(3).double.pow.reverseInt.get); // 63
```
上面的代码中设置`Proxy`后达到了链式使用函数名的效果。

下面的例子是利用`get`拦截实现一个生成各种`DOM`节点的通用函数dom。
```js
const dom = new Proxy({}, {
  get(target, property) {
    console.log(target, property);
    return function(attrs = {}, ...children) {
      console.log(attrs, children);
      const el = document.createElement(property);

      for (let prop of Object.keys(attrs)) {
        el.setAttribute(prop, attrs[prop]);
      }

      for (let child of children) {
        if (typeof child === "string") {
          child = document.createTextNode(child);
        }
        el.appendChild(child);
      }

      return el;
    }
  }
});

const el = dom.div({},
  'Hello, my name is',
  dom.a({href: '//example.com'}, 'Mark'),
  '. I like:',
  dom.ul({},
    dom.li({}, 'The web'),
    dom.li({}, 'Food'),
    dom.li({}, '...actually that \'s it')
  )
);

document.body.appendChild(el);
```
如果一个属性不可配置（configurable）或者不可写（writable）的话，这个属性就不能被代理，通过`Proxy`对象访问该属性将会报错。
```js
const target = Object.defineProperty({}, {
  foo: {
    value: 123,
    writable: false,
    configuralble: false
  },
});

const handler = {
  get(target, propkey) {
    return '123';
  }
};

const proxy = new Proxy(target, handler);

proxy.foo;
// TypeError：Property description must be an object: undefined
```

### set()

`set`方法用于拦截某个属性的赋值操作。

比如`Person`对象有一个`age`属性，该属性应该是一个不大于200的整数，那就可以使用`Proxy`对象保证`age`的属性值符合要求。
```js
let validator = {
  set(obj, prop, value) {
    if (prop === 'age') {
      if (!Number.isInteger(value)) {
        console.log('设置Age值不是一个整数');
      }

      if (value > 200) {
        console.log("设置Age值不能大于200");
      }
    }

    obj[prop] = value;
  }
};

let person = new Proxy({}, validator);

person.age = 100;
console.log(person.age);
person.age = 10.1;
// 设置Age值不是一个整数
person.age = "abc";
// 设置Age值不是一个整数
person.age = 300;
// 设置Age值不能大于200
```
上面的代码中，由于设置了存值的函数`set`，任何不符合要求的`age`属性赋值都会抛出一个错误，这是数据验证的一种方法。我们可以利用`set`方法实现数据绑定，就是每当对象发生变化时，会自动更新DOM。

有时候，我们会在对象上设置内部属性，属性名的第一个字符使用下画线开头，表示这些属性不应该被外部使用。结合`get`和`set`方法，可以做到防止这些内部属性被外部读或写。
```js
let handler = {
  get (target, key) {
    invariant(key, 'get');
    return target[key];
  },
  set (target, key, value) {
    invariant(key, 'set');
    target[key] = value;
    return true;
  }
};

function invariant(key, action) {
  if (key[0] === '_') {
    throw new Error(`Invalid attempt to ${action} private "${key}" property`);
  }
}

var target = {};
var proxy = new Proxy(target, handler);

proxy._prop;
// Error: Invalid attempt to get private "_prop" property

proxy._prop = "c";
// Error: Invalid attempt to set private "_prop" property
```
上面的代码中，只要读/写的属性名的第一个字符是下画线，都会抛出错误，可以达到禁止读/写内部属性的目的。

:::warning
如果目标对象自身的某个属性不可写也不可配置，那`set`不能改变这个属性的值，只能返回一样的值，否则会报错。
:::

### apply()
`apply`方法用于拦截函数的调用、`call`和`apply`操作。

`apply`方法可以接收3个参数，分别是目标对象，目标对象的上下文对象（`this`）和目标对象的参数数组。
```js
var handler = {
  apply(target, ctx, args) {
    return Reflect.apply(...arguments);
  }
};
```
看下面的例子。
```js
var target = function() { return 'I am the target' };
var handler = {
  apply() {
    return 'I am the proxy';
  }
};

var p = new Proxy(target, handler);

console.log(p());
// I am the proxy
```
上面的代码中，变量`p`是`Proxy`的实例，作为函数调用时（`p()`）就会被`apply`方法拦截，返回一个字符串。
```js
var twice = {
  apply(target, ctx, args) {
    return Reflect.apply(...arguments) * 2;
  }
};

function sum(left, right) {
  return left + right;
};

var proxy = new Proxy(sum, twice);

console.log(proxy(1, 2)); // 6
console.log(proxy.call(null, 5, 6)); // 22
console.log(proxy.apply(null, [7, 8])); // 30
```
上面的代码中，每次执行`proxy`（直接调用或`call`和`apply`调用）都会被`apply`方法拦截。还有直接调用`Reflect.apply`方法也会被拦截。
```js
console.log(Reflect.apply(proxy, null, [11, 10])); // 42
```

### has()

`has`方法用于拦截`HasProperty`操作，就是判断对象是否具有某个属性时，这个方法会生效。典型操作`in`运算符。

下面的例子使用`has`隐藏了某些属性，从而不被`in`运算符发现。
```js
var handler = {
  has(target, key) {
    if (key[0] === "_") {
      return false;
    }

    return key in target;
  }
};

var target = { _prop: 'foo', prop: 'foo' };
var proxy = new Proxy(target, handler);

console.log('_prop' in proxy); // false
```
上面的代码中，如果原对象的属性名的第一个字符是下画线，`proxy.has`就会返回`false`，从而不会被`in`运算符发现。

如果原对象不可配置或者禁止扩展，这时`has`拦截会报错。
```js
var obj = { a: 10 };
Object.preventExtensions(obj);

var p = new Proxy(obj, {
  has(target, prop) {
    return false;
  }
});

console.log('a' in p);
// TypeError: 'has' on proxy: trap returned falsish for property 'a' but the proxy target is not extensible
```
上面的代码中，`obj`对象禁止扩展，结果使用`has`拦截就会报错。也就是说，如果某个属性不可配置（或者目标对象不可扩展），则`has`方法就不得"隐藏"（返回false）目标对象的该属性。

:::warning
`has`方法拦截的是`HasProperty`操作，而不是`HasOwnProperty`操作，即`has`方法不判断一个属性是对象自身的属性还是继承的属性。
:::

另外，`for...in`循环虽然用到了`in`运算符，但是`has`拦截对此不会产生任何效果。
```js
let stu1 = { name: '张三', score: 59 };
let stu2 = { name: '李四', score: 99 };

let handler = {
  has(target, prop) {
    if (prop === 'score' && target[prop] < 60) {
      console.log(`${target.name} 不及格`);
      return false;
    }

    return prop in target;
  }
}

let oproxy1 = new Proxy(stu1, handler);
let oproxy2 = new Proxy(stu2, handler);

console.log('score' in oproxy1);
// 张三 不及格
// false

console.log('score' in oproxy2);
// true

for (let a in oproxy1) {
  console.log(oproxy1[a]);
  // 张三
  // 59
}

for (let b in oproxy2) {
  console.log(oproxy2[b]);
  // 李四
  // 99
}
```
上面的代码中，`has`拦截只对`in`循环生效，对`for...in`循环不生效，导致不符合要求的属性没有被排除在`for...in`循环之外。

### construct()
`construct`方法用于拦截`new`命令，下面是拦截对象的写法。
```js
var handler = {
  construct(target, args, newTarget) {
    return new target(...args);
  }
};
```
construct方法可以接受两个参数。
* target：目标对象。
* args：构建函数的参数对象。

来看个例子：
```js
var p = new Proxy(function() {}, {
  construct(target, args) {
    console.log('called:' + args.join(', '));
    return { value: args[0] * 10 };
  }
});

console.log(new p(1).value);
// called:1
// 10
```
construct方法返回的必须是一个对象，否则会报错。
```js
var p = new Proxy(function() {}, {
  construct(target, args) {
    return 1;
  }
});

new p();
//  TypeError: 'construct' on proxy: trap returned non-object ('1')
```

### deleteProperty()
`deleteProperty`方法用于拦截`delete`操作，如果这个方法抛出错误或者返回`false`，当前属性就无法被`delete`命令删除。
```js
var handler = {
  deleteProperty(target, key) {
    invariant(key, 'delete');
    return true;
  }
};

function invariant(key, action) {
  if (key[0] === '_') {
    throw new Error(`Invalid attempt to ${action} private "${key}" property`);
  } else {
    console.log("进入", delete key);
    return delete key;
  }
}

var target = { _prop: 'foo', num: 10 };
var proxy = new Proxy(target, handler);

delete proxy._prop;
// Error: Invalid attempt to delete private "_prop" property
```
上面的代码中，`deleteProperty`方法拦截了`delete`操作符，删除第一个字符为下画线的属性会报错。

:::warning
目标对象自身的不可配置（`configurable`）的属性不能被`deleteProperty`方法删除，否则会报错。
:::

### defineProperty()
`defineProperty`方法拦截了`Object.defineProperty`操作。
```js
var handler = {
  defineProperty(target, key, descriptor) {
    return false;
  }
};

var target = { };
var proxy = new Proxy(target, handler);
proxy.foo = 'bar';
console.log(proxy.foo); // undefined
```
上面的代码中，`defineProperty`方法返回false，导致了添加新属性时并没有添加成功。读取`proxy.foo`时则是`undefined`。

:::warning
如果目标对象不可扩展（extensible），`definedProperty`不能增加目标对象中不存在的属性，否则会报错。另外，如果目标对象的某个属性不可写（writable）或不可配置（configurable），则`definedProperty`方法不得改变这两个设置。
:::

### getOwnPropertyDescriptor()
`getOwnPropertyDescriptor`方法拦截`Object.getOwnPropertyDescriptor()`，返回一个属性描述对象或者`undefined`。
```js
var handler = {
  getOwnPropertyDescriptor(target, key) {
    if (key[0] === '_') {
      return;
    }

    return Object.getOwnPropertyDescriptor(target, key);
  }
};

var target = { _foo: 'bar', baz: 'tar' };
var proxy = new Proxy(target, handler);
console.log(Object.getOwnPropertyDescriptor(proxy, 'wat')); // undefined
console.log(Object.getOwnPropertyDescriptor(proxy, '_foo'));
console.log(Object.getOwnPropertyDescriptor(proxy, 'baz'));
// {value: "tar", writable: true, enumerable: true, configurable: true}
```

### getPrototypeOf()
`getPrototyepOf`方法用来拦截获取对象原型。总得来说，用于拦截一下操作。
* `Object.prototype.__proto__`
* `Object.prototype.isPrototypeOf()`
* `Object.getPrototypeOf()`
* `Reflect.getPrototypeOf()`
* `instanceof`
看下面的例子：
```js
var proto = {};
var p = new Proxy({}, {
  getPrototypeOf(target) {
    return proto;
  }
});

console.log(Object.getPrototypeOf(p) === proto); // true
```
上面的代码中，`getPrototypeOf`方法拦截`Object.getPrototypeOf()`，返回`proto`对象。

:::warning
`getPrototypeOf`方法的返回值必须是对象或者`null`，不然会出现报错。另外，如果目标对象不可扩展（extensible），`getPrototypeOf`方法必须返回目标对象的原型对象。
:::

### isExtensible()
`isExtensible`方法拦截`Object.isExtensible`操作。
```js
var p = new Proxy({}, {
  isExtensible(target) {
    console.log("called");
    return true;
  }
});

console.log(Object.isExtensible(p));
// called
// true
```
上面的代码设置了`isExtensible`方法，在调用`Object.isExtensible`时会输出called。

但是要注意点是，上面的方法只能返回布尔值，否则返回值会被自动转成布尔值。

这个方法有一个强制，它的返回值必须与目标对象的`isExtensble`属性保持一致，否则会报错。
```js
let target = {};
var p = new Proxy(target, {
  isExtensible(target) {
    console.log("called");
    return true;
  }
});

console.log(Object.isExtensible(p) === Object.isExtensible(target));
// true
```
看下面的例子：
```js
let target = {};
var p = new Proxy(target, {
  isExtensible(target) {
    return false;
  }
});

console.log(Object.isExtensible(p));
// TypeError: 'isExtensible' on proxy: trap result does not reflect extensibility of proxy target (which is 'true')
```

### ownKeys()
`ownKeys`方法用来拦截对象自身属性的读取操作，总的来说，拦截以下的操作。
* Object.getOwnPropertyNames()
* Object.getOwnPropertySymbols()
* Object.keys()

下面是拦截`Objece.keys()`的例子。
```js
let target = {
  a: 1,
  b: 2,
  c: 3
};

let handler = {
  ownKeys(target) {
    return ['a'];
  }
};

let proxy = new Proxy(target, handler);

console.log(Object.keys(proxy)); // ["a"]
```
上面的代码拦截了对于`target`对象的`Object.keys()`操作，只返回`a`、`b`、`c`三个属性之中的`a`属性。 

下面的例子是拦截第一个字符为下画线的属性名。
```js
let target = {
  _bar: 'foo',
  _prop: 'bar',
  prop: 'bar',
  foo: 'abc'
};

let handler = {
  ownKeys(target) {
    return Reflect.ownKeys(target).filter(key => key[0] !== '_');
  }
};

let proxy = new Proxy(target, handler);

for (let key of Object.keys(proxy)) {
  console.log(target[key]);
  // bar
  // abc
}
```
需要注意的是，使用`Object.keys`方法市，有三类属性会被`ownKeys`方法自动过滤，不会返回。
* 目标对象上不存在的属性。
* 属性名为`Symbol`值。
* 不可遍历（enumerable）属性。

```js
let target = {
  a: 1,
  b: 2,
  c: 3,
  [Symbol.for('secret')]: '4'
};

Object.defineProperty(target, 'key', {
  enumerable: false,
  configurable: false,
  writable: true,
  value: 'static'
});

let handler = {
  ownKeys(target) {
    return ['a', 'd', Symbol.for('secret'), 'key'];
  }
};

let proxy = new Proxy(target, handler);

console.log(Object.keys(proxy));
// ["a"]
```
上面的代码中，`ownKeys`方法显式返回不存在的属性`d`、Symbol值（`Symbol.for('secret')`）、不可遍历的属性（`key`），结果都会被自动过滤掉了。

`ownKeys`方法还可以拦截`Object.getOwnPropertyNames()`。
```js
var p = new Proxy({}, {
  ownKeys(target) {
    return ['a', 'b', 'c'];
  }
});

console.log(Object.getOwnPropertyNames(p));
// ["a", "b", "c"]
```
`ownKeys`方法返回的数组成员只能是字符串或`Symbol`值。如果有其他类型的值，或者返回的根本不是数组，就会报错。
```js
var obj = {};
var p = new Proxy(obj, {
  ownKeys(target) {
    return [123, true, undefined, null, {}, {}];
  }
});
console.log(Object.getOwnPropertyNames(p));
// TypeError: 123 is not a valid property name
```
上面的代码中，`ownKeys`方法虽然返回一个数组，但是每一个数组成员都不是字符串或`Symbol`值，因此就会报错。

如果目标对象自身包含不可配置的属性，则该属性必须被`ownKeys`方法返回，否则会报错。
```js
var obj = {};
Object.defineProperty(obj, 'a', {
  configurable: false,
  enumerable: true,
  value: 10
});

var p = new Proxy(obj, {
  ownKeys(target) {
    return ['b'];
  }
});

console.log(Object.getOwnPropertyNames(p));
// TypeError: 'ownKeys' on proxy: trap result did not include 'a'
```
上面的代码中，`obj`对象的`a`属性是不可配置的，这时`ownKeys`方法返回的数组之中必须包含`a`，否则就会报错。

另外，如果目标对象是不可扩展的，这时`ownKeys`方法返回的数组之中必须包含原对象的所有属性，而且不能包含多余的属性，
否则会报错。
```js
var obj = {
  a: 1
};

console.log(Object.preventExtensions(obj));

var p = new Proxy(obj, {
  ownKeys(target) {
    return ['a', 'b'];
  }
});

console.log(Object.getOwnPropertyNames(p));
// {a: 1}
// TypeError: 'ownKeys' on proxy: trap returned extra keys but proxy target is non-extensible
```
上面的代码中，`Obj`对象是不可扩展的，这时`ownKeys`方法返回的数组之中包含了`obj`对象的多余属性`b`，所以导致出错了。

### preventExtensions()
`preventExtensions`方法拦截`Object.preventExtensions()`。该方法必须返回一个布尔值，否则会被自动转为布尔值。这个方法有一个限制，只有目标对象不可扩展时，也就是说，`Object.isExtensions(proxy)`为`false`的时候。`proxy.preventExtensions`才能返回`true`，否则报错。
```js
var p = new Proxy({}, {
  preventExtensions(target) {
    return true;
  }
});

console.log(Object.preventExtensions(p));
// 'preventExtensions' on proxy: trap returned truish but the proxy target is extensible
```
上面的代码中，`proxy.preventExtensions`方法返回`true`，但是`Object.isExtensible(proxy)`也返回了`true`，所以就报错了。

为了防止出现这类问题，通常在`proxy.preventExtensions`方法中调用一次`Object.preventExtensions`，看下面的例子。
```js
var p = new Proxy({}, {
  preventExtensions: function(target) {
    console.log('called');
    Object.preventExtensions(target);
    return true;
  }
});

console.log(Object.preventExtensions(p));
// "called"
// Proxy {}
```

### setPrototypeOf()
`setPrototypeOf`方法主要用于拦截`Object.setPrototypeOf`方法。
看下面的例子。
```js
var handler = {
  setPrototypeOf(target, proto) {
    throw new Error('changing the prototype is forbidden');
  } 
};

var proto = {};
var target = function() {};
var proxy = new Proxy(target, handler);
Object.setPrototypeOf(proxy, proto);
// Error: changing the prototype is forbidden
```
上面的代码中，只要修改`target`的原型对象就会报错。

:::warning
该方法只能返回布尔值，否则会被自动转为布尔值。还有，如果目标对象不可扩展，`setPrototypeOf`方法不得改变目标对象的原型。
:::

## Proxy.revocable()
`Proxy.revocable`方法返回一个可取消的`Proxy`实例。`Proxy.revocable`方法返回一个对象，对象里有两个属性分别是`proxy`和`revoke`，proxy属性就是`Proxy`实例。`revoke`属性是一个方法，用来取消`Proxy`实例。看下面的例子：
```js
let target = {};
let handler = {};

let { proxy, revoke } = Proxy.revocable(target, handler);

proxy.foo = 123;

console.log(proxy.foo); // 123

console.log(Proxy.revocable(target, handler)); // {proxy: Proxy, revoke: ƒ}

revoke();

console.log(proxy.foo);
//  Cannot perform 'get' on a proxy that has been revoked 
```
上面的代码中，当执行`revoke`函数后再访问`Proxy`实例就会抛出一个报错。

`Proxy.revocable`的使用场景一般是，目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问。

## this问题
虽然`Proxy`可以代理针对目标对象的访问，但它不是目标对象的透明代理，即不做任何拦截的情况下也无法保证与目标对象的行为一致。主要原因是在`Proxy`代理的情况下，目标对象内部的`this`关键字会指向`Proxy`代理。
```js
const target = {
  m() {
    console.log(this === proxy);
  }
};

const handler = {};

const proxy = new Proxy(target, handler);

target.m(); // false
proxy.m(); // true
```
上面的代码中，一旦`proxy`代理`target.m`，后者内部的`this`就指向`proxy`，而不是`target`。

来看下面的例子，由于`this`指向的变化导致`Proxy`无法代理目标对象。
```js
const _name = new WeakMap();

class Person {
  constructor(name) {
    _name.set(this, name)
  }

  get name() {
    return _name.get(this);
  }
}

const jane = new Person('Jane');
console.log(jane.name); // Jane

const proxy = new Proxy(jane, {});

console.log(proxy.name); // undefined
```
上面的代码中，目标对象`jane`的`name`属性实际保存在外部`WeakMap`对象_name上面，通过`this`键区分。由于通过`proxy.name`访问时，`this`指向了`proxy`，导致取不到值，所以返回`undefined`。

另外，有些原生对象的内部属性只有通过正确的`this`才能获取，所以`Proxy`也无法代理这些原生对象的属性。
```js
const target = new Date();
const handler = {};
const proxy = new Proxy(target, handler);

console.log(proxy.getDate());
// TypeError: this is not a Date object.
```
上面的代码中，`getData`方法只能在`Date`对象实例上获取，如果说`this`不是`Date`对象实例就会报错。通过`this`绑定原始对象就可以解决这个问题。
```js
const target = new Date('2020-09-13');
const handler = {
  get(target, prop) {
    if (prop === 'getDate') {
      return target.getDate.bind(target);
    }

    return Reflect.get(target, prop);
  }
};

const proxy = new Proxy(target, handler);

console.log(proxy.getDate()); // 13