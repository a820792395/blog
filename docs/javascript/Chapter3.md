# 基础
## 变量
定义变量时要使用`var`操作符，后跟变量名，如下所示：
```js
  var message;
```
也支持直接初始化变量，因此在定义变量的同时就可以设置变量的值，如下所示：
```js
  var message = "hi";
```
在此，变量`message`中保存了一个字符串值`"hi"`。像这样初始化变量并不会把它标记为字符串类型；初始化的过程就是给变量赋一个值那么简单。因此可以在修改变量值的同时修改值的类型，如下所示：
```js
  var message = "hi";
  message = 10;
  console.log(message); // 10
```
有一点必须注意，即用`var`操作符定义的变量将成为定义该变量的作用域中的局部变量。也就是说，如果在函数中使用`var`定义一个变量，那么这个变量在函数退出后就会被销毁，例如：
```js
  function test() {
    var message = "hi";
  }
  test();
  console.log(message); // ReferenceError: message is not defined
```
不过，可以像下面这样省略`var`操作符，从而创建一个全局变量：
```js
function test() {
  message = "hi";
}
test();
console.log(message); // hi
```
这个例子省略了`var`操作符，因而`message`就成了全局变量。这样，只要调用一次`test()`函数，这个变量就有了定义，就可以在函数外部的任何地方被访问到。

可以使用一条语句定义多个变量，只要像下面这样把每个变量（初始化或不初始化均可）用逗号分隔开即可：
```js
var message = "hi",
  found = false,
  age = 29;
```
## 数据类型
ECMAScript中有`5`种简单数据类型（也称为基本数据类型）：`Undefined、Null、Boolean、Number`和`String`。还有一种复杂数据类型--`Object`。

typeof操作符
鉴于ECMAScript是松散类型的，因此需要有一种手段来检测给定变量的数据类型----`typeof`就是负责提供这方面信息的操作符。对一个值使用typeof操作符可能返回下列某个字符串：
* `"undefined"`：表示这个值未定义；
* `"boolean"`：表示这个值是布尔值；
* `"string"`：表示这个值是字符串；
* `"number"`：表示这个值是数值；
* `"object"`：表示这个值是对象或者`null`；
下面是几个使用`typeof`操作符的例子：
```js
  var message = "some string";
  console.log(typeof message); // string
  console.log(typeof (message));// string
  console.log(typeof 95); // number
  console.log(typeof null); // object
```
有时候，`typeof`操作符会返回一些令人迷惑但技术上却正确的值。比如，调用`typeof null`会返回`"object"`，因为特殊值`null`被认为是一个空的对象引用。

### Undefined类型
`Undefined`（未定义）类型只有一个值，即特殊的`undefined`。在使用`var`声明变量但未对其加初始化时，这个变量值就是`undefined`，例如：
```js
  var message;
  console.log(message); // undefined
```
### Null类型
`Null`（空）类型是第二个只有一个值的数据类型，这个特殊的值是`null`。从逻辑角度来看，`null`值表示一个空对象指针，而这也正是使用`typeof`操作符检测`null`值是会返回`"object"`的原因，如
下面的例子所示：
```js
  var car = null;
  console.log(typeof null); // object
```
### Boolean类型
`Boolean`类型是ECMAScript中使用最多的一种类型，该类型只有两个字面值：`true`和`false`。在`Number`类型里面`1`表示`true`、`0`表示`false`。
```js
  var found = true;
  var lost = false;
```
### Number类型
JavaScript里只有一种数值类型。
```js
var intNum = 55
```
#### 浮点数值
所谓浮点数值，就是该数值中必须包含一个小数点，并且小数点后面必须至少有一位数字。
```js
var floatNum1 = 1.1;
var floatNum2 = 0.1;
```
对于那些极大或极小的数值，可以用e表示法表示的浮点数值表示。用e表示法表示的数值等于e前面的数值乘以10的指数次幂。ECMAScript中e表示法的格式也是如此，即前面是一个数值（可以是整数也可以是浮点数），中间是一个大写或小写的字母E，后面是10的幂中的指数，该幂值将用来与前面的数相乘，下面是一个使用e表示法表示数值的例子：
```js
  var floatNum1 = 3.125e7;
  console.log(floatNum1); // 31250000
```
也可以使用e表示法表示极小的数值，看下面的例子：
```js
  var floatNum1 = 0.00000000000000003;
  console.log(floatNum1); // 3e-17
```
在默认情况下，ECMAScript会将那些小数点后面带有6个零以上的浮点数值转换为以e表示法表示的数值。

#### 数值范围
如果某次计算的结果得到了一个超出JavaScript数值范围的值，那么这个数值将被自动转换成特殊的Infinity。具体来说，如果这个数值是负数，则会被转换成-Infinity（负无穷），如果这个数值是正数，则会被转换成Infinity（正无穷）。

#### NaN
NaN，即非数值（Not a Number）是一个特殊的数值，这个数值用于表示本来要返回数值的操作数未返回数值的情况（这样就不会抛出错误了）。在ECMAScript中，任何数值除以非数值会返回NaN，因此不会影响其他代码的执行。

#### 数值转换
有3个函数可以把非数值转换为数值：Number()、parseInt()和parseFloat()

#### String类型
String类型即字符串，字符串可以由双引号（"）或单引号（'）表示，因此下面两种字符串的写法都是
有效的：
```js
  var color = "blue";
  var name = 'xiaoli';
```
#### 转换为字符串
要把一个值转换为一个字符串可以通过toString()方法。看下面的例子：
```js
  var age = 11;
  var ageAsString = age.toString();
  var found = true;
  var foundAsString = found.toString();
  console.log(ageAsString); // 字符串 "11"
  console.log(foundAsString);// 字符串 "true"
```
### Object类型
ECMAScript中的对象其实就是一组数据和功能的集合。对象可以通过执行new操作符后跟要创建的对象类型的名称来创建。而创建Object类型的实例并其添加属性和（或）方法，就可以创建自定义对象，如下所示：
```js
  var o = new Object();
```
## 操作符
### 一元操作符
#### 递增和递减操作符
只能操作一个值的操作符叫做一元操作符。递增和递减操作符，分为前置型和后置型。顾名思义，前置型应该位于要操作的变量之前，
而后置型则应该位于要操作的变量之后。
```js
  var age = 29;
  ++age;
  console.log(age); // 30
```
在这个例子中，前置递增操作符把age的值变成了30。实际上，执行这个前置递增操作与执行以下操作的效果相同：
```js
  var age = 29;
  age = age + 1;
```
执行前置递减操作的方法也类似，结果会从一个数值中减去1。使用前置递减操作符，要把两个减号（--）放在相应变量的前面，如下所示：
```js
  var age = 29;
  --age;
  console.log(age); // 28
```
执行前置递增和递减操作时，变量的值都是在语句被求值以前改变的。（在计算机科学领域，这种情况通常被称作副效应。）看下面的例子。
```js
  var age = 29;
  var anotherAge = --age + 2;
  console.log(age); // 28
  console.log(anotherAge); // 30
```
这个例子中变量anotherAge的初始值等于变量age的值前置递减之后加2.由于先执行了减法操作，age的值变成了28，所以再加上2的结果就是30。
:::warning 警告
由于前置递增和递减操作与执行语句的优先级相等，因此整个语句会从左至右被求值。
:::

后置型递增和递减操作符的语法不变，只不过要放在变量的后面而不是前面。后置递增和递减与前置递增和递减一个非常重要的区别，即递增和递减操作是在包含它们的语句被求值之后才执行的。看下面的例子：
```js
  var num1 = 2;
  var num2 = 20;
  var num3 = num1-- + num2;
  var num4 = num1 + num2;

  console.log(num3); // 22
  console.log(num4); // 21
```
在这个例子中，num3等于22，num4等于21。这里在计算num3时使用了num1的原始值完成了加法计算，而num4则使用了递减后的值。

所有这4个操作符对任何值都适用，也就是它们不仅适用于整数，还可以用字符串、布尔值、浮点数值和对象。在应用于不同的值是，递增和递减操作符遵循下列规则。
* 在应用于一个包含有效数字字符的字符串时，先将其转换为数字值，再执行加减1的操作。字
* 符串变量变成数值变量。
* 在应用一个不包括有效数字字符的字符串时，将变量的值设置为NaN。字符串变量变成数值变量。
* 在应用布尔值false时，先将其转换为再0执行加减1的操作。布尔值变量变成数值变量。
* 在应用于布尔值true是，先将其转换为1再执行加减1的操作。布尔值变量变成数值变量。
* 在应用于浮点数值时，执行加减1的操作。
* 在应用于对象是，先调用对象的valueOf()方法以取得一个可供操作的值。然后对该值应用前述规则。如果结果是NaN，则调用toString()方法后再应用前述规则。对象变量变成数值变量。
看下面的例子：
```js
  var s1 = "2";
  var s2 = "z";
  var b = false;
  var f = 1.1;
  var o = {
    valueOf: function() {
      return -1;
    }
  };

  s1++;
  s2++;
  b++;
  f--;
  o--;

  console.log(s1); // 3
  console.log(s2); // NaN
  console.log(b); // 1
  console.log(f); // 0.10000000000000009（由于浮点数舍入错误所致）
  console.log(o); // -2
```
#### 一元加和减操作符
一元加操作以一个加号表示，放在数值前面，对数值不会产生任何影响。看下面的例子：
```js
  var num = 25;
  num = +num;
  console.log(num); // 25
```
不过，在对非数值应用一元加操作符时，该操作符会像Number()转型函数一样对这个执行转换。换句话说，布尔值false和true将被转换为0和1，字符串只会被按照一组特殊的规则进行解析，而对象是线调用它们的valueOf()和toString()方法，再转换得到的值。下面的例子展示了对不同数据类型应用一元加操作符的结果：
```js
  var s1 = "01";
  var s2 = "1.1";
  var s3 = "z";
  var b = false;
  var f = 1.1;
  var o = {
    valueOf: function() {
      return -1;
    }
  };

  s1 = +s1;
  s2 = +s2;
  s3 = +s3;
  b = +b;
  f = +f;
  o = +o;

  console.log(s1); // 1
  console.log(s2); // 1.1
  console.log(s3); // NaN
  console.log(b); // 0
  console.log(f); // -1.1
  console.log(o); // -1
```
一元减操作符主要表示负数，例如将1转换成-1。下面的例子演示了这个简单的转换过程：
```js
  var num = 25;
  num = -num;
  console.log(num); // -25
```
在将一元减操作符应用于数值时，该值会变成负数。而当应用于非数值时，一元减操作遵循与一元加操作符相同的规则，最后再将得到的数值转换为负数，如下的例子所示：
```js
var s1 = "01";
var s2 = "1.1";
var s3 = "z";
var b = false;
var f = 1.1;
var o = {
  valueOf: function() {
    return -1;
  }
};

s1 = -s1;
s2 = -s2;
s3 = -s3;
b = -b;
f = -f;
o = -o;

console.log(s1); // -1
console.log(s2); // -1.1
console.log(s3); // NaN
console.log(b); // -0
console.log(f); // -1.1
console.log(o); // 1
```
一元加和减操作符主要用于基于的算术运算，也可以像前面示例展示的一样用于转换数据类型。
### 位操作符
位操作符用于在最基本的层次上，即按内存中表示数值的位来操作数值。ECMAScript中的所有数值都以IEEE-754 64位格式存储，但位操作符并不直接操作64位的值。而是先将64位的值转换32位的整数，然后执行操作，最后再将结果转换回64位。

对于有符号的整数，32位中的前31位用于表示整数的值。在第32位用于表示数值的符号：0表示正数，1表示负数。这个表示符号的位叫做符号位，符号位的值决定了其他数值的格式。其中，正数以纯二进制格式存储，31位中的每一位都表示2的幂。第一位（叫做位0）2<sup>0</sup>，第二位表示2<sup>1</sup>，从此类推。没有用到的位以0填充，即忽略不计。例如，数值18的二进制表示是00000000000000000000000000010010，或者更简洁的10010。这是5个有效位，这5位本身就决定了实际的值（3-1图）。

![An image](./images/3-1.gif)

负数同样以二进制码存储，但使用的格式是二进制补码。计算一个数值的二进制补码，需要经过一下三个步骤：
1. 求这个数值绝对值的二进制码（例如，要求-18的二进制补码，先求18的二进制码）；
2. 求二进制反码，即将0替换为1，将1替换0；
3. 得到的二进制反码加1。
要根据这3个步骤求得-18的二进制码，首先就要求得18的二进制码，即：
```js
  0000 0000 0000 0000 0000 0000 0001 0010
```
然后，求其二进制反码，即0和1互换：
```js
  1111 1111 1111 1111 1111 1111 1110 1101
```
最后，二进制反码加1：
```js
  1111 1111 1111 1111 1111 1111 1110 1101
                                        1
  ---------------------------------------
  1111 1111 1111 1111 1111 1111 1110 1110
```
这样，就求得了-18的二进制表示，即1111 1111 1111 1111 1111 1111 1110 1110。要注意的是，在处理有符号整数是，是不能访问为31的。

在以二进制字符串形式初始一个负数是，我们看到的只是这个负数绝对值的二进制码前面加上了一个负号。看下面的例子：
```js
  var num = -18;
  console.log(num.toString(2)); // -10010
```
要把数值-18转成二进制字符串，得到的结果是"-10010"。这说明转换过程理解了二进制补码并将其以更合乎逻辑的形式展示了出来。
#### 按位非（NOT）
按位非操作符由一个波浪线（~）表示，执行按位非的结果就是返回数值的反码。
```js
  var num1 = 25;
  var num2 = ~num1;
  console.log(num2); // -26
```
这里，对25执行按位非操作，结果得到了-26。这也验证了按位非操作的本质：操作数的负值减1。

#### 按位与（AND）
按位与操作符由一个和号字符（&）表示，它有两个操作符数。从本质上讲，按位操作就是将两个数值的每一位对齐，然后根据下表中的规则，对相同位置上的两个数执行AND操作：
|第一个数值的位|第二个数值的位|结果|
|----|----|----|
|1|1|1|
|1|0|0|
|0|1|0|
|0|0|0|
简而言之，按位与操作只在两个数值的对应为都是1时才返回1，任何一位是0，结果都是0。

下面看一个对25和3执行按位于操作的例子：
```js
var result = 25 & 3;
console.log(result); // 1
```
可见，对25和3执行按位与操作的结果是1。为什么呢？请看其底层操作：
```js
  25 = 0000 0000 0000 0000 0000 0000 0001 1001
   3 = 0000 0000 0000 0000 0000 0000 0000 0011
```
可以看出，25和3的二进制码对应微商只有一个位同时是1，而其他位的结果自然都是0，因此最终结果等于1。
#### 按位或（OR）
按位或操作符由一个竖线符号（|）表示，同样也有两个操作数。按位或操作遵循下面这个真值表。
|第一个数值的位|第二个数值的位|结果|
|----|----|----|
|1|1|1|
|1|0|1|
|0|1|1|
|0|0|0|
由此可见，按位或操作在有一个位是1的情况下就返回1，而只有在两个为都是0的情况下才返回0。

如果前面按位与的例子中对25和3执行按位或操作，则代码如下所示：
```js
  var result = 25 | 3;
  console.log(result); // 27
```
25与3按位或的结果是27：
```js
25 = 0000 0000 0000 0000 0000 0000 0001 1001
 3 = 0000 0000 0000 0000 0000 0000 0000 0011
--------------------------------------------
OR = 0000 0000 0000 0000 0000 0000 0001 1011
```
这两个数值的都包含4个1，因此可以把每个1直接放到结果中。二进制码11011等于十进制27。
#### 按位异或（XOR）
按位异或操作符有一个插入符号（^）表示，也有两个操作数。以下是按位异或的真值表。
|第一个数值的位|第二个数值的位|结果|
|----|----|----|
|1|1|0|
|1|0|1|
|0|1|1|
|0|0|0|
按位异或与按位或的不同之处在于，这个操作在两个数值对应位上只有一个1时才返回1，如果对应的两位都是1或都是0，则返回0。
对25和3执行按位异或操作的代码如下所示：
```js
  var result = 25 ^ 3;
  console.log(result); // 26
```
25与3按位异或的结果是26，其底层操作如下所示：
```js
  25 = 0000 0000 0000 0000 0000 0000 0001 1001
   3 = 0000 0000 0000 0000 0000 0000 0000 0011
  ---------------------------------------------
 XOR = 0000 0000 0000 0000 0000 0000 0001 1010
```
这两个数值都包含4个1，但第一位上则都是1，因此结果的第一位变成了0。而其他位上的1在另一个数值中都没有对应的1，可以直接放到结果中。二进制码11010等于十进制值26。
#### 左移
左移操作符由两个小于号（<<）表示，这个操作符会将数值的所有位向左移动指定的位数。例如，如果将数值2（二进制码为10）向左移动5位，结果就是64，（二进制码为1000000），代码如下所示：
```js
  var oldValue = 2;
  var newValue = oldValue << 5;
  console.log(newValue); // 64
```
注意，在向左移动后，原数值的右侧多出了5个空位。左移操作会以0来填充这些空位，以便得到的结果是一个完整的32位进制数（图3-2）。

![An image](./images/3-2.gif)

注意，左移不会影响操作数的符号位。换句话说，如果将-2向做移动5位，结果将是-64，而非64。

#### 有符号的右移
有符号的右移操作符由两个大于号（>>）表示，这个操作符会将数值右移动，但保留符号位（即
正负号标记）。有符号的右移操作与左移操作恰好相反，即如果将64向右移动5位，结果将变回2：
```js
  var oldValue = 64;
  var newValue = oldValue >> 5;
  console.log(newValue); // 2
```
同样，在移位过程中，原数值中也会出现空位。只不过这次的空位出现在原数值的左侧、符号位的右侧。同3-2图的原理，只不过是右移了。而此时ECMAScript会用符号位的值来填充所有空位，以便得到一个完整的值。

#### 无符号右移
无符号右移操作符由3个大于号（>>>）表示，这个操作符会将数值的所有32位都向右移动。对正数来说，无符号右移的结果与有符号右移相同。如果将64无符号右移5位，结果仍然还是2：
```js
  var oldValue = 64;
  var newValue = oldValue >>> 5;
  console.log(newValue); // 2
```
但是对负数来说，情况就不一样了。首先，无符号右移是以0来填充空位，而不是像有符号右移那样以符号位的值来填充空位。所以，对正数的无符号右移与有符号右移结果相同，但对负数的结果就不一样了。其次，无符号右移操作符会把负数的二进制码当成正数的二进制码。而且，由于负数以其绝对值的二进制补码形式表示，因此就会导致无符号右移后的结果非常之大，如下面的例子所示：
```js
  var oldValue = -64;
  var newValue = oldValue >>> 5;
  console.log(newValue); // 134217726
```
这里，当对-64执行无符号右移5位的操作后，得到的结果是134217726。之所以结果如此之大，是因为-64的二进制码为11111111111111111111111111000000，而且无符号右移操作会把这个二进制码当成正数的二进制码，换算成十进制就是4294967232。如果把这个值右移5位，结果就变成了00000111111111111111111111111110，即十进制的134217726。

### 布尔操作符
布尔操作符一共有3个：非（NOT）、与（AND）和或（OR）。

#### 逻辑非
逻辑非操作符由一个感叹号（!）表示，可以应用于ECMAScript中的任何值。无论这个值是什么数据类型，这个操作符都返回一个布尔值。逻辑非操作符首先会将它的操作数转换为一个布尔值，然后再对其求反，也就是说，逻辑非操作符遵循下列规则：
* 如果操作数是一个对象，返回false；
* 如果操作数是一个空字符串，返回true；
* 如果操作数是一个非空字符串，返回false；
* 如果操作数是数值0，返回true；
* 如果操作数是任意非0数值（包括Infinity），返回false；
* 如果操作数是null，返回true；
* 如果操作数是NaN，返回true；
* 如果操作数undefined，返回true；
下面几个例子展示了应用上述规则的结果：
```js
  console.log(!false); // true
  console.log(!"blue"); // false
  console.log(!0); // true
  console.log(!NaN); // true
  console.log(!""); // true
  console.log(!12345); // false
```
逻辑非操作符也可以用于将一个值转换为与其对应的布尔值。而同时使用两个逻辑非操作符，实际上就会模拟Boolean()转型函数的行为。其中，第一个逻辑非操作会基于无论什么操作数返回一个布尔值，而第二个逻辑非操作则对该布尔值求反，于是就得到了这个值真正对应的布尔值。当然，最终结果与对这个值使用Boolean()函数相同，如下面的例子所示：
```js
  console.log(!!"blue"); // true
  console.log(!!0); // false
  console.log(!!NaN); // false
  console.log(!!""); // false
  console.log(!!12345); // true
```
#### 逻辑与
逻辑与操作符由两个和号（&&）表示，有两个操作数，如下面的例子所示：
|第一个数值的位|第二个数值的位|结果|
|----|----|----|
|true|true|true|
|true|false|flase|
|false|true|false|
|false|false|false|
逻辑与操作可以应用于任何类型的操作数，而不仅仅是布尔值。在有一个操作不是布尔值的情况
下，逻辑与操作就不一定返回布尔值；此时，它遵循下列规则：
* 如果第一个操作数是对象，则返回第二个操作数；
* 如果第二个操作数是对象，则只有在第一个操作数的求值结果为true的情况下才返回该对象；
* 如果两个操作数都是对象，则返回第二个操作数；
* 如果第一个操作数是null，则返回null；
* 如果第一个操作数是NaN，则返回NaN；
* 如果第一个操作数是undefined，则返回undefined。

逻辑与操作属于短路操作，即如果一个操作数能够决定结果，那么就不会再对第二个操作数求值。对于逻辑与操作而言，如果第一个操作数是false，则无论第二个操作数是什么值，结果都不再可能是true了。来看下面的例子：
```js
  var found = true;
  var result = (found && someUndefinedVariable); // 这里发生错误
  console.log(result); // 这一行不会执行
```
在上面的代码中，当执行逻辑与操作时会发生错误，因为变量someUndefinedVariable没有声明。由于变量found的值是true，所以逻辑与操作符会继续对变量someUndefinedVariable求值。但someUndefinedVariable未定义，因此导致错误。这说明不能在逻辑与操作中使用未定义的值。如果像下面这个例子一样，将found的值设置为false，就不会发生错误：
```js
  var found = false;
  var result = (found && someUndefinedVariable);
  console.log(result); // false
```
在这个例子中，无论变量someUndefinedVariable有没有定义，也永远不会对它求值，因为第一个操作数的值是false。而这也意味着逻辑与操作的结果必定是flase，根本用不着再对&&右侧的操作数求值了。在使用逻辑与操作符时要始终铭记它是一个短路操作符。
#### 逻辑或
逻辑或操作符由两个竖线符号（||）表示，有两个操作数，如下面的例子所示：
```js
  var result = true || false;
```
逻辑或的真值表如下：
|第一个数值的位|第二个数值的位|结果|
|----|----|----|
|true|true|true|
|true|false|true|
|false|true|true|
|false|false|false|
与逻辑与操作相似，如果有一个操作数不是布尔值，逻辑或也不一定返回布尔值；此时，它遵循
下列规则：
* 如果第一个操作数是对象，则返回第一个操作数；
* 如果第一个操作数的求值结果为false，则返回第二个操作数；
* 如果两个操作数都是对象，则返回第一个操作数；
* 如果两个操作数都是null，则返回null；
* 如果两个操作数都是NaN，则返回NaN；
* 如果两个操作数都是undefined，则返回undefined。

与逻辑与操作符相似，逻辑或操作符也是短路操作符。也就是说，如果第一个操作数的求值结果为true，就不会对第二个操作数求值了。下面看一个列子：
```js
  var found = true;
  var result = (found || someUndefinedVariable);
  console.log(result); // true
```
这个例子跟前面的例子一样。变量someUndefinedVariable没有定义的。但是，由于变量found的值是true。而someUndefinedVariable永远不会被求值，因此结果就会输出"true"。如果像下面这个例子一样，把found的值改为false，就会导致错误：
```js
  var found = false;
  var result = (found || someUndefinedVariable); // 这里发生错误
  console.log(result); // 这一行不会执行
```
我们可以利用逻辑或的这一行为来避免为变量赋null或undefined值。例如：
```js
  var myObject = preferredObject || backupObject;
```
在这个例子中，变量myObject将被赋予等号后面两个值中的一个。变量preferredObject中包含优先赋给变量myObject的值，变量backupObject负责在preferredObject中不包含有效值的情况下提供后备值。如果preferredObject的值不是null，那么它的值将被赋给myObject；如果是null，则将backupObject的值赋给myObject。
### 乘性操作法
ECMAScript定义了3个乘性操作符：乘法、除法和求模。在操作数为非数值的情况下会执行自动的类型转换。如果参与乘性计算的某个操作数不是数值，后台会先使用Number()转型函数将其转换为数值。空字符串将被当作0，布尔值true将当作1。

#### 乘法
乘法操作符由一个星号（*）表示，用于计算两个数值1乘积。如下面的例子：
```js
  var result = 34 * 56;
  console.log(result); // 1904
```
在处理特殊值的情况下，乘法操作符遵循下列特殊的规则：
* 如果操作数都是数值，执行常规的乘法计算，即两个正数或两个负数相乘的结果还是正数，而如果只有一个操作数有符号，那么结果就是负数。如果乘积超过ECMAScript数值的表示范围，则返回Infinity或-Infinity；
* 如果有一个操作数是NaN，则结果是NaN；
* 如果是Infinity与0相乘，则结果是NaN；
* 如果是Infinity与非0数值相乘，则结果是Infinity或-Infinity，取决于有符号操作数的符号；
* 如果是Infinity与Infinity相乘，则结果是Infinity；
* 如果有一个操作数不是数值，则在后台调用Number()将其转换为数值，然后再应用上面的规则。

#### 除法
除法操作符由一个斜线符号（/）表示，执行第二个操作数除第一个操作数的计算，如下面的例子：
```js
  var result = 66 / 11;
  console.log(result); // 6
```
与乘法运算符相似，在处理特殊值时，除法运算符也有一些特殊行为：
* 如果操作数都是数值，执行常规的除法计算，即两个正数或两个负数相除的结果还是正数，而如果只有一个操作数有符号，那么结果就是负数。如果商超过ECMAScript数值的表示范围，则返回Infinity或-Infinity；
* 如果有一个操作数是NaN，则结果是NaN；
* 如果是Infinity与0除，则结果是NaN；
* 如果是零被零除，则结果是NaN；
* 如果是非零的有限数被零除，则结果是Infinity或-Infinity，取决于有符号操作数的符号；
* 如果是Infinity被任何非零数值除，则结果是Infinity或-Infinity，取决于有符号操作数的符号；
* 如果有一个操作数不是数值，则在后台调用Number()将其转换为数值，然后再应用上面的规则。

#### 求模
求模（余数）操作符由一个百分号（%）表示，用法如下：
```js
  var result = 26 % 5;
  console.log(result); // 1
```
与其他乘性运算符相似，对于特殊值，取模运算符也有特殊的行为：
* 如果操作数都是数值，执行常规的除法计算，返回除得的余数；
* 如果被除数是无穷大值而除数是有限大的数值，则结果是NaN；
* 如果被除数是有限打的数值而除数是零，则结果是NaN；
* 如果是Infinity被Infinity除，则结果是NaN；
* 如果被除数是有限打的数值而除数是无穷大的数值，则结果是被除数；
* 如果被除数是零，则结果是零；
* 如果有一个操作数不是数值，则在后台调用Number()将其转换为数值，然后再应用上面的规则。

### 加性操作符
加性操作符，在ECMAScipt中，这两个操作符却都有一系列的特殊行为。

#### 加法
加法操作符（+）的用法如下所示：
```js
  var result = 1 + 2;
  console.log(result); // 3
```
如果两个操作符都是数值，执行常规的加法计算，然后根据下列规则返回结果：
* 如果有一个操作数是NaN，则结果是NaN；
* 如果是Infinity加Infinity，则结果是Infinity；
* 如果是-Infinity加-Infinity，则结果是-Infinity；
* 如果是Infinity加-Infinity，则结果是NaN；
* 如果是+0加+0，则结果是+0；
* 如果是-0加-0，则结果是-0；
* 如果是+0加-0，则结果是+0；
不过，如果有一个操作数是字符串，那么就要应用如下规则：
* 如果两个操作数都是字符串，则将第二个操作数与第一个操作数拼接起来；
* 如果只有一个操作数是字符串，则将另一个操作数转换为字符串，然后再将两个字符串拼接起来。

如果有一个操作数是对象、数值或布尔值，则调用它们的toString()方法取得相应的字符串值，然后再应用前面关于字符串的规则。对于undefined和null，则分别调用String()函数并取得字符串"undefined"和"null"

下面来举几个例子：
```js
  var result = 5 + 5; // 两个数值相加
  console.log(result); // 10 

  var result2 = 5 + "5"; // 一个数值和一个字符串相加
  console.log(result2); // 55
```
以上代码演示了加法操作符在两种模式下的差别。第一行代码演示了正常的情况，即5+5等于10（数值）。但是，如果将一个操作符改为字符串"5"，结果就变成了"55"（字符串值），因为第一个操作数也被转换成了"5"。

忽视加法操作中的数据类型是ECMAScript编程中最常见的一个错误。再来看一个例子：
```js
  var num1 = 5;
  var num2 = 10;
  var message = "The sum of 5 and 10 is " + num1 + num2;
  console.log(message); // The sum of 5 and 10 is 510
```
在这个例子中，变量message的值是执行两个加法操作之后的结果。有人可能以为最后得到字符串是"The sum of 5 and 10 is 15"，实际上的结果是"The sum of 5 and 10 is 510"。之所以会这样，是因为每个加法操作是独立执行的。第一个加法操作将一个字符串和一个数值（5）拼接了起来，结果是一个字符串。而第二个加法又用这个字符串去加另一个数值（10），当然也会得到一个字符串。如果想先对数值执行算术计算，然后再将结果与字符串拼接起来，应该像下面这样使用圆括号：
```js
  var num1 = 5;
  var num2 = 10;
  var message = "The sum of 5 and 10 is " + (num1 + num2);
  console.log(message); // The sum of 5 and 10 is 15
```
#### 减法
减法操作符（-）是另一个极为常用的操作符，其用法如下所示：

  var result = 2 - 1;
  console.log(result); // 1

与加法操作符类似，在处理特殊值时，减法运算符也有一些特殊行为：
* 如果两个操作符都是数值，则执行常规的算术减法操作并返回结果；
* 如果有一个操作数是NaN，则结果是NaN；
* 如果是Infinity减Infinity，则结果是NaN；
* 如果是-Infinity减-Infinity，则结果是NaN；
* 如果是Infinity减-Infinity，则结果是Infinity；
* 如果是-Infinity减Infinity，则结果是-Infinity；
* 如果是+0减+0，则结果是+0；
* 如果是-0减+0，则结果是-0；
* 如果是-0减-0，则结果是+0；
* 如果一个操作数是字符串、布尔值、null或undefined。则先在后台调用Number()函数将其转换为数值，然后再根据前面的规则执行减法计算。如果转换的结果是NaN，则减法的结果就是NaN；
* 如果一个操作数是对象，则调用对象的valueOf()方法以取得表示该对象的数值。如果得到的值是NaN，则减法的结果就是NaN。如果对象没有valueOf()方法，则调用其toString()方法并得到的字符串转换为数值。

下面几个例子展示了上面的规则：
```js
  var result1 = 5 - true;
  var result2 = NaN - 1;
  var result3 = 5 - 3;
  var result4 = 5 - "";
  var result5 = 5 - "2";
  var result6 = 5 - null;

  console.log(result1); // 4 因为true被转换成了1
  console.log(result2); // NaN
  console.log(result3); // 2
  console.log(result4); // 5 因为""被转换成了0
  console.log(result5); // 3 因为"2"被转换成了2
  console.log(result6); // 5 因为null被转换成了0
```
### 关系操作符
小于（<）、大于（>）、小于等于（<=）和大于等于这几个关系操作符用于对两个值进行比较，
这几个操作符都返回一个布尔值，如下面的例子所示：
```js
  var result1 = 5 > 3;
  var result2 = 5 < 3;
  console.log(result1); // true
  console.log(result2); // false
```
当关系操作符的操作数使用了非数值是，也要进行数据转换或完成某些奇怪的操作。以下就是
相应的规则。
* 如果两个操作数都是数值，则执行数值比较。
* 如果两个操作数都是字符串，则比较两个字符串对应的字符编码值。
* 如果一个操作数是对象，则调用这个对象的valueOf()方法，用得到的结果按照前面的执行行比较。如果对象没有valueOf()方法，则调用toString()方法，并用得到的结果根据前面的规则执行比较。
* 如果一个操作数是布尔值，则先将其转换为数值，然后再执行比较。

在使用关系操作符比较两个字符串时，会执行一种奇怪的操作。很多人都会认为，在比较字符串值时，小于的意思是“在字母表中的位置靠前”，而大于则意味着“在字母表中的位置靠后”，但实际上完全不是那么回事。在比较字符串时，实际比较的是两个字符串中对应位置的每个字符的字符编码值。经过这么一番比较之后，再返回一个布尔值。由于大小写字符的字符编码全部小于小写字母的字符编码，因此我们就会看到如下所示的奇怪现象：
```js
  var result = "Brick" < "alphabet";
  console.log(result); // true
```
在这个例子中，字符串"Brick"被认为小于字符串"alphabet"。原因是字母B的字符编码为66，而字母a的字符编码是97.如果要真正按字母顺序比较字符串，就必须把两个操作数转换为相同大小写形式（全部大写或全部小写），然后再执行比较，如下所示：
```js
  var result = "Brick".toLowerCase() < "alphabet".toLowerCase();
  console.log(result); // false
```
通过将两个操作数都转换为小写形式，就可以得出"alphabet"按字母表顺序排在"Brick"之前
的正确判断了。

另一种奇怪的现象发生在比较两个数字字符串的情况下，比如下面这个例子：
```js
  var result = "23" < "3";
  console.log(result); // true
```
确实，当比较字符串"23"是否小于"3"时，结果居然是true。这是因为两个操作数都是字符串，而字符串比较的是字符编码（"2"的字符编码是50，而"3"的字符编码是51）。不过，如果像下面例子中一样，将一个操作数改为数值，比较的结果就正常了：
```js
  var result = "23" < 3;
  console.log(result); // false
```
此时，字符串"23"会被转换成数值23，然后再与3进行比较，因此就会得到合理的结果。在比较数值和字符串时，字符串都会被转换成数值，然后再以数值方式与另一个数值比较。当然，这个规则对前面的例子是适用的。可是，如果那个字符串不能被转换成一个合理的数值呢？比如：
```js
  var result = "a" < 3;
  console.log(result); // false，因为"a"被转换成了NaN
```
由于字母"a"不能转换成合理的数值，因此就被转换成了NaN。根据规则，任何操作数与NaN进行关系比较，结果都是false。于是，就出现了下面这个有意思的现象：
```js
  var result1 = NaN < 3;
  var result2 = NaN >= 3;

  console.log(result1); // false
  console.log(result2); // false
```
按照常理，如果一个值不小于另一个值，则一定是大于或等于那个值。然而，在于NaN进行比较时，这两个比较操作的结果都返回了false。

### 相等操作符
#### 相等和不相等
ECMAScript中的相等操作符由两个等于号（==）表示，如果两个操作数相等，则返回true。而不相等操作符由叹号跟等于号（!=）表示，如果两个操作数不相等，则返回true。这两个操作符都会先转换操作数（通常称为强制转型），然后再比较他们的相等性。

在转换不同的数据类型时，相等和不相等操作遵循下列基本规则：
* 如果有一个操作数是布尔值，则在比较相等性之前先将其转换为数值--false转换为0，而true转换为1；
* 如果一个操作数是字符串，另一个操作数是数值，在比较相等性之前先将字符串转换为数值；
* 如果一个操作数是对象，另一个操作数不是，则调用对象的valueOf()方法，用得到的基本类型值按照前面的规则进行比较；

这两个操作符在进行比较时则要遵循下列规则。

* null和undefined是相等。
* 要比较相等性之前，不能将null和undefined转换成其他任何值。
* 如果有一个操作数是NaN，则相等操作符返回false，而不相等操作符返回true。
* 如果两个操作数都是对象，则比较它们是不是同一个对象。如果两个操作数都指向同一个对象，则相等操作符返回true；否则，返回false。
:::warning 重要提示
即使两个操作数都是NaN，相等操作符也返回false；因为按照规则，NaN不等于NaN。
:::

下表列出了一些特殊情况及比较结果：

|表达式|值|
|----|----|
|null == undefined|true|
|"NaN" == NaN|false|
|5 == NaN|false|
|NaN == NaN|false|
|NaN != NaN|true|
|falst != 0|true|
|true == 1|true|
|true == 2|false|
|undefined == 0|false|
|null == 0|false|
|"5" == 5|true|

#### 全等和不全等
除了在比较值钱不转换操作数之外，全等和不全等操作符与相等和不相等操作符没有什么区别。全
等操作符由3个等于号（===）表示，它只在两个操作数未经转换就相等情况下返回true，如下面的
例子所示：
```js
  var result1 = ("55" == 55);
  var result2 = ("55" === 55);

  console.log(result1); // true 因为转换后相等
  console.log(result2); //false 因为不同的数据类型不相等
```
在这个例子中，第一个比较使用的是相等操作比较字符串"55"和数值55，结果返回了true。如前所述，这是因为字符串"55"先被转换成了数值55，然后再与另一个数值55进行比较。第二个比较使用了全等操作符以不转换数值的方式比较同样的字符串和值。在不转换的情况下，字符串当然不等于数值，因此结果就是false。

不全等与操作符由一个叹号后跟两个等于号（!==）表示，它在两个操作数未经转换就不相等的情况下返回true。例如：
```js
  var result1 = ("55" != 55);
  var result2 = ("55" !== 55);

  console.log(result1); // false 因为转换后相等
  console.log(result2); // true 因为不同的数据类型不相等
```
这个例子中，第一个比较使用了不相等操作符，而该操作符会将字符串"55"转换成55，结果就与第二个操作数（也是55）相等了。而由于这两个操作数被认为相等，因此就返回false了。第二个比较使用了不全等操作符。假如我们这样想：字符串55与数值55不相同吗？，答案一定是：是的（true）。

记住：null == undefined 会返回true，因为它们是类似的值；但null === undefined会返回false，因为它们是不同类型的值。

### 条件操作符
条件操作符应该是ECMAScript中最灵活的一种操作符了，如下面的例子所示：
```js
  var num1 = 5;
  var num2 = 4;
  var max = (num1 > num2) ? num1 : num2;
  console.log(max); // 5
```
在这个例子中，max中将会保存一个最大的值。这个表示式的意思是：如果num1大于num2（关系表达式返回true），则将num1的值赋给max；如果num1小于或等于num2（关系表达式返回flase），则将num2的值赋给max。

### 赋值操作符
简单的赋值操作符由等于号（=）表示，其作用就是把右侧的值赋给左侧的变量，看下面的例子：
```js
  var num = 10;
```
如果在等于号（=）前面再添加乘性操作符、加性操作符或位操作符，就可以完成复合赋值操作。
```js
  var num = 10;
  num += 10;
  console.log(num); // 20
```
每个主要算术操作符（以及个别的其他操作符）都有对应的复合赋值操作符。这些操作符如下所示：
* 乘法/赋值（*=）
* 除法/赋值（/=）
* 取模/赋值（%=）
* 加法/赋值（+=）
* 减法/赋值（-=）
* 左移/赋值（<<=）
* 有符号右移/赋值（>>=）
* 无符号右移/赋值（>>>=）

设计这些操作符的主要目的就是简化赋值操作。使用它们不会带来任何性能的提升。

### 逗号操作符
使用逗号错座敷可以在一条语句中执行多个操作，如下面的例子所示：
```js
  var num=1, num2=2, num3=3;
```
逗号操作符多用于声明多个变量，但除此之外，逗号操作符还可以用于赋值。在用于赋值时，逗号
操作符总会返回表达式中的最后一项，如下面的例子所示：
```js
  var num = (5, 1, 4, 8, 0);
  console.log(num); // 0
```
## 语句
### if语句
大多数编程语言中最为常用的一个语句就是if语句。以下是if语句的语法：
```js
  if (condition) statement1 else statement2
```
其中的condition（条件）可以是任意表达式；而且对这个表达式求值的结果不一定是布尔值。ECMAScript会自动调用Boolean()转换函数将这个表达式的结果转换为一个布尔值。如果对condition求值的结果是true，则执行statement1（语句1），如果对condition求值结果是false，则执行statement2（语句2）。而且这两个语句既可以是一行代码，也可以是一个代码块（以一对花括号括起来的多行代码）。看下面的例子：
```js
  var i = 26;
  if (i > 25) {
    console.log("大于 25");
  } else {
    console.log("小于或等于25");
  }
```
另外，也可以像下面这样把整个if语句写在一行代码中：
```js
  if (condition1) statement1 else if (condition2) statement2 else statement3
```
但推荐的做法则是像下面这样：
```js
  var i = 26;
  if (i > 25) {
    console.log("大于 25");
  } else if (i < 0) {
    console.log("小于0");
  } else {
    console.log("小于或等于25");
  }
```
### do-while语句
在对条件表达式求值之前，循环体内的代码至少会被执行一次
```js
var text = ""
var i = 0;
do {
  i += 2;
  text += "数字为" + i;
} while (i < 10) {
  // 到10的时候就停止了上面的循环
  console.log(text);
  // 数字为2数字为4数字为6数字为8数字为10
};
```
像 do-while 这种后测试的循环语句最常用于循环体中的代码至少要被执行一次的情形下使用

### while语句
是属于前测试循环语句，也就是说满足条件才会被执行的语句
```js
var i = 0;
while (i < 10) {
  i++;
  console.log(i);
}
```
### for语句
for语句也是一种测试循环语句，但它具有在执行循环之前初始化变量和定义循环后要执行的代码的能力。以下是 for 语句的语法：
```js
  for (initialization; expression; post-loop-expression) statement
```
请看下面的例子：
```js
  var count = 10;
  for (var i = 0; i < count; i++) {
    console.log(i);
  }
```
这段代码定义了初始值为 0 的变量 i。只有当条件表达式（i < count）的值为 true 时，才进入 for 循环，这样循环主体可能不被执行。如果执行了循环主体，那么将执行循环后表达式，并迭代变量 i。

### for-in语句
精准的迭代语句，可以用来枚举对象的属性。
```js
  for (varpropName in window) {
    // 这里就是把BOM中的属性一一列举出来
    console.log(propName)
  }
```
### label语句
使用label语句可以在代码中添加标签，但是加标签的语句一般都是和for语句等循环语句配合使用
```js
  var count=10;
  start: for (var i = 0; i < count; i++ ){
    console.log(i);
  }
```

### break语句
break 语句会立即退出循环，强制继续执行循环后面的语句。在下面这个例子中的for循环会将变量i由1递增到10。在循环体内，有一个if语句检查i的值是否可以被5整除这里用的是求模操作符如果可以那么就跳出语句循环。另一方面变量num从0开始，记录循环执行的次数。在执行break语句之后要执行的下一行代码是console.log()函数结果就为4
```js
  var num = 0;
  for (var i = 1; i < 10; i++) {
    if (i % 5 == 0) {
      break;
    }
    num++;
  }
  console.log(num);// 4
  ```
### continue语句
continue语句虽然也是会立即退出循环，但是退出循环后会从循环的顶部继续执行。在下面的例子结果为8也就是num++总共执行了8次。当变量i等于5时，循环会在num再次递增之前退出，但是接下来执行的是下一次循环，即使i的值等于6的循环，于是循环又继续执行直到i等于10的时候自然结束，num最终值之所以是8可以理解成到i=5的时候跳出了循环，i=6还是会执行的，直到10就没了。
```js
  var num = 0;
  for (var i = 1; i < 10; i++) {
    if (i % 5 == 0) {
      continue;
    }
    num++;
  }
  console.log(num);// 8
  ```

### break于label语句联合使用
这个例子中如果每个循环正常执行10次num++语句就会正常执行100次。换句话来说如果两个循环都自然结束num的值应该是100。但是内部循环中的break语句带了一个参数(start标签)；要返回到标签添加这个标签的结果将导致break语句不仅会退出内部的for循环(j的循环),而且也会退出外部的for语句(i的循环)，当变量i和j都等于5时，num的值刚好是55。
```js
var num = 0;
start:
for (var i = 0; i < 10; i++) {
  for (var j = 0; j < 10; j++) {
    if (i == 5 && j == 5) {
      break start;
    }
    num++;
  }
}
console.log(num); // 55;
```
### continue于label语句联合使用
在这种情况下,continent会强制继续执行循环---退出内部循环，执行外部循环当j是5的时候continent语句执行，而这也就意味着内部循环少执行了5次，因为num的结果是95。
```js
var num = 0;
start:
for (var i = 0; i < 10; i++) {
  for (var j=0; j < 10; j++) {
    if (i == 5 && j == 5) {
    continue start;
    }
    num++;
  }
}
console.log(num); // 95
```
### with语句
with语句的作用是将代码的作用域设置到一个特定的对象中。定义一个with语句是为了简化多次编写同一个对象的工作使用with语句关联了location对象，这意味着在with语句这意味着在with语句里面每个变量首先被认为是一个局部变量如果在局部环境中找不到这个变量的定义，就会查询location对象中是否有同名的属性，如果发现了同名属性，则以location对象属性的值作为变量值。
```js
with (location) {
  var qs = search.substring(1);
  var hostName = hostname;
  var url = href;
}
```
:::warning 警告
严格模式下不允许使用with语句的
:::

### switch语句
switch语句与if语句的关系最为密切，和if语句很相似，如下所示：
```js
var i = 25;
switch (i) {
  case 25:
    alert("25");
    break;
  case 35:
    alert("35");
    break;
  case 45:
    alert("45");
    break;
  default:
    alert("Other");
}
```
switch语句中的每一种情形（case）的含义是：如果表达式等于这个值（value），则执行后面的语句。而break关键字会导致代码执行流跳出switch语句。如果省略break关键字，就会导致执行当前case后，继续执行下一个case。最后的default关键字则用于在表达式不匹配前面任何一种情形的时候，执行机动代码（也相当于一个else语句）。

## 函数
函数使用**function**关键字来声明，后跟一组参数以及函数体。函数的基本语法如下所示：
```js      
function sayHi(name, message) {
  console.log("Hello  " + name + "," + message);
}
sayHi("xiaoli", "how are you today？");
```
函数在定义时不必指定是否返回值任何函数在任何时候都可以通过return语句后跟要返回的值来实现返回值，但是切记的是return语句之后停止并立即退出，因此位于return语句之后的任何代码都不会执行。
```js
function sayHi(name,msg){
  return name + msg; // 这里return回去了两个值，并且退出了这个函数之后的操作都不会被执行
  console.log(name,msg); // 上面已经return回去了，这里是不会被执行的。
}
console.log(sayHi("hello","word")); //helloword
```
另外，return语句也可以不带有任何返回值。在这种情况下，函数在停止执行后将返回一个undefined这种用法一般用在需要提前停止函数执行而又不需要返回值的情况下要么就返回一个值，要么就永远都不要返回值
```js
function sayHi(){
  return; // 这里return任何返回值 所以得到的是一个undefined
}
console.log(sayHi()); // undefined
```
## 理解参数
参数在内部是用一个数组来表示的，函数接收到的始终都是这个数组而不关心数组包含哪些参数，如果这个数组中不包含任何元素，无所谓如果包含多个元素，也没问题。这时候就要靠我们的arguments对象来访问这个参数数组，从而获取传递给函数的每一个参数,arguments对象只是和数组类似而已，但是它并不是array的实例。

像下面这个例子，即使函数中不包含命名的参数。虽然已经没有name,msg标识符但是也可以通过arguments[0]这样的形式去访问参数
```js
function f(){
  console.log("hello"+arguments[0] + "," + arguments[1]); // hello小黎你好
};
f("小黎","你好");
```
通过访问arguments对象的length属性可以获知有多少个参数传递给了函数，下面这个函数会在每次被调用时，输出传入其中的参数个数，分别显示了2,0,1
```js
function fun(){
  console.log(arguments.length);
}
fun("string",45); // 2
fun(); // 0
fun(12); // 1
```
像这里doAdd()函数会在只有一个参数的情况下给该参数加上10；如果是两个参数的话则将那个参数简单的相加并返回结果。因此下面的doAdd(10)得到了10，doAdd(30,20)得到了50。
```js
function doAdd() {
if (arguments.length == 1) {
  console.log(arguments[0] + 10); //10
} else if (arguments.length == 2) {
    console.log(arguments[0] + arguments[1]); //50
  }
}
doAdd(10);
doAdd(30,20);
```
重写后的这个doAdd()函数中，两个命名参数与arguments对象一起使用由于num1的值和arguments[0]的值是相同的，因此它们可以互换使用。
```js
function doAdd (num1,num2) {
  if (arguments.length == 1) {
    console.log(num1 + 10);  //20
  } else if(arguments.length == 2) {
    console.log(arguments[0] + 20); //30
  }
}
doAdd(10);
doAdd(10,20);
```
关于arguments的行为，还有一点比较有意思的，那就是它的值永远与对应命名参数的值保持同步。
```js
function doAdd(num1, num2) {
  arguments[1] = 10;  //这里是把下面doAdd(10,20)中的20参数改变成了10;
  console.log(arguments[0] + num2); // 20
}
doAdd(10,20);
```
每次执行这个 doAdd() 函数都会重写第二个参数，将第二个参数的值修改为10，因为 arguments 对象中的值会自动会反映到对应的命名参数所以修改了num2结果它们的值都会变成10,不过这并不是说读取这两个值会访问相同的内存空间；它们的内存空间是独立的，但是它们的值会同步。另外还要记住如果只传入一个参数，那么arguments[1]设置的值不会反映到命名参数中。这是因为arguments对象的长度是由传入的参数个数决定的，不是由定义函数时的命名参数的个数决定的。

还要记住最后一点:没有传递值的命名参数将自动被赋予undefined值。这就跟定义了变量但又没有初始化一样。例如如果给doAdd()函数传递了一个参数，那么num2中就会保存undefined。

## 没有重载
ECMAScript里面是没有重载的，只要这两个定义的签名(接受的参数类型和数量)不同即可。在ECMAScript里面函数是没有签名的，因为其参数是有包含零或多个值的数组来表示的。而没有函数签名，真正的重载时不可以做到的。

如果在ECMAScript中定义了两个名字相同的函数，则该名字只属于后定义的函数。看下面的例子：
```js
function f(num) {
  return num + 100;
};
function f(num) {
  return num + 200;
}
var result = f(100);
console.log(result); //300
```
上面的f()函数被定义了两次。第一个f()给参数加100，而第二个f()给参数加了200。由于后定义的函数覆盖了先定义的函数，因此当在最后一行代码中调用这个函数是，返回的结果是300。