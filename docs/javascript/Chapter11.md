# DOM扩展
## 选择符API
### querySelector()
**querySelector()** 方法接收一个CSS选择符，返回与该模式匹配的第一个元素，如果没有找到匹配的元素，返回null。看下面的例子。
```js
// 取得body元素
var body = document.querySelector("body");

// 取得ID为"myDiv"元素
var myDiv = document.querySelector("#myDiv");

// 取得类为"selected"的第一个元素
var selected = document.querySelector(".selected");

// 取得类为"button"的第一个图像元素
var img = document.body.querySelector("img.button");
```
### querySeletorAll()
**querySeletorAll()** 方法接收的参数和 **querySeletor()** 方法一样，返回的是所有匹配的元素。这个方法返回的是一个NodeList的实例。
```js
// 取得某<div>中的所有<em>元素
var ems = document.getElementById("myDiv").querySelectorAll("em");

// 取得类为"selected"的所有元素
var selected = document.querySelectorAll(".selected");

// 取得所有<p>元素中的所有<strong>元素
var strongs = document.querySelectorAll("p strong");
```
## 元素遍历
**childElementCount**：返回子元素的个数（不包括文本节点和注释）。

**firstElementChild**：指向第一个子元素；

**lastElementChild**：指向最后一个子元素；

**previousElementSibling**：指向前一个同辈元素；

**nextElementSibling**：指向后一个同辈元素；
## HTML5
### 与类相关的扩充
**getElementsByClassName()** 方法接收一个参数，即一个包含一或多个类型的字符串，返回带有指定类的所有元素的 **NodeList** 。
```JS
// 取得所有类中包含"username"和"current"的元素，类名的先后顺序无所谓
var allCurrentUsernames = document.getElementsByClassName("username current");

// 取得ID为"myDiv"的元素中带有类名"selected"的所有元素
var selected = document.getElementById("myDiv").getElementsByClassName("selected");
```
### classList属性
**classList** 属性返回DOMTokenList的实例。有一个表示自己包含多少元素的length属性。
此外这个新类型定义如下方法：

**add()**：将给定的类名添加到列表中。

**contains()**：表示列表中是否存在给定类名，如果存在则返回true，否则返回false。

**remove()**：从列表中删除给定的类名。

**toggle()**：如果列表中已经存在的类名，删除它，如果列表中没有给定的类名，添加它。
```js
var div = document.getElementById("myDiv");
// 删除"user"类名
div.classList.remove("user");

// 添加"current"类名
div.classList.add("current");

// 切换"user"类名
div.classList.toggle("user");

// 判断是否有包含既定的类名
if (div.classList.contains("bd") && !div.classList.contains("disabled")) {
  // 执行操作
}
```
### 焦点管理
**activeElement** 属性，这个属性始终会引用DOM中当前获得了焦点的元素。元素获取焦点的方式有页面加载、用户输入和在代码中调用focus()（设置焦点）方法。来看例子。
```js
var button = document.getElementById("myButton");
button.focus();
console.log(document.activeElement === button); // true
```
**hasFocus()** 方法，这个方法用于确定文档是否获得焦点。
```js
var button = document.getElementById("myButton");
button.focus();
console.log(document.hasFocus());
```
### 自定义数据属性
HTML5规定可以为元素添加非标准的属性，但要添加前缀data-，目的是为元素提供与渲染无关的信息，或者提供语义信息。这些属性可以任意添加、随便命名，只要以data-开头即可。看一个例子。
```js
var div = document.getElementById("myDiv");

// 取得自定义属性的值
var appId = div.dataset.appid;
var myName = div.dataset.myname;
console.log(appId); // 12345
console.log(myName); // xiaoli

// 设置值
div.dataset.appid = 23456;
div.dataset.myname = "xiaoyan";
```
### 插入标记
**innerHTML**属性返回与调用元素的所有子节点（包括元素、注释和文本节点）对应的HTML标记。看下面的例子。
```html
<div id="content">
  <p>This is a<strong>paragraph</strong>with a list following it.</p>
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
    <li>Item 4</li>
  </ul>
</div>
```
对于上面的`<div>`元素来说，它的innerHTML属性会返回如下字符串。
```js
var content = document.getElementById("content");
console.log(content.innerHTML);

<p>This is a<strong>paragraph</strong>with a list following it.</p>
<ul>
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
  <li>Item 4</li>
</ul>
```
在写模式下，**innerHTML** 会根据指定的值创建新的DOM树，然后用这个DOM树完全替换
调用元素原先的所有子节点。看下面的例子。
```js
  var content = document.getElementById("content");
  content.innerHTML = "Hello world";
```
### outerHTML属性
在读模式下，outerHTML返回调用它的元素及所有子节点的HTML标签。在写模式下，
outerHTML会根据指定的HTML字符串创建新的DOM子树，然后用这个DOM子树完全替换
调用元素。
```js
var content = document.getElementById("content");
console.log(content.outerHTML); // 返回了以下的字符串，包括<div>本身
<div id="content">
  <p>This is a<strong>paragraph</strong>with a list following it.</p>
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
    <li>Item 4</li>
  </ul>
</div>
```
使用outerHTML属性以下面这种方式设置值：
```js
  var content = document.getElementById("content");
  content.outerHTML = "<p>This is  a paragraph.</p>"
```
### insertAdjacentHTML()
**insertAdjacentHTML()** 方法接收两个参数：插入位置和要插入的HTML文本。第一个参数必须是以下的值之一：
* **"beforebegin"**：在当前元素之前差一个紧邻的同辈元素；
* **"afterbegin"**：在当前元素之下插入一个新的子元素或在第一个子元素之前再插入新的子元素；
* **"beforeend"**：在当前元素之下插入一个新的子元素或在最后一个子元素之后再插入新的子元素；
* **"afterend"**：在当前元素之后插入一个紧邻的同辈元素。
来看下面的例子：
```js
  var content = document.getElementById("content");
  // 作为前一个同辈元素插入
  content.insertAdjacentHTML("beforebegin", "<h1>Hello world</h1>");

  // 作为第一个子元素插入
  content.insertAdjacentHTML("afterbegin", "<h2>Hello world</h2>");

  // 作为最后一个子元素插入
  content.insertAdjacentHTML("beforeend", "<h3>Hello world</h3>");

  // 作为后一个同辈元素插入
  content.insertAdjacentHTML("afterend", "<h4>Hello world</h4>");
```
### 内存与性能问题
替换子节点可能会导致浏览器的内存占用问题，尤其是在IE中，问题更加明显。在删除带有事件处理程序或引用了其他JavaScript对象子树时，就有可能导致内存占用问题。假设某个元素有一个事件处理程序（或者引用了一个JavaScript对象作为属性），在使用前述某个属性将该元素从文档树中删除后，元素与事件处理程序之间的绑定关系在内存中并没有一并删除。如果这种情况频繁出现，页面占用的内存数量就会明显增加。因此，在使用innerHTML、outerHTML属性和insertAdjacentHTML()方法时，最好先手动删除要被替换的元素的所有事件处理程序和JavaScript对象属性。

### scrollIntoView()方法
scrollIntoView()方法让当前的元素滚动到浏览器窗口的可视区域内，这个方法传入true作为参数，或者不传入任何参数，那么窗口滚动之后会让调用元素的顶部与视口顶部尽可能平齐。如果传入false做为参数，调用元素就会出现在视口中（可能的话，
调用元素的底部会与视口底部平齐。），取决于其他元素的布局情况，此元素可能不会完全滚动到顶端或底端。

## 专有扩展
### children属性
children属性与childNodes没什么区别，都是用于获取元的子节点。
```js
var myList = document.getElementById("myList");
console.log(myList.children); // HTMLCollection(4)
console.log(myList.children[0]); // 返回第一个子元素，包含文本节点
```
### contains()方法
**contains()** 用于某个节点是不是另一个子节点的后代这个方法接收一个参数，既要
检测的后代节点，如果被检测的节点是后代节点，该方法返回true；否则返回false。
```js
console.log(document.documentElement.contains(myList)); // true
console.log(document.documentElement.contains(document.body)); // true
```
### 插入文本
#### innerText属性
通过innerText属性可以操作元素中包含的所有文本内容，包括子文档树中的文本。在读取值市，它会按照由浅入深的顺序，将子文档树中的所有文本拼接起来。在写入值时，结果会删除元素的所有子节点，插入包含相应文本值的文本节点。来看下面的例子：
```html
<div id="content">
  <p>This is a <strong>paragraph</strong> with a list following it.</p>
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
  </ul>
</div>
```
对于这个例子中的`<div>`元素而言，其innerText属性会返回下列字符串：
```js
var content = document.getElementById("content");
console.log(content.innerText);
// This is a paragraph with a list following it.
//
// Item 1
// Item 2
// Item 3
```
使用innerText来设置这个`<div>`元素的内容，则只需一行代码：
```js
content.innerText = "Hello world";
```
执行这行代码后，页面的HTML代码就会变成如下所示。
```html
<div id="content">Hello world</div>
```
#### outerText属性
**outerText** 和 **innerText** 基本上没有多大区别。在读取文本值时，和innerText的结果完全一样，但在写模式下，outerText不只是替换调用它的元素的子节点，而是会替换整个元素（包括子节点）。
```js
var content = document.getElementById("content");
content.outerText = "Hello world";
```
本质上，新的文本节点会完全取代调用outerText的元素。此后，该元素就从文档中被删除，无法访问。由于这个属性会导致调用它的元素不存在，因此并不常用。建议尽可能不要使用这个属性。

