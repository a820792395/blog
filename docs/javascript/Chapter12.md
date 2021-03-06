# DOM2和DOM3
## 样式
### 访问元素的样式
对于使用短划线的CSS属性名，必须将其转换成驼峰大小写形式，例如：CSS的background-color
在JavaScript里面是backgroundColor。
```js
<div id="myDiv" style="background-color: red; width: 300px; height: 300px;"></div>

var div = document.getElementById("myDiv");
console.log(div.style.backgroundColor); // red
console.log(div.style.width); // 300px
console.log(div.style.height);// 300px
```
设置样式
```js
var div = document.getElementById("myDiv");

// 设置背景颜色
div.style.backgroundColor = "red";

// 设置大小
div.style.width = "300px";
div.style.height = "300px"; 
```
### 元素大小
#### 偏移量
**偏移量** ，包括元素在屏幕上占用的所有可见的空间。元素的可见大小由其高度、宽度决定，包括所有内边距、滚动条和边框大小（注意，不包括外边距）。通过下列4个属性可以取得元素的偏移量。
* **offsetHeight**：元素在垂直方向上占用的空间大小，以像素计。包括元素的高度、（可见的）水平滚动条的高度、上边框高度和下边框高度。
* **offsetWidth**：元素在水平方向上占用的空间大小，以像素计。包括元素的宽度、（可见的）垂直滚动条的宽度、左边框宽度和右边框宽度。
* **offsetLeft**：元素的左外边框至包含元素的左内边框之间的元素距离。
* **offsetTop**：元素的上边边框至包含元素的上内边框之间的元素距离。
#### 客户区大小
元素的客户区大小，指的是元素内容及其内边距所占据的空间大小。有关客户区大小的属性有两个：**clientWidth和clientHeight**。其中，**clientWidth** 属性是元素内容区宽度加上左右内边距宽度；**clientHeight** 属性是元素内容区高度加上上下内边距高度。
#### 滚动大小
滚动大小，指的是包含滚动内容的元素的大小。有些元素（例如`<html>`元素），即使没有执行任何代码也能自动地添加滚动条；但另一些元素，则需要通过CSS的overflow属性进行设置才能滚动。以下是4个滚动大小相关的属性。
* **scrollHeight**：在没有滚动条的情况下，元素内容的总高度。
* **scrollWidth**：在没有滚动条的情况下，元素内部的总高度。
* **scrollLeft**：被隐藏在内容域左侧的像素数。通过设置这个属性可以改变元素的滚动位置。
* **scrollTop**：被隐藏在内容区域上方的像素数。通过设置这个属性可以改变元素的滚动位置。