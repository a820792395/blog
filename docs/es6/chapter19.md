# Class的基本语法

## 简介

### 类的由来
在JavaScript语言中，生成实例对象的传统方法是通过构造函数。看下面的例子。
```js
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function() {
  return `(${this.x}, ${this.y})`;
}

let p = new Point(1, 2);

console.log(p.toString());
// (1, 2)
```
上面的这种写法跟传统的面向对象语言来说差异很大，容易使得新学习这门语言的人感到困惑。

ES6提供了更接近传统语言的写法，引入了Class（类）这个概念，作为对象的模板。通过`class`关键字，可以定义类。

ES6的`class`可以看作只是一个语法糖，它的绝大部分功能，ES5都可以做到，新的`class`写法只是让对象原型的写法更加清晰、更像面向对象的语法而已。上面的代码用ES6的`class`改写，就是下面这样。
```js
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return `(${this.x}, ${this.y})`;
  }
}

const p = new Point(3, 4);

console.log(p.toString()); // (3, 4)
```
上面代码定义了一个“类”，可以看到里面有一个`constructor`方法，这就是构造方法，而`this`关键字代表实例对象。也就是说，ES5的构造函数`Point`，对应ES6的`Point`类的构造方法。

`Point`类除了构造方法，还定义了一个`toString`方法。定义“类”的方法时，不需要加上`function`关键字，直接把函数定义放进去了就可以了。另外，方法之间不需要逗号分隔，加上的话会导致报错。

ES6的类，完全可以看作构造函数的另一种写法。
```js
class Point {

}

console.log(typeof Point); // function

console.log(Point === Point.prototype.constructor); // true
```
上面的代码表明，类的数据类型就是函数，类本身就指向构造函数。

使用的时候，也是直接对类使用`new`命令，跟构造函数的用法完全一致。
```js
class Bar {
  doStuff() {
    console.log('stuff');
  }
}

var b = new Bar();
b.doStuff(); // stuff
```
构造函数`prototype`属性，在ES6的“类”上面继续存在。事实上，类的所有方法都定义在类的`prototype`属性上面。
```js
class Point {
  constructor() {

  }

  toString() {

  }

  toValue() {

  }
}

// 等于
Point.prototype = {
  constructor() {},
  toString() {},
  toValue() {}
}
```
在类的实例上面调用方法，其实就是调用原型上的方法。
```js
class B {};
let b = new B();

console.log(b.constructor === B.prototype.constructor); // true
```
上面代码中，`b`是`B`类的实例，它的`constructor`方法就是`B`类原型的`constructor`方法。

由于类的方法都定义在`prototype`对象上面，所以类的新方法可以添加在`prototype`对象上面。`Object.assign`方法可以很方便地一次向类添加多个方法。
```js
class Point {
  constructor() {

  }
}

Object.assign(Point.prototype, {
  toString() {},
  toValue() {}
});

console.log(Point.prototype);
// {toString: ƒ, toValue: ƒ, constructor: ƒ}
```
`prototype`对象的`constructor`属性，直接指向“类”的本身，这跟ES5的行为是一致的。
```js
class Point {
  constructor() { }
}

console.log(Point.prototype.constructor === Point); // true
```
另外，类的内部所有定义的方法，都是不可枚举的
```js
class Point {
  constructor(x, y) { }
  toString() { }
}

console.log(Object.keys(Point.prototype));
// []

console.log(Object.getOwnPropertyNames(Point.prototype));
// ["constructor", "toString"]
```
上面的代码中，`toString`方法是`Point`类内部定义的方法，它是不可枚举的。这一点和ES5的行为不一致。
```js
var Point = function(x, y) { };

Point.prototype.toString = function() { };

console.log(Object.keys(Point.prototype));
// ["toString"]

console.log(Object.getOwnPropertyNames(Point.prototype));
// ["constructor", "toString"]
```
上面代码采用ES5的写法，`toString`方法就是可枚举的。

### constructor方法
`constructor`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须由`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。
```js
class Point {
}

// 等于
class Point {
  constructor() { }
}
```
上面代码中，定义了一个空的类`Point`，JavaScript引擎会自动为它添加一个空的`constructor`方法。

`constructor`方法默认返回实例对象（即`this`），完全可以指定返回另一个对象。
```js
class Foo {
  constructor() {
    return Object.create(null);
  }
}

console.log(new Foo instanceof Foo); // false
```
上面代码中，`constructor`函数返回一个全新的对象，结果就导致实例对象不是`Foo`类的实例。

类必须使用`new`调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用`new`也可以执行。
```js
class Foo {
  constructor() {
    return Object.create(null);
  }
}

console.log(Foo());
// Uncaught TypeError: Class constructor Foo cannot be invoked without 'new'
```

### 类的实例
生成类的实例的写法，和ES5完全一样，也是使用`new`命令。如果忘记加上`new`，像函数那样调用`Class`，会导致报错。
```js
class Point {

}

// 报错
var point = Point(2, 3);

// 正确
var point = new Point(2, 3);
```
与ES5一样，实例的属性除非显式定义在其本身（即定义在`this`对象上），否则都是定义在原型（即定义在`class`上）。
```js
// 定义类
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return `(${this.x}, ${this.y})`;
  }
}

let point = new Point(2, 3);

console.log(point.toString()); // (2, 3)

console.log(point.hasOwnProperty('x')); // true
console.log(point.hasOwnProperty('y')); // true
console.log(point.hasOwnProperty('toString')); // false
console.log(point.__proto__.hasOwnProperty('toString')); // true
```
上面代码中，`x`和`y`都是实例对象`point`自身的属性（因为定义在`this`变量上），所以`hasOwnProperty`方法返回`true`，而`toString`是原型对象的属性（因为定义在`Point`类上），所以`hasOwnProperty`方法返回`false`。这些都和ES5的行为保持一致。

和ES5一样的，类的所有实例共享一个原型对象。
```js
class Point {
  constructor() {}
}

var p1 = new Point(1, 2);
var p2 = new Point(3, 4);

console.log(p1.__proto__ === p2.__proto__); // true
```
上面代码中，`p1`和`p2`都是`Point`的实例，它们的原型都是`Point.prototype`，所以`__proto__`属性是相等的。

这意味着，可以通过实例的`__proto__`属性为“类”添加方法。
```js
class Point {
  constructor() { }
};

var p1 = new Point(1, 2);
var p2 = new Point(3, 4);

p1.__proto__.printName = function() { return 'Oops' };

console.log(p1.printName()); // Oops
console.log(p2.printName()); // Oops

var p3 = new Point(5, 6);
console.log(p3.printName()); // Oops
```
上面代码在`p1`的原型上添加了一个`printName`方法，由于`p1`的原型就是`p2`的原型，因此`p2`也可以调用这个方法。而且，此后新建的实例`p3`也可以调用这个方法。这意味着，使用实例的`__proto__`属性改写原型，必须谨慎，不推荐使用，因为这会改变“类”的原始定义，影响到所有实例。


### 取值函数(getter)和存值函数(setter)
和ES5一样，在“类”的内部可以使用`get`和`set`关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为。
```js
class MyClass {
  constructor() { }

  get prop() {
    return 'getter';
  }

  set prop(value) {
    console.log(`setter: ${value}`);
  }
}

let inst = new MyClass();

inst.prop = 123;
// setter: 123

console.log(inst.prop);
// getter
```
上面代码中，`prop`属性有对应的存值函数和取值函数，因此赋值和读取行为都被自定义了。

存值函数和取值函数是设置在属性的Descriptor对象上的。
```js
class CustomHTMLElement {
  constructor(element) {
    this.element = element;
  }

  get html() {
    return this.element.innerHTML;
  }

  set html(value) {
    this.element.innerHTML = value;
  }
}

var descriptor = Object.getOwnPropertyDescriptor(
  CustomHTMLElement.prototype, "html"
);

console.log("get" in descriptor); // true
console.log("set" in descriptor); // true
```
上面代码中，存值函数和取值函数是定义在`html`属性的描述对象上，这和ES5完全一致。

### 属性表达式
类的属性名，可以采用表达式。
```js
let methodName = 'getArea';

class Square {
  constructor(length) { }

  [methodName]() {
    return 'getArea';
  }
}

var square = new Square();

console.log(square.getArea()); // getArea
```
上面代码中，`Square`类的方法名`getArea`，是从表达式得到的。

### Class表达式
与函数一样，类也可以使用表达式的形式定义。
```js
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
```
上面代码使用表达式定义了一个类。需要注意的是，这个类的名字是`Me`，但是`Me`只在Class的内部可用，指代当前类。在Class外部，这个类只能用`MyClass`引用。
```js
let inst = new MyClass();
inst.getClassName();
console.log(Me.name);
// Uncaught ReferenceError: Me is not define
```
上面代码表示，`Me`只在Class内部有定义。

如果类的内部没用到的话，可以省略`Me`，也就是可以写成下面的形式。
```js
const MyClass = class { };
```
采用Class表达式，可以写出立即执行的Class。
```js
let person = new class {
  constructor(name) {
    this.name = name;
  }

  sayName() {
    console.log(this.name);
  }
}('张三');

person.sayName(); // 张三
```
上面代码中，`person`是一个立即执行的类的实例。

## 静态方法
类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上`static`关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。
```js
class Foo {
  static classMethod() {
    return 'hello';
  }
}

console.log(Foo.classMethod());
// hello

var foo = new Foo();
console.log(foo.classMethod());
// Uncaught TypeError: foo.classMethod is not a function
```
上面代码中，`Foo`类的`classMethod`方法前有`static`关键字，表明该方法是一个静态方法，可以直接在`Foo`类上调用（Foo.classMethod()），而不是在`Foo`类的实例上调用。如果在实例上调用静态方法，会抛出一个错误，表示不存在该方法。

注意，如果静态方法包含`this`关键字，这个`this`指的是类，而不是实例。
```js
class Foo {
  static bar() {
    this.baz();
  }

  static baz() {
    console.log('hello');
  }

  baz() {
    console.log('world');
  }
}

Foo.baz(); // hello
```
上面代码中，静态方法`bar`调用了`this.baz`，这里的`this`指的是`Foo`类，而不是`Foo`的实例，等同于调用`Foo.baz`。另外，从这个例子还可以看出，静态方法可以与非静态方法重名。

父类的静态方法，可以被子类继承。
```js
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
  constructor() {
    super();
  }
}

console.log(Bar.classMethod()); // hello
```
上面代码中，父类`Foo`有一个静态方法，子类`Bar`可以调用这个方法。

静态方法也可以从`super`对象上调用的。
```js
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
  static classMethod() {
    return super.classMethod() + ', too';
  }
}

console.log(Bar.classMethod()); // hello, too
```

## 实例属性的新写法

实例属性除了定义在`constructor()`方法里面的`this`上面，也可以定义在类的最顶层。
```js
class IncreasingCounter {
  constructor() {
    this._count = 0;
  }

  get value() {
    console.log('getting the current value!');
    return this._count;
  }

  increment() {
    this._count++;
  }
}
```
上面代码中，实例属性`this_count`定义在`constructor()`方法里面。另一种写法是，这个属性也可以定义在类的最顶层，其他都不变。
```js
class IncreasingCounter {
  _count = 0;
  
  get value() {
    console.log('getting the current value!');
    return this._count;
  }

  increment() {
    this._count++;
  }
}
```
上面代码中，实例属性`_count`和取值函数`value()`和`increment()`方法，处于同一个层级。这时，不需要在实例属性前面加上`this`。

这种新写法的好处是，所有实例对象自身的属性都定义在类的头部，看上去比较整齐，一眼就能看出这个类都有哪些实例属性。
```js
class Foo {
  bar = 'hello';
  baz = 'world';

  constructor() {
    
  }
}
```
上面的代码，一眼就能看出，`foo`类有两个实例属性，一目了然。另外，写起来也比较简洁。

## 静态属性
静态属性指的是Class本身的属性，即`Class.propName`，而不是定义在实例对象（`this`）上的属性。
```js
class Foo {
}

Foo.prop = 1;
console.log(Foo.prop); // 1
```
上面的写法为`Foo`类定义了一个静态属性`prop`。因为ES6明确规定，Class内部只有静态方法，没有静态属性。


ES7有一个静态属性的提案，这个提案对实例属性和静态属性都规定了新的写法。在实例属性前面加上`static`关键字，这就表示该属性是静态属性。
```js
class MyClass {
  static myStaticProp = 42;

  constructor() {
    console.log(MyClass.myStaticProp); // 42
  }
}

var myclass = new MyClass();
```
这个新写法大大方便了静态属性的表达。
```js
// 老写法
class Foo {
}

Foo.prop = 1;

// 新写法
class Foo {
  static prop = 1;
}
```
上面代码中，老写法的静态属性定义在类的外部。整个类生成以后，再生成静态属性。这样让人很容易忽略这个静态属性，也不符合相关代码应该放在一起的代码组织原则。另外，新写法是显式声明，而不是赋值处理，语义更好。

## 私有方法和私有属性
私有方法和私有属性，是只能在类的内部访问的方法和属性，外部不能访问。这是常见的需求，有利于代码的封装，但ES6没有提供，只能通过变通方法模拟实现。

一种做法就是在命名上区别。
```js
class Widget {
  // 公有方法
  foo() {
    this._bar(baz);
  }

  // 私有方法
  _bar(baz) {
    return this.snaf = baz;
  }

  // ...
}
```
上面代码中，`_bar`方法前面的下划线，表示这是一个只用于内部的私有方法。但是，这种命名并不保险，在类的外部，还是可以调用这个方法的。

另一种方法就是索性将私有方法移出模块，因为模块内部的所有方法都是对外可见的。
```js
class Widget {
  foo (baz) {
    bar.call(this, baz);
  }
}

function bar(baz) {
  return this.snaf = baz;
}
```
上面代码中，`foo`是公开方法，内部调用了`bar.call(this, baz)`。这使得`bar`实际上成为了当前模块的私有方法。

还有一种方法是利用`Symbol`值的唯一性，将私有方法的名字命名为一个`Symbol`值。
```js
const bar = Symbol('bar');
const snaf = Symbol('snaf');

class MyClass {

  // 公有方法
  foo(baz) {
    this[bar](baz);
  }

  // 私有方法
  [bar](baz) {
    return this[snaf] = baz;
  }

  //...
};
```
上面代码中，`bar`和`snaf`都是`Symbol`值，一般情况下无法获取到它们，因此达到了私有方法和私有属性的效果。但是也不是绝对不行，`Reflect.ownKeys()`可以拿到它们。
```js
const bar = Symbol('bar');
const snaf = Symbol('snaf');

class MyClass {

  // 公有方法
  foo(baz) {
    this[bar](baz);
  }

  // 私有方法
  [bar](baz) {
    return this[snaf] = baz;
  }

  //...
};

const inst = new MyClass();

console.log(Reflect.ownKeys(MyClass.prototype));
// ["constructor", "foo", Symbol(bar)]
```
上面代码中，Symbol值的属性名依然可以从类的外部拿到。

### 私有属性的提案
目前，有一个提案，为`class`加上私有属性。方法是在属性名之前，使用`#`表示。
```js
class IncreasingCounter {
  #count = 0;

  get value() {
    console.log('getting the current value!');
    return this.#count;
  }

  increment() {
    this.#count++;
  }
}

const counter = new IncreasingCounter();

console.log(counter.#count);
// Uncaught SyntaxError: Private field '#count' must be declared in an enclosing class
counter.#count = 42;
```
上面的代码中，`#count`就是一个私有属性，只能在类的内部使用（`this.#count`）。如果在类的外部使用，就会报错。

看下面的例子。
```js
class Point {
  #x;

  constructor(x = 0) {
    this.#x = +x;
  }

  get x() {
    return this.x;
  }

  set x(value) {
    this.#x = +value;
  }
}
```
上面代码中，`#x`就是私有属性，在`Point`类之外是读取不到这个属性的。由于井号`#`是属性名的一部分，使用时必须带有`#`一起使用，所以`#x`和`x`是两个不同的属性。

这种写法不仅可以写私有属性，还可以用来写私有方法。
```js
class Foo {
  #a;
  #b;

  constructor(a, b) {
    this.#a = a;
    this.#b = b;
  }

  #sum() {
    return this.#a + this.#b;
  }

  printSum() {
    console.log(this.#sum());
  }
}

var foo = new Foo(1, 2);

foo.printSum();
// 3
```
上面代码中，`#sum()`就是一个私有方法。

另外，私有属性也可以设置getter和setter方法。
```js
class Counter {
  #xValue = 0;

  constructor() {
  }

  get #x() { return this.#xValue; };

  set #x(value) {
    this.#xValue = value;
  }

  printValue(bool) {
    if (bool) {
      console.log(this.#xValue);
    } else {
      console.log('setting');
      this.#xValue = 10;
    }
  }
}

const counter = new Counter();

counter.printValue(false); // setting
counter.printValue(true); // 10
```
上面代码中，`#x`是一个私有属性，它的读写通过`get #x()`和`set #x()`来完成。

私有属性不限于从`this`引用，只要在类的内部，实例也可以引用私有属性。
```js
class Foo {
  #privateValue = 42;

  static getPrivateValue(foo) {
    return foo.#privateValue;
  }
}

console.log(Foo.getPrivateValue(new Foo())); // 42;
```
上面代码允许从实例`foo`上面引用私有属性。

私有属性和私有方法前面，也可以加上`static`关键字，表示这是一个静态的私有属性和私有方法。
```js
class FakeMath {
  static PI = 22 / 7;
  static #totallyRandomNumber = 4;

  static #computeRandomNumber() {
    return FakeMath.#totallyRandomNumber;
  }

  static random() {
    console.log('I heard you like random nubmers');
    return FakeMath.#computeRandomNumber();
  }
};

console.log(FakeMath.PI); // 3.142857142857143
console.log(FakeMath.random());
// I heard you like random nubmers
// 4

console.log(FakeMath.#totallyRandomNumber);
// Uncaught SyntaxError: Private field '#totallyRandomNumber' must be declared in an enclosing class
console.log(FakeMath.#computeRandomNumber());
// Uncaught SyntaxError: Private field '#totallyRandomNumber' must be declared in an enclosing class
```
上面代码中，`#totallyRandomNumber`是私有属性，`#computeRandomNumber()`是私有方法，只能在`FakeMath`这个类的内部调用，外部调用就会报错。

## new.target属性
`new`是从构造函数生成实例对象的命令。ES6为`new`命令引入了一个`new.target`属性，这个属性一般用在构造函数之中，返回`new`命令作用于的那个构造函数。如果构造函数不是通过`new`命令或`Reflect.constructor()`调用的，`new.target`会返回`undefined`，因此这个属性可以用来确定构造函数是怎么调用的。
```js
function Person(name) {
  if (new.target !== undefined) {
    this.name = name;
  } else {
    throw new Error('必须使用 new 命令生成实例');
  }
}

// 另一种写法
function Preson(name) {
  if (new.target === Person) {
    this.name = name;
  } else {
    throw new Error('必须使用 new 命令生成实例');
  }
}

var person = new Person('张三'); // 正确的写法
var notAPerson = Person.call(person, '张三');
// 19.6.html:21 Uncaught Error: 必须使用 new 命令生成实例
```
上面代码中确保构造函数只能通过`new`命令调用。

Class内部调用`new.target`，返回当前Class。
```js
class Rectangle {
  constructor(length, width) {
    console.log(new.target === Rectangle);
    this.length = length;
    this.width = width;
  }
}

var obj = new Rectangle(3, 4); // 控制台打印出true
```
需要注意的是，子类继承父类时，`new.target`会返回子类。
```js
class Rectangle {
  constructor(length, width) {
    console.log(new.target === Rectangle);
  }
}

class Square extends Rectangle {
  constructor(length, width) {
    super(length, width);
  }
}

let obj = new Square(3); // 控制台打印出false
```
利用这个特点，可以写出不能独立使用、必须继承后才能使用的类。
```js
class Shape {
  constructor() {
    if (new.target === Shape) {
      throw new Error('本类不能实例化');
    }
  }
}

class Rectangle extends Shape {
  constructor(length, width) {
    super();
  }
}

let x = new Shape();
// Uncaught Error: 本类不能实例化

let y = new Rectangle(3, 4); // 正确的写法
```
上面代码中，`Shape`类不能被实例化，只能用于继承。

注意，在函数外部，使用`new.target`会报错。