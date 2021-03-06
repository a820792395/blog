# async函数
      
## 含义
async函数是Generator函数的语法糖。来看个例子。
```js
function* gen() {
  yield 1;
  yield 2;
}

let g = gen();

console.log(g.next()); // {value: 1, done: false}
console.log(g.next()); // {value: 2, done: false}

async function number() {
  let num1 = await 1;
  let num2 = await 2;
  console.log(num1);
  console.log(num2);
}

number();
```
一比较就会发现，`async`函数把Generator函数的星号（`*`）替换成`async`，把`yield`替换成`await`。

`async`函数对Generator函数的改进，有以下四点。

#### 1.内置执行器 
Generator函数的执行必须靠执行器，所以才有`co`模块，而`async`函数自带执行器。也就是说，`async`函数的执行，和普通的函数一模一样，
只要一行。
```js
number();
```
上面的代码调用了`number`函数，然后就会自动执行，输出最后结果。这完全不像Generator函数，需要调用`next`方法，或者用`co`模块，才能真正执行，得到最后结果。

#### 2. 更好的语义。
`async`和`await`，比起星号和`yield`，语义更加清楚。`async`表示函数里有异步操作，`await`表示紧跟在后面的表达式需要等待结果。

#### 3. 更广的实用性
`co`模块约定，`yield`命令后面只能是Thunk函数或Promise对象，而`async`函数的`await`命令后面，可以是Promise对象和原始类型的值（数值、字符串和布尔值，但这时会自动转成立即resolved的Promise对象）。

#### 4. 返回值时Promise
`async`函数的返回值时Promise对象，这比Generator函数的返回值时Iterator对象方便多了。可以用`then`方法指定下一步的操作。

进一步说，`async`函数完全可以看作多个异步操作，包装成的一个Promise对象，而`await`命令就是内部`then`命令的语法糖。

## 基本用法
`async`函数返回一个Promise对象，可以使用`then`方法添加回调函数。当函数执行的时候，遇到`await`就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。

来看个例子，指定多少毫秒后输出一个值。
```js
function timeout(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
};

async function asyncPrint(value, ms) {
  await timeout(ms);
  console.log(value);
}

asyncPrint('hello, world', 50);
```
上面代码指定50毫秒以后，输出`hello world`。

由于`async`函数返回的是Promise对象，可以作为`await`命令的参数。所以，上面的例子也可以写成下面的形式。
```js
async function timeout(ms) {
  await new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}

async function asyncPrint(value, ms) {
  await timeout(ms);
  console.log(value);
}

asyncPrint('hello world', 50);
// hello world
```
async函数有多种使用形式。
```js
// 函数声明
async function foo() { };

// 函数表达式
const foo = async function() { };

// 对象的方法
let obj = { async foo() { } };
obj.foo().then();

// Class方法
class Storage {
  constructor() {
    this.cachePromise = caches.open('avatars');
  }

  async getAvatar(name) {
    const cache = await this.cachePromise;
    return cache.match(`/avatars/${name}.jpg`);
  }
}

const storage = new Storage();
storage.getAvatar('jake').then();

// 箭头函数
const foo = async () => { };
```

## 语法
`async`函数的语法规则总体上比较简单，但是难点就在于错误处理机制。

### 返回Promise对象
`async`函数返回一个Promise对象。
`async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。
```js
async function f() {
  return 'hello world';
}

f().then(res => console.log(res));
// hello world
```
上面代码中，函数`f`内部`return`操作符返回的值，会被`then`方法回调哈桑农户接收。

`async`函数内部抛出错误，会导致返回的Promise对象变为`reject`状态。抛出的错误对象会被`catch`方法回调函数接收到。
```js
async function f() {
  throw new Error('又有bug了');
}

f().then(
  v => console.log('resolve', v),
  e => console.log('reject', e)
)
// reject Error: 又有bug了
```

### Promise对象的状态变化
`async`函数返回的Promise对象，必须要等到内部所有`await`命令后面的Promise对象执行完，才会发生状态改变，除非遇到`return`语句或者抛出错误。也就是说，只有`async`函数内部的异步操作执行完，才会执行`then`方法指定的回调函数。

看下面的例子。
```js
async function getTitle(url) {
  let response = await fetch(url);
  let html = await response.text();
  return html.match(/<title>([\s\S]+)<\/title>/i)[1];
}
getTitle('https://tc39.github.io/ecma262/').then(console.log);
```
上面代码中，函数`getTitle`内部有三个操作：抓取网页、取出文本、匹配页面标题。只有这三个操作全部完成，才会执行`then`方法里面的`console.log`。

### await命令
正常情况下，`await`命令后面是一个Promise对象，返回该对象的结果。如果不是Promise对象，就直接返回对应的值。
```js
async function f() {
  // 等同于
  // return 123;
  return await 123;
}

f().then(res => console.log(res));
// 123
```
上面代码中，`await`命令的参数是数值`123`，这时等同于`return 123`。

另一种情况是，`await`命令后面是一个`thenable`对象（即定义了`then`方法的对象），那么`await`会将其等同于Promise对象。
```js
class Sleep {
  constructor(timeout) {
    this.timeout = timeout;
  }

  then(resolve, reject) {
    const startTime = Date.now();
    setTimeout(() => {
      resolve(Date.now() - startTime),
      this.timeout
    });
  }
}

(async () => {
  const sleepTime = await new Sleep(1000);
  console.log(sleepTime);
})();
```
上面代码中，`await`命令后面是一个`Sleep`对象的实例。这个实例不是Promise对象，但是定义了`then`方法，`await`会将其是为`Promise`处理。

JavaScript一直没有休眠的语法，但是借助`await`命令就可以让程序停顿指定的时间。下面给出一个简化的`sleep`实现。
```js
function sleep(interval) {
  return new Promise(resolve => {
    setTimeout(resolve, interval);
  });
}

// 用法
async function one2FiveInAsync() {
  for(let i = 1; i <= 5; i++) {
    console.log(i);
    await sleep(1000);
  }
}

one2FiveInAsync();
```
`await`命令后面的Promise对象如果变为`reject`状态，则`reject`的参数会被`catch`方法的回调函数接收到。
```js
async function f() {
  await Promise.reject('又有bug了');
}

f()
  .then(res => console.log(res))
  .catch(err => console.log(err));
  // 又有bug了
```
上面的代码中，`await`语句前面没有`return`，但是`reject`方法的参数还是传入了`catch`方法的回调函数中。这里如果在`await`前面加上`return`，效果是一样的。

任何一个`await`语句后面的Promise对象变为`reject`状态，整个`async`函数都会中断执行。
```js
async function f() {
  await Promise.reject('又有bug了');
  
  // 下面的await语句不会执行
  await Promise.resolve('hello world');
}

f();
```
上面代码中，第二个`await`语句是不会执行的，因为第一个`await`语句状态变成了`reject`。

有时候，希望一个异步操作失败，也不会中断后面的异步操作，可以把第一个`await`放在`try...catch`结构里面，这样不管这个异步操作是否成功，第二个`await`都会执行。
```js
async function f() {
  try {
    await Promise.reject('哎呀，出bug了');
  } catch (e) {
    console.log(e);
  }
  return await Promise.resolve('hello world');
}

f()
  .then(res => console.log(res));
  // 哎呀，出bug了
  // hello world
```
另一种方法是`await`后面的Promise对象再跟一个`catch`方法，处理前面可能出现的错误。
```js
async function f() {
  await Promise.reject('哎呀，又有bug了')
    .catch(e => console.log(e));
  return await Promise.resolve('hello world');
}
f()
  .then(res => console.log(res));
// 哎呀，又有bug了
// hello world
```

### 错误处理
如果`await`后面的异步操作出错了，就等于`async`函数返回的Promise对象被`reject`。
```js
async function f() {
  await new Promise((resolve, reject) => {
    throw new Error('又有bug了');
  });
}

f()
  .then(res => {
    console.log(res);
  })
  .catch(err => {
    console.log(err);
  });
  // Error: 又有bug了
```
上面代码中，`async`函数`f`执行后，`await`后面的Promise对象会抛出一个错误对象，导致`catch`方法的回调函数被调用，它的参数就是抛出的错误对象。

为了防止出错的方法，最好把`await`代码块放在`try...catch`代码块中。
```js
async function f() {
  try {
    await new Promise((resolve, reject) => {
      throw new Error('又有bug了');
    });
  } catch(e) {
    console.log(e);
  }
  return await('hello world')
}

f();
// Error: 又有bug了
```
如果有多个`await`命令，可以统一放在`try...catch`结构中。
```js
function foo1() {
  return new Promise((resolve, reject) => {
    resolve(false);
  });
}

function foo2(val) {
  return new Promise((resolve, reject) => {
    if (val) {
      resolve('hello');
    } else {
      reject('出错啦！');
    }
  });
}

async function main() {
  try {
    const val1 = await foo1();
    const val2 = await foo2(val1);
    console.log(val2);
  } catch (e) {
    console.log(e);
  }
}

main();
```
下面的例子使用`try...catch`结构，实现多次重复尝试。
```js
function foo(val) {
  return new Promise((resolve, reject) => {
    if (val) {
      resolve(val);
    } else {
      reject('出错啦');
    }
  });
};

const NUM_RETRIES = 3;

async function test() {
  let i;
  for (i = 0; i < NUM_RETRIES; ++i) {
    try {
      await foo(false);
      break;
    } catch(err) {
      console.log(err);
    }
  }
  console.log(i);
}

test();
```
上面代码中，如果`await`操作成功，就会使用`break`语句退出循环；如果失败，会被`catch`语句捕捉，然后进入下一轮循环。

### 使用注意点
第一点，`await`命令后面的`Promise`对象，运行结果如果是`rejected`，最好把`await`命令放在`try...catch`代码块中。
```js
async function asyncFunc1() {
  try {
    await foo();
  } catch(err) {
    console.log(err);
  }
}

// 另一种写法
async function asyncFunc2() {
  await foo()
    .cacth(err => {
      console.log(err);
    });
}
```
第二点，多个`await`命令后面的异步操作，如果不存在依赖关系的话，最好让它们同时触发。
```js
async function asyncFunc1() {
  let foo = await getFoo();
  let bar = await getBar();
}
```
上面代码中，`getFoo`和`getBar`是两个独立的异步操作（互不依赖），被写成继发关系。这样就比较耗时，因为只有`getFoo`完成以后，才会执行`getBar`，完全可以让它们同时触发。
```js
// 写法一
let [foo, bar] = await Promise.all([getFoo(), getBar()]);

// 写法二
let fooPromise = getFoo();
let barPromise = getBar();
let foo = await fooPromise;
let bar = await barPromise;
```
上面两种写法，`getFoo`和`getBar`都是同时触发，这样就会缩短程序的执行时间。

第三点，`await`命令只能用在`async`函数之中，如果用在普通函数，就会报错。
```js
async function dbFunc(db) {
  let docs = [{}, {}, {}];

  // 报错
  docs.forEach(function(doc) {
    await db.post(doc);
  })
}

dbFunc(1);
// SyntaxError: await is only valid in async function
```
上面代码会报错，因为`await`用在普通函数之中了。但是，如果将`forEach`方法的参数改成`async`函数，也会有问题。
```js
function dbFunc(db) { // 这里不需要async
  let docs = [{}, {}, {}];
  
  //可能得到错误结果
  docs.forEach(async function(doc) {
    await db.post(doc);
  })
}

const obj = {
  post(val) {
    console.log(val);
  }
}

dbFunc(obj);
```
上面代码可能不会正常工作，原因是这时三个`db.post`操作将是并发执行，也就是同时执行，而不是继发执行。正确的写法是采用`for`循环。
```js
const obj = {
  post(val) {
    console.log(val);
  },
};

async function dbFunc(db) {
  let docs = [{}, {}, {}];

  for (let doc of docs) {
    await db.post(doc);
  }
};

dbFunc(obj);
```

如果确实希望多个请求并发执行，可以使用`Promise.all`方法。当三个请求都会`resolved`时，下面两种写法效果相同。
```js
async function dbFuc(db) {
  let docs = [{}, {}, {}];
  let promises = docs.map((doc) => db.post(doc));

  let results = await Promise.all(promises);
  console.log(results);
}

// 或者使用下面的写法
async function dbFuc(db) {
  let docs = [{}, {}, {}];
  let promises = docs.map((doc) => db.post(doc));

  let results = [];
  for (let promise of promises) {
    results.push(await promise);
  }
  console.log(results);
}
```
第四点，async函数可以保留运行堆栈。
```js
const a = () => {
  b().then(() => c());
}
```
上面代码中，函数`a`内部运行了一个异步任务`b()`。当`b()`运行的时候，函数`a()`不会中断，继续执行。等到`b()`运行结束，可能`a()`早就结束了，`b()`所在的上下文环境已经消失了。如果`b()`或`c()`报错，错误堆栈将不包括`a()`。

把上面的例子改写成`async`函数。
```js
const a = async () => {
  await b();
  c();
}
```
上面的代码中，`b()`运行的时候，`a()`是暂停执行，上下文环境都保存着。一旦`b()`或`c()`报错，错误堆栈将包括`a()`。

## async函数的实现原理
async函数的实现原理就是把Generator函数和自动执行器，包装在一个函数里面。
```js
async function fn(args) {
  // ...
};

// 等同于
function fn(args) {
  return spawn(function* () {
    // ...
  })
};
```
所有的`async`函数都可以写成上面的第二种形式，其中的`spawn`函数就是自动执行器。

下面是`spawn`函数的实现以及使用方法。
```js
function spawn(genF) {
  return new Promise((resolve, reject) => {
    const gen = genF();

    function step(nextF) {
      let next;
      try {
        next = nextF();
      } catch (e) {
        return reject(e);
      }

      if (next.done) {
        return resolve(next.value);
      }

      Promise.resolve(next.value).then((value) => {
        step(() => gen.next(value));
      }, () => {
        step(() => gen.throw(e));
      })
    }
    step(() => gen.next(undefined));
  })
}

function fn(args) {
  return spawn(function* () {
    let nums = [1, 2, 3, 4, 5];
    console.log('进入', nums.concat(args));
  });
};

fn([6, 7, 8]);
// 进入 (8) [1, 2, 3, 4, 5, 6, 7, 8]
```

## 实例：按顺序完成异步操作
在实际开发中，经常遇到一组异步操作，需要按照顺序完成。比如，依次远程读取一组URL，然后按照读取的顺序输出结果。

下面是Promise的写法。
```js
function logInOrder(urls) {
  // 远程读取所有URL
  const textPromises = urls.map(url => {
    return fetch(url).then(res => res.text());
  });

  // 按次序输出
  textPromises.reduce((chain, textPromise) => {
    return chain.then(() => textPromise)
      .then(text => console.log(text));
  }, Promise.resolve());
}
const urls = [
  'https://www.easy-mock.com/mock/5c3202882188f1589db6ae9d/example/mock', 
  'https://www.easy-mock.com/mock/5c3202882188f1589db6ae9d/example/query'
];
logInOrder(urls);
```
上面代码中使用`fetch`方法，同时远程读取一组URL。每个`fetch`操作都返回一个Promise对象，放进`textPromise`数组里。然后，`reduce`依次处理每个Promise对象，然后使用`then`，把所有Promise对象连起来，因此就可以依次输出结果。

但是这种写法不太直观，可读性比较差，使用async函数改造一下。
```js
async function logInOrder(urls) {
  for (const url of urls) {
    const response = await fetch(url);
    console.log(await response.text());
  }
}

const urls = [
  'https://www.easy-mock.com/mock/5c3202882188f1589db6ae9d/example/mock', 
  'https://www.easy-mock.com/mock/5c3202882188f1589db6ae9d/example/query'
];
logInOrder(urls);
```
上面代码大大简化了，但是所有远程操作都是继发的，只有前一个URL返回结果了，才会去读取下一个URL，这样效率就会很低，浪费时间。而我们需要的是并发发出请求。
```js
async function logInOrder(urls) {
  // 并发读取远程URL
  const textPromises = urls.map(async (url) => {
    const response = await fetch(url);
    return response.text();
  });

  for (let textPromise of textPromises) {
    console.log(await textPromise);
  }
}

const urls = [
  'https://www.easy-mock.com/mock/5c3202882188f1589db6ae9d/example/mock', 
  'https://www.easy-mock.com/mock/5c3202882188f1589db6ae9d/example/query'
];

logInOrder(urls);
```
上面的代码中，虽然`map`方法的参数是`async`函数，但它是并发执行的，因为只有`async`函数内部是继发执行，外部不受影响。后面的`for...of`循环内部使用了`await`，因此实现了按顺序输出。