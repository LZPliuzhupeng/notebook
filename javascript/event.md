# 事件

JavaScript与HTML的交互是通过事件实现的，事件代表文档或浏览器窗口中某个有意义的时刻。可以使用仅在事件发生时执行的监听器（也叫处理程序）订阅事件。在传统软件工程领域，这个模型叫“观察者模式”，其能够做到页面行为（在JavaScript中定义）与页面展示（在HTML和CSS中定义）的分离。
## 事件流

?> 在一张纸上画几个同心圆。把手指放到圆心上，则手指不仅是在一个圆圈里，而且是在所有的圆圈里。

事件流描述了页面接收事件的顺序。结果非常有意思，IE和Netscape开发团队提出了几乎完全相反的事件流方案。IE将支持事件冒泡流，而Netscape Communicator将支持事件捕获流。

### 事件冒泡

IE事件流被称为**事件冒泡**，这是因为事件被定义为从最具体的元素（文档树中最深的节点）开始触发，然后向上传播至没有那么具体的元素（文档）。

```html
<!DOCTYPE html>
<html>
<head>
  <title>Event Bubbling Example</title>
</head>
<body>
  <div id="myDiv">Click Me</div>
</body>
</html>
```
在点击页面中的<div>元素后，click事件会以如下顺序发生：`div > body > html > document`

`<div>`元素，即被点击的元素，最先触发`click`事件。然后，`click`事件沿DOM树一路向上，在经过的每个节点上依次触发，直至到达`document`对象。

![](../assets/image/javascript/event__事件流__事件冒泡.png ':size=40%')

所有现代浏览器都支持事件冒泡，只是在实现方式上会有一些变化。IE5.5及早期版本会跳过<html>元素（从<body>直接到document）。现代浏览器中的事件会一直冒泡到window对象。

### 事件捕获

Netscape Communicator团队提出了另一种名为**事件捕获**的事件流。事件捕获的意思是最不具体的节点应该最先收到事件，而最具体的节点应该最后收到事件。事件捕获实际上是为了在事件到达最终目标前拦截事件。

在点击页面中的`<div>`元素后，click事件会以如下顺序发生：`document > html > body > div`

在事件捕获中，click事件首先由document元素捕获，然后沿DOM树依次向下传播，直至到达实际的目标元素<div>。

![](../assets/image/javascript/event__事件流__事件捕获.png ':size=40%')

虽然这是Netscape Communicator唯一的事件流模型，但事件捕获得到了所有现代浏览器的支持。实际上，所有浏览器都是从`window`对象开始捕获事件，而DOM2 Events规范规定的是从`document`开始。

> 由于旧版本浏览器不支持，因此实际当中几乎不会使用事件捕获。通常建议使用事件冒泡，特殊情况下可以使用事件捕获

### DOM事件流

DOM2 Events规范规定事件流分为3个阶段：`事件捕获`、`到达目标`和`事件冒泡`。

事件捕获最先发生，为提前拦截事件提供了可能。然后，实际的目标元素接收到事件。最后一个阶段是冒泡，最迟要在这个阶段响应事件。

点击`<div>`元素会以如下图所示的顺序触发事件：

![](../assets/image/javascript/event__事件流__DOM事件流.png ':size=50%')

在DOM事件流中，实际的目标（`<div>`元素）在捕获阶段不会接收到事件。这是因为捕获阶段从`document`到`<html>`再到`<body>`就结束了。下一阶段，即会在`<div>`元素上触发事件的“到达目标”阶段，通常在事件处理时被认为是冒泡阶段的一部分（稍后讨论）。然后，冒泡阶段开始，事件反向传播至文档。

大多数支持DOM事件流的浏览器实现了一个小小的拓展。虽然DOM2 Events规范明确捕获阶段不命中事件目标，但现代浏览器都会在捕获阶段在事件目标上触发事件。最终结果是在事件目标上有两个机会来处理事件。

!> 注意　所有现代浏览器都支持DOM事件流，只有IE8及更早版本不支持。

## 事件处理程序

事件意味着用户或浏览器执行的某种动作。比如，单击（`click`）、加载（`load`）、鼠标悬停（`mouseover`）。为响应事件而调用的函数被称为**事件处理程序**（或**事件监听器**）。

### DOM0事件处理程序

在JavaScript中指定事件处理程序的传统方式是把一个函数赋值给（DOM元素的）一个事件处理程序属性。要使用JavaScript指定事件处理程序，必须先取得要操作对象的引用。

```js
let btn = document.getElementById("myBtn");
btn.onclick = function() {
  console.log("Clicked");
};
```
!> 注意，前面的代码在运行之后才会给事件处理程序赋值。因此如果在页面中上面的代码出现在按钮之后，则有可能出现用户点击按钮没有反应的情况。

像这样使用DOM0方式为事件处理程序赋值时，所赋函数被视为元素的方法。因此，事件处理程序会在元素的作用域中运行，即this等于元素。
```js
let btn = document.getElementById("myBtn");
btn.onclick = function() {
  console.log(this.id);  // "myBtn"
};
```

?> 以这种方式添加事件处理程序是注册在事件流的**冒泡阶段**的。

通过将事件处理程序属性的值设置为`null`，可以移除通过DOM0方式添加的事件处理程序。
```js
btn.onclick = null;  // 移除事件处理程序
```

### DOM2事件处理程序

DOM2 Events为事件处理程序的赋值和移除定义了两个方法：`addEventListener()`和`removeEventListener()`。这两个方法暴露在所有DOM节点上，它们接收3个参数：`事件名`、`事件处理函数`和`一个布尔值`，`true`表示在捕获阶段调用事件处理程序，`false`（默认值）表示在冒泡阶段调用事件处理程序。

```js
let btn = document.getElementById("myBtn");
btn.addEventListener("click", () => {
  console.log(this.id);
}, false);
```
以上代码为按钮添加了会在事件冒泡阶段触发的`onclick`事件处理程序（因为最后一个参数值为`false`）。与DOM0方式类似，这个事件处理程序同样在被附加到的元素的作用域中运行。使用DOM2方式的主要优势是可以为同一个事件添加多个事件处理程序。
```js
let btn = document.getElementById("myBtn");
btn.addEventListener("click", () => {
  console.log(this.id);
}, false);
btn.addEventListener("click", () => {
  console.log("Hello world!");
}, false);
```

**通过`addEventListener()`添加的事件处理程序只能使用`removeEventListener()`并传入与添加时同样的参数来移除。这意味着使用`addEventListener()`添加的匿名函数无法移除。**
```js
let btn = document.getElementById("myBtn");
btn.addEventListener("click", () => {
  console.log(this.id);
 }, false);

// 其他代码

btn.removeEventListener("click", function() {    // 没有效果！
  console.log(this.id);
}, false);
```
这个例子通过`addEventListener()`添加了一个匿名函数作为事件处理程序。然后，又以看起来相同的参数调用了`removeEventListener()`。但实际上，传递给`removeEventListener()`的第二个参数与传给`addEventListener()`的完全不是一回事。

传给removeEventListener()的事件处理函数必须与传给addEventListener()的是同一个:
```js
let btn = document.getElementById("myBtn");
let handler = function() {
  console.log(this.id);
};
btn.addEventListener("click", handler, false);

// 其他代码

btn.removeEventListener("click", handler, false);  // 有效果！
```

### IE事件处理程序

IE实现了与DOM类似的方法，即`attachEvent()`和`detachEvent()`。这两个方法接收两个同样的参数：`事件处理程序的名字`和`事件处理函数`。因为IE8及更早版本只支持事件冒泡，所以使用`attachEvent()`添加的事件处理程序会添加到冒泡阶段。

**使用`attachEvent()`给按钮添加click事件处理程序：**
```js
var btn = document.getElementById("myBtn");
btn.attachEvent("onclick", function() {
  console.log("Clicked");
});
```

**在IE中使用`attachEvent()`与使用DOM0方式的主要区别是事件处理程序的作用域。使用DOM0方式时，事件处理程序中的`this`值等于目标元素。而使用`attachEvent()`时，事件处理程序是在全局作用域中运行的，因此`this`等于`window`。**
```js
var btn = document.getElementById("myBtn");
btn.attachEvent("onclick", function() {
  console.log(this === window);   // true
});
```

**与使用`addEventListener()`一样，使用`attachEvent()`方法也可以给一个元素添加多个事件处理程序。**
```js
var btn = document.getElementById("myBtn");
btn.attachEvent("onclick", function() {
  console.log("Clicked");
});
btn.attachEvent("onclick", function() {
  console.log("Hello world!");
});
```
这里调用了两次`attachEvent()`，分别给同一个按钮添加了两个不同的事件处理程序。不过，与DOM方法不同，这里的事件处理程序会以添加它们的顺序**反向触发**。换句话说，在点击例子中的按钮后，控制台中会先打印出`"Hello world!"`，然后再打印出`"Clicked"`。

使用`attachEvent()`添加的事件处理程序将使用`detachEvent()`来移除，只要提供相同的参数。与使用DOM方法类似，作为事件处理程序添加的匿名函数也无法移除。但只要传给`detachEvent()`方法相同的函数引用，就可以移除。
```js
var btn = document.getElementById("myBtn");
var handler = function() {
  console.log("Clicked");
};
btn.attachEvent("onclick", handler);

// 其他代码

btn.detachEvent("onclick", handler);
```

### 跨浏览器事件处理程序

为了以跨浏览器兼容的方式处理事件，很多开发者会选择使用一个JavaScript库，其中抽象了不同浏览器的差异。有些开发者也可能会自己编写代码，以便使用最合适的事件处理手段。自己编写跨浏览器事件处理代码也很简单，主要依赖能力检测。要确保事件处理代码具有最大兼容性，只需要让代码在冒泡阶段运行即可。

```js
var EventUtil = {
  addHandler: function(element, type, handler) {
    if (element.addEventListener) {
      element.addEventListener(type, handler, false);
    } else if (element.attachEvent) {
      element.attachEvent("on" + type, handler);
    } else {
      element["on" + type] = handler;
    }
  },

  removeHandler: function(element, type, handler) {
    if (element.removeEventListener) {
      element.removeEventListener(type, handler, false);
    } else if (element.detachEvent) {
      element.detachEvent("on" + type, handler);
    } else {
      element["on" + type] = null;
    }
  }
};
```
这里的`addHandler()和removeHandler()`方法并没有解决所有跨浏览器一致性问题，比如IE的作用域问题、多个事件处理程序执行顺序问题等。不过，这两个方法已经实现了跨浏览器添加和移除事件处理程序。另外也要注意，DOM0只支持给一个事件添加一个处理程序。好在DOM0浏览器已经很少有人使用了，所以影响应该不大。

## 事件对象

在DOM中发生事件时，所有相关信息都会被收集并存储在一个名为event的对象中。这个对象包含了一些基本信息，比如导致事件的元素、发生的事件类型，以及可能与特定事件相关的任何其他数据。例如，鼠标操作导致的事件会生成鼠标位置信息，而键盘操作导致的事件会生成与被按下的键有关的信息。

### DOM事件对象

在DOM合规的浏览器中，`event`对象是传给事件处理程序的唯一参数。不管以哪种方式（DOM0或DOM2）指定事件处理程序，都会传入这个`event`对象。
```js
let btn = document.getElementById("myBtn");
btn.onclick = function(event) {
  console.log(event.type);  // "click"
};

btn.addEventListener("click", (event) => {
  console.log(event.type);  // "click"
}, false);
```

| 属性/方法 | 类型 | 读/写 | 说明 |
| ---- | ---- | ---- | ---- |
| `bubbles` | 布尔值 | 只读 | 表示事件是否冒泡 |
| `cancelable` | 布尔值 | 只读 | 示是否可以取消事件的默认行为 |
| `currentTarget	` | 元素 | 只读 | 当前事件处理程序所在的元素 |
| `defaultPrevented` | 布尔值 | 只读 | `true`表示已经调用`preventDefault()`方法（DOM3 Events中新增） |
| `detail` | 整数 | 只读 | 事件相关的其他信息 |
| `eventPhase` | 整数 | 只读 | 表示调用事件处理程序的阶段：1代表捕获阶段，2代表到达目标，3代表冒泡阶段 |
| `preventDefault()` | 函数 | 只读 | 用于取消事件的默认行为。只有`cancelable`为`true`才可以调用这个方法 |
| `stopImmediatePropagation()` | 函数 | 只读 | 用于取消所有后续事件捕获或事件冒泡，并阻止调用任何后续事件处理程序（DOM3 Events中新增） |
| `stopPropagation()` | 函数 | 只读 | 用于取消所有后续事件捕获或事件冒泡。只有`bubbles`为`true`才可以调用这个方法 |
| `target` | 元素 | 只读 | 事件目标 |
| `trusted` | 布尔值 | 只读 | `true`表示事件是由浏览器生成的。`false`表示事件是开发者通过JavaScript创建的（DOM3 Events中新增） |
| `type` | 字符串 | 只读 | 被触发的事件类型 |
| `View` | AbstractView | 只读 | 与事件相关的抽象视图。等于事件所发生的`window`对象 |


**在事件处理程序内部，`this`对象始终等于`currentTarget`的值，而`target`只包含事件的实际目标。如果事件处理程序直接添加在了意图的目标，则`this`、`currentTarget`和`target`的值是一样的。**
```js
let btn = document.getElementById("myBtn");
btn.onclick = function(event) {
  console.log(event.currentTarget === this);  // true
  console.log(event.target === this);         // true
};
```


**如果这个事件处理程序是添加到按钮的父节点（如`document.body`）上，那么它们的值就不一样了。**
```js
document.body.onclick = function(event) {
  console.log(event.currentTarget === document.body);              // true
  console.log(this === document.body);                             // true
  console.log(event.target === document.getElementById("myBtn"));  // true
};
```
这种情况下点击按钮，`this`和`currentTarget`都等于`document.body`，这是因为它是注册事件处理程序的元素。而`target`属性等于按钮本身，这是因为那才是`click`事件真正的目标。由于按钮本身并没有注册事件处理程序，因此`click`事件冒泡到`document.body`，从而触发了在它上面注册的处理程序。


**`type`被触发的事件类型**
```js
let btn = document.getElementById("myBtn");
let handler = function(event) {
  switch(event.type) {
    case "click":
      console.log("Clicked");
      break;
    case "mouseover":
      event.target.style.backgroundColor = "red";
      break;
    case "mouseout":
      event.target.style.backgroundColor = "";
      break;
  }
};

btn.onclick = handler;
btn.onmouseover = handler;
btn.onmouseout = handler;
```

**`preventDefault()`方法用于阻止特定事件的默认动作。**比如，阻止链接在被单击时导航到href属性指定URL的默认行为。
```js
let link = document.getElementById("myLink");
link.onclick = function(event) {
  event.preventDefault();
};
```
> 任何可以通过preventDefault()取消默认行为的事件，其事件对象的cancelable属性都会设置为true


**`stopPropagation()`方法用于立即阻止事件流在DOM结构中传播，取消后续的事件捕获或冒泡。**例如，直接添加到按钮的事件处理程序中调用stopPropagation()，可以阻止document.body上注册的事件处理程序执行。
```js
let btn = document.getElementById("myBtn");
btn.onclick = function(event) {
  console.log("Clicked");
  event.stopPropagation();
};

document.body.onclick = function(event) {
  console.log("Body clicked");
};
```
如果不调用`stopPropagation()`，那么点击按钮就会打印两条消息。但这里由于`click`事件不会传播到`document.body`，因此`onclick`事件处理程序永远不会执行。


**`eventPhase`属性可用于确定事件流当前所处的阶段。**如果事件处理程序在捕获阶段被调用，则`eventPhase`等于1；如果事件处理程序在目标上被调用，则`eventPhase`等于2；如果事件处理程序在冒泡阶段被调用，则`eventPhase`等于3。

虽然“到达目标”是在冒泡阶段发生的，但其`eventPhase`仍然等于2。
```js
let btn = document.getElementById("myBtn");
btn.onclick = function(event) {
  console.log(event.eventPhase);   // 2
};

document.body.addEventListener("click", (event) => {
  console.log(event.eventPhase);   // 1
}, true);

document.body.onclick = (event) => {
  console.log(event.eventPhase);   // 3
};

1
2
3
```
点击按钮首先会触发注册在捕获阶段的`document.body`上的事件处理程序，显示`eventPhase`为1。接着，会触发按钮本身的事件处理程序（尽管是注册在冒泡阶段），此时显示`eventPhase`等于2。最后触发的是注册在冒泡阶段的`document.body`上的事件处理程序，显示`eventPhase`为3。而当`eventPhase`等于2时，`this`、`targe`t和`currentTarget`三者相等。

!> 注意　`event`对象只在事件处理程序执行期间存在，一旦执行完毕，就会被销毁。

### IE事件对象

与DOM事件对象不同， IE事件对象可以基于事件处理程序被指定的方式以不同方式来访问。如果事件处理程序是使用DOM0方式指定的，则event对象只是window对象的一个属性：
```js
var btn = document.getElementById("myBtn");
btn.onclick = function() {
  let event = window.event;
  console.log(event.type);  // "click"
};
```

如果事件处理程序是使用attachEvent()指定的，则event对象会作为唯一的参数传给处理函数，event对象仍然是window对象的属性（像DOM0方式那样），只是出于方便也将其作为参数传入。
```js
var btn = document.getElementById("myBtn");
btn.attachEvent("onclick", function(event) {
  console.log(event.type);  // "click"
});
```

IE事件对象也包含与导致其创建的特定事件相关的属性和方法，其中很多都与相关的DOM属性和方法对应。与DOM事件对象一样，基于触发的事件类型不同，`event`对象中包含的属性和方法也不一样。不过，所有IE事件对象都会包含下表所列的公共属性和方法。

| 属性/方法 | 类型 | 读/写 | 说明 |
| ---- | ---- | ---- | ---- |
| `cancelBubble` | 布尔值 | 读/写 | 默认为`false`，设置为`true`可以取消冒泡（与DOM的`stopPropagation()`方法相同） |
| `returnValue` | 布尔值 | 读/写 | 默认为`true`，设置为`false`可以取消事件默认行为（与DOM的`preventDefault()`方法相同） |
| `srcElement	` | 元素 | 只读 | 事件目标（与DOM的`target`属性相同） |
| `type` | 字符串 | 只读 | 触发的事件类型 |

由于事件处理程序的作用域取决于指定它的方式，因此this值并不总是等于事件目标。为此，更好的方式是使用事件对象的srcElement属性代替this。不同事件对象上的srcElement属性中保存的都是事件目标：
```js
var btn = document.getElementById("myBtn");
btn.onclick = function() {
  console.log(window.event.srcElement === this);  // true
};

btn.attachEvent("onclick", function(event) {
  console.log(event.srcElement === this);         // false
});
```

**`returnValue`属性等价于DOM的`preventDefault()`方法，都是用于取消给定事件默认的行为。只不过在这里要把`returnValue`设置为`false`才是阻止默认动作**
```js
var link = document.getElementById("myLink");
link.onclick = function() {
  window.event.returnValue = false;
};
```

**`cancelBubble`属性与DOM`stopPropagation()`方法用途一样，都可以阻止事件冒泡。**因为IE8及更早版本不支持捕获阶段，所以只会取消冒泡。stopPropagation()则既取消捕获也取消冒泡。
```js
var btn = document.getElementById("myBtn");
btn.onclick = function() {
  console.log("Clicked");
  window.event.cancelBubble = true;
};

document.body.onclick = function() {
  console.log("Body clicked");
};
```

### 跨浏览器事件对象

虽然DOM和IE的事件对象并不相同，但它们有足够的相似性可以实现跨浏览器方案。
```js
var EventUtil = {
  addHandler: function(element, type, handler) {
     // 为节省版面，删除了之前的代码
  },

  getEvent: function(event) {
    return event ? event : window.event;
  },

  getTarget: function(event) {
    return event.target || event.srcElement;

  },

  preventDefault: function(event) {
    if (event.preventDefault) {
      event.preventDefault();
    } else {
      event.returnValue = false;
    }
  },

  removeHandler: function(element, type, handler) {
    // 为节省版面，删除了之前的代码
  },

  stopPropagation: function(event) {
    if (event.stopPropagation) {
      event.stopPropagation();
    } else {
      event.cancelBubble = true;
    }
  }

};
```

**`getEvent()`，其返回对event对象的引用。**
```js
btn.onclick = function(event) {
  event = EventUtil.getEvent(event);
};
```

**`getTarget()`，其返回事件目标。**
```js
btn.onclick = function(event) {
  event = EventUtil.getEvent(event);
  let target = EventUtil.getTarget(event);
};
```

**`preventDefault()`，其用于阻止事件的默认行为。**
```js
let link = document.getElementById("myLink");
link.onclick = function(event) {
  event = EventUtil.getEvent(event);
  EventUtil.preventDefault(event);
};
```

**`stopPropagation()`先检测用于停止事件流的DOM方法，如果没有再使用cancelBubble属性。**
```js
let btn = document.getElementById("myBtn");
btn.onclick = function(event) {
  console.log("Clicked");
  event = EventUtil.getEvent(event);
  EventUtil.stopPropagation(event);
};

document.body.onclick = function(event) {
  console.log("Body clicked");
};
```
这个方法在浏览器上可能会停止事件冒泡，也可能会既停止事件冒泡也停止事件捕获。

## 事件类型

用户界面事件（`UIEvent`）：涉及与BOM交互的通用浏览器事件。

焦点事件（`FocusEvent`）：在元素获得和失去焦点时触发。

鼠标事件（`MouseEvent`）：使用鼠标在页面上执行某些操作时触发。

滚轮事件（`WheelEvent`）：使用鼠标滚轮（或类似设备）时触发。

输入事件（`InputEvent`）：向文档中输入文本时触发。

键盘事件（`KeyboardEvent`）：使用键盘在页面上执行某些操作时触发。

合成事件（`CompositionEvent`）：在使用某种IME（Input Method Editor，输入法编辑器）输入字符时触发。

除了这些事件类型之外，HTML5还定义了另一组事件，而浏览器通常在DOM和BOM上实现专有事件。这些专有事件基本上都是根据开发者需求而不是按照规范增加的，因此不同浏览器的实现可能不同。

### 用户界面事件

用户界面事件或UI事件不一定跟用户操作有关。这类事件在DOM规范出现之前就已经以某种形式存在了，保留它们是为了向后兼容。UI事件主要有以下几种。

`DOMActivate`：元素被用户通过鼠标或键盘操作激活时触发（比`click`或`keydown`更通用）。这个事件在DOM3 Events中已经废弃。因为浏览器实现之间存在差异，所以不要使用它。

`load`：在`window`上当页面加载完成后触发，在窗套（`<frameset>`）上当所有窗格（`<frame>`）都加载完成后触发，在`<img>`元素上当图片加载完成后触发，在`<object>`元素上当相应对象加载完成后触发。

`unload`：在`window`上当页面完全卸载后触发，在窗套上当所有窗格都卸载完成后触发，在`<object>`元素上当相应对象卸载完成后触发。

`abort`：在`<object>`元素上当相应对象加载完成前被用户提前终止下载时触发。

`error`：在`window`上当JavaScript报错时触发，在`<img>`元素上当无法加载指定图片时触发，在`<object>`元素上当无法加载相应对象时触发，在窗套上当一个或多个窗格无法完成加载时触发。

`select`：在文本框（`<input>`或`textarea`）上当用户选择了一个或多个字符时触发。

`resize`：在window或窗格上当窗口或窗格被缩放时触发。

`scroll`：当用户滚动包含滚动条的元素时在元素上触发。`<body>`元素包含已加载页面的滚动条。

除了`DOMActivate`，这些事件在DOM2 Events中都被归为HTML Events（`DOMActivate`在DOM2中仍旧是UI事件）。


#### `load`事件

在`window`对象上，`load`事件会在整个页面（包括所有外部资源如图片、JavaScript文件和CSS文件）加载完成后触发。可以通过两种方式指定`load`事件处理程序。
```js
window.addEventListener("load", (event) => {
  console.log("Loaded!");
});
```
```js
<!DOCTYPE html>
<html>
<head>
  <title>Load Event Example</title>
</head>
<body onload="console.log('Loaded!')">

</body>
</html>
```
一般来说，任何在`window`上发生的事件，都可以通过给`<body>`元素上对应的属性赋值来指定，这是因为HTML中没有`window`元素。这实际上是为了保证向后兼容的一个策略，但在所有浏览器中都能得到很好的支持。实际开发中要尽量使用JavaScript方式。

!> 注意　根据DOM2 Events，`load`事件应该在`document`而非`window`上触发。可是为了向后兼容，所有浏览器都在`window`上实现了`load`事件。

图片上也会触发`load`事件，包括DOM中的图片和非DOM中的图片。
```js
<img src="smile.gif" onload="console.log('Image loaded.')">
```
```js
let image = document.getElementById("myImage");
image.addEventListener("load", (event) => {
  console.log(event.target.src);
});
```
```js
window.addEventListener("load", () => {
  let image = document.createElement("img");
  image.addEventListener("load", (event) => {
    console.log(event.target.src);
  });
  document.body.appendChild(image);
  image.src = "smile.gif";
});
```
```js
window.addEventListener("load", () => {
  let image = new Image();
  image.addEventListener("load", (event) => {
    console.log("Image loaded!");
  });
  image.src = "smile.gif";
});
```


`<script>`元素会在JavaScript文件加载完成后触发load事件，从而可以动态检测。与图片不同，要下载JavaScript文件必须同时指定`src`属性并把`<script>`元素添加到文档中。
```js
window.addEventListener("load", () => {
  let script = document.createElement("script");
  script.addEventListener("load", (event) => {
    console.log("Loaded");
  });
  script.src = "example.js";
  document.body.appendChild(script);
});
```
```js
window.addEventListener("load", () => {
  let link = document.createElement("link");
  link.type = "text/css";
  link.rel= "stylesheet";
  link.addEventListener("load", (event) => {
    console.log("css loaded");
  });
  link.href = "example.css";
  document.getElementsByTagName("head")[0].appendChild(link);
});
```
> IE8及更早版本不支持`<script>`元素触发`load` 事件。IE和Opera支持`<link>`元素触发`load`事件。

#### `unload`事件

与`load`事件相对的是`unload`事件，`unload`事件会在文档卸载完成后触发。`unload`事件一般是在从一个页面导航到另一个页面时触发，最常用于清理引用，以避免内存泄漏。
```js
window.addEventListener("unload", (event) => {
  console.log("Unloaded!");
});
```
```js
<!DOCTYPE html>
<html>
<head>
  <title>Unload Event Example</title>
</head>
<body onunload="console.log('Unloaded!')">

</body>
</html>
```
因为`unload`事件是在页面卸载完成后触发的，所以不能使用页面加载后才有的对象。此时要访问DOM或修改页面外观都会导致错误。


#### `resize`事件

当浏览器窗口被缩放到新高度或宽度时，会触发`resize`事件。
```js
window.addEventListener("resize", (event) => {
  console.log("Resized");
});
```

不同浏览器在决定何时触发`resize`事件上存在重要差异。IE、Safari、Chrome和Opera会在窗口缩放超过1像素时触发`resize`事件，然后随着用户缩放浏览器窗口不断触发。Firefox早期版本则只在用户停止缩放浏览器窗口时触发`resize`事件。无论如何，都应该避免在这个事件处理程序中执行过多计算。否则可能由于执行过于频繁而导致浏览器响应明确变慢。

!> 注意　浏览器窗口在最大化和最小化时也会触发`resize`事件。 

#### scroll事件

```js
window.addEventListener("scroll", (event) => {
  if (document.compatMode == "CSS1Compat") {
    console.log(document.documentElement.scrollTop);
  } else {
    console.log(document.body.scrollTop);
  }
});
```

### 焦点事件

鼠标事件是Web开发中最常用的一组事件，这是因为鼠标是用户的主要定位设备。DOM3 Events定义了9种鼠标事件。

`click`：在用户单击鼠标主键（通常是左键）或按键盘回车键时触发。这主要是基于无障碍的考虑，让键盘和鼠标都可以触发`onclick`事件处理程序。  
`dblclick`：在用户双击鼠标主键（通常是左键）时触发。这个事件不是在DOM2 Events中定义的，但得到了很好的支持，DOM3 Events将其进行了标准化。  
`mousedown`：在用户按下任意鼠标键时触发。这个事件不能通过键盘触发。  
`mouseenter`：在用户把鼠标光标从元素外部移到元素内部时触发。这个事件不冒泡，也不会在光标经过后代元素时触发。`mouseenter`事件不是在DOM2 Events中定义的，而是DOM3 Events中新增的事件。  
`mouseleave`：在用户把鼠标光标从元素内部移到元素外部时触发。这个事件不冒泡，也不会在光标经过后代元素时触发。`mouseleave`事件不是在DOM2 Events中定义的，而是DOM3 Events中新增的事件。  
`mousemove`：在鼠标光标在元素上移动时反复触发。这个事件不能通过键盘触发。  
`mouseout`：在用户把鼠标光标从一个元素移到另一个元素上时触发。移到的元素可以是原始元素的外部元素，也可以是原始元素的子元素。这个事件不能通过键盘触发。  
`mouseover`：在用户把鼠标光标从元素外部移到元素内部时触发。这个事件不能通过键盘触发。  
`mouseup`：在用户释放鼠标键时触发。这个事件不能通过键盘触发。  

**由于事件之间存在关系，因此取消鼠标事件的默认行为也会影响其他事件。**

+ `click`事件触发的前提是`mousedown`事件触发后，紧接着又在同一个元素上触发了`mouseup`事件。如果`mousedown`和`mouseup`中的任意一个事件被取消，那么`click`事件就不会触发。

+ 两次连续的`click`事件会导致`dblclick`事件触发。只要有任何逻辑阻止了这两个`click`事件发生（比如取消其中一个`click`事件或者取消`mousedown`或`mouseup`事件中的任一个），dblclick事件就不会发生。

这4个事件永远会按照如下顺序触发：

1.`mousedown` > 2.`mouseup` > 3.`click` > 4.`mousedown` > 5.`mouseup` > 6.`click` > 7.`dblclick `

`click`和`dblclick`在触发前都依赖其他事件触发，`mousedown`和`mouseup`则不会受其他事件影响。

IE8及更早版本的实现中有个问题，这会导致双击事件跳过第二次`mousedown`和`click`事件。相应的顺序变成了:

1.`mousedown` > 2.`mouseup` > 3.`click` > 4.`mouseup` > 5.`dblclick`

鼠标事件在DOM3 Events中对应的类型是`"MouseEvent"`，而不是`"MouseEvents"`。

鼠标事件还有一个名为滚轮事件的子类别。滚轮事件只有一个事件`mousewheel`，反映的是鼠标滚轮或带滚轮的类似设备上滚轮的交互。

#### `clientX`和`clientY`,客户端坐标

**客户端坐标是事件发生时鼠标光标在客户端`视觉窗口`中的坐标**。这些信息被保存在`event`对象的`clientX`和`clientY`属性中。这两个属性表示事件发生时鼠标光标在视口中的坐标，所有浏览器都支持。

![](../assets/image/javascript/event__事件类型__焦点事件01.png ':size=70%')

#### `pageX`和`pageY`,页面坐标

**页面坐标是事件发生时鼠标光标在页面上的坐标。**

在页面没有滚动时，`pageX`和`pageY`与`clientX`和`clientY`的值相同。这两个属性表示鼠标光标在页面上的位置，因此反映的是光标到页面而非视口左边与上边的距离。

> 在页面没有滚动时，`pageX`和`pageY`与`clientX`和`clientY`的值相同。

#### `screenX`和`screenY`,屏幕坐标

鼠标光标在屏幕上的坐标

![](../assets/image/javascript/event__事件类型__焦点事件02.png ':size=70%')

#### 修饰键

虽然鼠标事件主要是通过鼠标触发的，但有时候要确定用户想实现的操作，还要考虑键盘按键的状态。键盘上的**修饰键****Shift**、**Ctrl**、**Alt**和**Meta**经常用于修改鼠标事件的行为。DOM规定了4个属性来表示这几个修饰键的状态：`shiftKey`、`ctrlKey`、`altKey`和`metaKey`。这几属性会在各自对应的修饰键被按下时包含布尔值`true`，没有被按下时包含`false`。
```js
let div = document.getElementById("myDiv");
div.addEventListener("click", (event) => {
  let keys = new Array();

  if (event.shiftKey) {
    keys.push("shift");
  }

  if (event.ctrlKey) {
    keys.push("ctrl");
  }

  if (event.altKey) {
    keys.push("alt");
  }

  if (event.metaKey) {
    keys.push("meta");
  }

  console.log("Keys: " + keys.join(","));

});
```
!> 注意　现代浏览器支持所有这4个修饰键。IE8及更早版本不支持`metaKey`属性。

#### 相关元素

对`mouseover`和`mouseout`事件而言，还存在与事件相关的其他元素。这两个事件都涉及从一个元素的边界之内把光标移到另一个元素的边界之内。对`mouseover`事件来说，事件的主要目标是获得光标的元素，相关元素是失去光标的元素。类似地，对`mouseout`事件来说，事件的主要目标是失去光标的元素，而相关元素是获得光标的元素。

例如：这个页面中只包含一个`<div>`元素。如果光标开始在`<div>`元素上，然后从它上面移出，则`<div>`元素上会触发`mouseout`事件，相关元素为`<body>`元素。与此同时，`<body>`元素上会触发`mouseover`事件，相关元素是`<div>`元素。
```js
<!DOCTYPE html>
<html>
<head>
  <title>Related Elements Example</title>
</head>
<body>
  <div id="myDiv"
       style="background-color:red;height:100px;width:100px;"></div>
</body>
</html>
```

DOM通过`event`对象的`relatedTarget`属性提供了相关元素的信息。这个属性只有在`mouseover`和`mouseout`事件发生时才包含值，其他所有事件的这个属性的值都是`null`。

IE8及更早版本不支持`relatedTarget`属性，但提供了其他的可以访问到相关元素的属性。在`mouseover`事件触发时，IE会提供`fromElement`属性，其中包含相关元素。而在`mouseout`事件触发时，IE会提供`toElement`属性，其中包含相关元素。（IE9支持所有这些属性。）因此，可以在`EventUtil`中增加一个通用的获取相关属性的方法：
```js
var EventUtil = {

  // 其他代码

  getRelatedTarget: function(event) {
    if (event.relatedTarget) {
      return event.relatedTarget;
    } else if (event.toElement) {
      return event.toElement;
    } else if (event.fromElement) {
      return event.fromElement;
    } else {
      return null;
    }
  },

  // 其他代码

};
```

与其他跨浏览器方法一样，这个方法同样使用特性检测来确定要返回哪个值。可以像下面这样使用`EventUtil.getRelatedTarget()`方法：
```js
let div = document.getElementById("myDiv");
div.addEventListener("mouseout", (event) => {
  let target = event.target;
  let relatedTarget = EventUtil.getRelatedTarget(event);
  console.log(
    `Moused out of ${target.tagName} to ${relatedTarget.tagName}`);
});
```


#### mousewheel

`mousewheel`事件会在用户使用鼠标滚轮时触发，包括在垂直方向上任意滚动。这个事件会在任何元素上触发，并（在IE8中）冒泡到`document`和（在所有现代浏览器中）`window`。`mousewheel`事件的`event`对象包含鼠标事件的所有标准信息，此外还有一个名为`wheelDelta`的新属性。当鼠标滚轮向前滚动时，`wheelDelta`每次都是+120；而当鼠标滚轮向后滚动时，`wheelDelta`每次都是–120。


#### 触摸屏设备

iOS和Android等触摸屏设备的实现大相径庭，因为触摸屏通常不支持鼠标操作。在为触摸屏设备开发时，要记住以下事项。

+ 不支持`dblclick`事件。双击浏览器窗口可以放大，但没有办法覆盖这个行为。
+ 单指点触屏幕上的可点击元素会触发`mousemove`事件。如果操作会导致内容变化，则不会再触发其他事件。如果屏幕上没有变化，则会相继触发`mousedown`、`mouseup`和`click`事件。点触不可点击的元素不会触发事件。可点击元素是指点击时有默认动作的元素（如链接）或指定了`onclick`事件处理程序的元素。
+ `mousemove`事件也会触发`mouseover`和`mouseout`事件。
+ 双指点触屏幕并滑动导致页面滚动时会触发`mousewheel`和`scroll`  事件。


#### 无障碍问题

如果Web应用或网站必须考虑残障人士，特别是使用屏幕阅读器的用户，那么必须小心使用鼠标事件。如前所述，按回车键可以触发`click`事件，但其他鼠标事件不能通过键盘触发。因此，建议不要使用`click`事件之外的其他鼠标事件向用户提示功能或触发代码执行，这是因为其他鼠标事件会严格妨碍盲人或视障用户使用。以下是几条使用鼠标事件时应该遵循的无障碍建议。

+ 使用`click`事件执行代码。有人认为，当使用`onmousedown`执行代码时，应用程序会运行得更快。对视力正常用户来说确实如此。但在屏幕阅读器上，这样会导致代码无法执行，这是因为屏幕阅读器无法触发`mousedown`事件。
+ 不要使用`mouseover`向用户显示新选项。同样，原因是屏幕阅读器无法触发`mousedown`事件。如果必须要通过这种方式显示新选项，那么可以考虑显示相同信息的键盘快捷键。
+ 不要使用`dblclick`执行重要的操作，这是因为键盘不能触发这个事件。

!> 注意　要了解更多关于网站无障碍的信息，可以参考WebAIM网站。


### 键盘与输入事件

+ `keydown`，用户按下键盘上某个键时触发，而且持续按住会重复触发。

+ `keypress`，用户按下键盘上某个键并产生字符时触发，而且持续按住会重复触发。Esc键也会触发这个事件。DOM3 Events废弃了`keypress`事件，而推荐`textInput`事件。

+ `keyup`，用户释放键盘上某个键时触发。

+ `textInput`，这个事件是对`keypress`事件的扩展，用于在文本显示给用户之前更方便地截获文本输入。`textInput`会在文本被插入到文本框之前触发。

当用户按下键盘上的某个`字符键`时，首先会触发`keydown`事件，然后触发`keypress`事件，最后触发`keyup`事件。注意，这里`keydown`和`keypress`事件会在文本框出现变化之前触发，而`keyup`事件会在文本框出现变化之后触发。如果一个字符键被按住不放，`keydown`和`keypress`就会重复触发，直到这个键被释放。

对于`非字符键`，在键盘上按一下这个键，会先触发`keydown`事件，然后触发`keyup`事件。如果按住某个非字符键不放，则会重复触发`keydown`事件，直到这个键被释放，此时会触发`keyup`事件。

!> 注意　键盘事件支持与鼠标事件相同的修饰键。`shiftKey`、`ctrlKey`、`altKey`和`metaKey`属性在键盘事件中都是可用的。IE8及更早版本不支持`metaKey`属性。


#### 键码

对于`keydown`和`keyup`事件，`event`对象的`keyCode`属性中会保存一个键码，对应键盘上特定的一个键。对于字母和数字键，`keyCode`的值与小写字母和数字的ASCII编码一致。比如数字7键的`keyCode`为55，而字母A键的`keyCode`为65，而且跟是否按了Shift键无关


| 键 |	键码 |	键 |	键码 |
| ---- | ---- | ---- | ---- |
| Backspace	| 8	| 数字键盘8	| 104
| Tab	| 9	| 数字键盘9	| 105
| Enter	| 13	| 数字键盘+	| 107
| Shift	| 16	| 减号（包含数字和非数字键盘）	| 109
| Ctrl	| 17	| 数字键盘.	| 110
| Alt	| 18	| 数字键盘/	| 111
| Pause/Break	| 19	| F1	| 112
| Caps Lock	| 20	| F2	| 113
| Esc	| 27	| F3	| 114
| Page Up	| 33	| F4	| 115
| Page Down	| 34	| F5	| 116
| End	| 35	| F6	| 117
| Home	| 36	| F7	| 118
| 左箭头	| 37	| F8	| 119
| 上箭头	| 38	| F9	| 120
| 右箭头	| 39	| F10	| 121
| 下箭头	| 40	| F11	| 122
| Ins	| 45	| F12	| 123
| Del	| 46	| Num Lock	| 144
| 左Windows	| 91	| Scroll Lock	| 145
| 右Windows	| 92	| 分号（IE/Safari/Chrome）	| 186
| Context Menu	| 93	| 分号（Opera/FF）	| 59
| 数字键盘0	| 96	| 小于号	| 188
| 数字键盘1	| 97	| 大于号	| 190
| 数字键盘2	| 98	| 反斜杠	| 191
| 数字键盘3	| 99	| 重音符（``` \` ```）	| 192
| 数字键盘4	| 100	| 等于号	| 61
| 数字键盘5	| 101	| 左中括号	| 219
| 数字键盘6	| 102	| 反斜杠（`\\`）	| 220
| 数字键盘7	| 103	| 右中括号	| 221
|  |  | 单引号	| 222


#### 字符编码

在`keypress`事件发生时，意味着按键会影响屏幕上显示的文本。对插入或移除字符的键，所有浏览器都会触发`keypress`事件，其他键则取决于浏览器。因为DOM3 Events规范才刚刚开始实现，所以不同浏览器之间的实现存在显著差异。

浏览器在event对象上支持`charCode`属性，只有发生`keypress`事件时这个属性才会被设置值，包含的是按键字符对应的ASCII编码。通常，`charCode`属性的值是0，在`keypress`事件发生时则是对应按键的键码。IE8及更早版本和Opera使用`keyCode`传达字符的ASCII编码。
```js
var EventUtil = {
  getCharCode: function(event) {
    if (typeof event.charCode == "number") {
      return event.charCode;
    } else {
      return event.keyCode;
    }
  },
};

let textbox = document.getElementById("myText");
textbox.addEventListener("keypress", (event) => {
  console.log(EventUtil.getCharCode(event));
});
```
一旦有了字母编码，就可以使用`String.fromCharCode()`方法将其转换为实际的字符了。


#### DOM3的变化

尽管所有浏览器都实现了某种形式的键盘事件，DOM3 Events还是做了一些修改。比如，DOM3 Events规范并未规定`charCode`属性，而是定义了`key`和`char`两个新属性。

其中，`key`属性用于替代`keyCode`，且包含字符串。在按下字符键时，`key`的值等于文本字符（如“k”或“M”）；在按下非字符键时，`key`的值是键名（如“Shift”或“ArrowDown”）。`char`属性在按下字符键时与key类似，在按下非字符键时为`null`。

IE支持`key`属性但不支持`char`属性。Safari和Chrome支持`keyIdentifier`属性，在按下非字符键时返回与key一样的值（如“Shift”）。对于字符键，`keyIdentifier`返回以“U+0000”形式表示Unicode值的字符串形式的字符编码。
```js
let textbox = document.getElementById("myText");
textbox.addEventListener("keypress", (event) => {
  let identifier = event.key || event.keyIdentifier;
  if (identifier) {
    console.log(identifier);
  }
});
```

### HTML5事件

#### `contextmenu`事件

通过单击鼠标右键为PC用户增加了上下文菜单

开发者面临的问题是如何确定何时该显示上下文菜单（在Windows上是右击鼠标，在Mac上是Ctrl+单击），以及如何避免默认的上下文菜单起作用(阻止冒泡)。
```html
<!DOCTYPE html>
<html>
<head>
  <title>ContextMenu Event Example</title>
</head>
<body>
  <div id="myDiv">Right click or Ctrl+click me to get a custom context menu.
    Click anywhere else to get the default context menu.</div>
  <ul id="myMenu" style="position:absolute;visibility:hidden;background-color:
    silver">
    <li><a href="http://www.somewhere.com"> somewhere</a></li>
    <li><a href="http://www.wrox.com">Wrox site</a></li>
    <li><a href="http://www.somewhere-else.com">somewhere-else</a></li>
  </ul>
</body>
</html>
```
```js
window.addEventListener("load", (event) => {
  let div = document.getElementById("myDiv");

  div.addEventListener("contextmenu", (event) => {
    event.preventDefault();

    let menu = document.getElementById("myMenu");
    menu.style.left = event.clientX + "px";
    menu.style.top = event.clientY + "px";
    menu.style.visibility = "visible";
  });

  document.addEventListener("click", (event) => {
    document.getElementById("myMenu").style.visibility = "hidden";
  });
});
```

#### `beforeunload`事件

`beforeunload`事件会在`window`上触发，用意是给开发者提供阻止页面被卸载的机会。

#### `DOMContentLoaded`事件

`window`的`load`事件会在页面完全加载后触发，因为要等待很多外部资源加载完成，所以会花费较长时间。

而`DOMContentLoaded`事件会在DOM树构建完成后立即触发，而不用等待图片、JavaScript文件、CSS文件或其他资源加载完成。

相对于`load`事件，`DOMContentLoaded`可以让开发者在外部资源下载的同时就能指定事件处理程序，从而让用户能够更快地与页面交互。

对于不支持`DOMContentLoaded`事件的浏览器，可以使用超时为0的`setTimeout()`函数，通过其回调来设置事件处理程序，比如：
```js
setTimeout(() => {
  // 在这里添加事件处理程序
}, 0);
```
以上代码本质上意味着在当前JavaScript进程执行完毕后立即执行这个回调。页面加载和构建期间，只有一个JavaScript进程运行。所以可以在这个进程空闲后立即执行回调，至于是否与同一个浏览器或同一页面上不同脚本的`DOMContentLoaded`触发时机一致并无绝对把握。为了尽可能早一些执行，以上代码最好是页面上的第一个超时代码。即使如此，考虑到各种影响因素，也不一定保证能在`load`事件之前执行超时回调。


#### `readystatechange`事件

IE首先在DOM文档的一些地方定义了一个名为`readystatechange`事件。这个有点神秘的事件旨在提供文档或元素加载状态的信息，但行为有时候并不稳定。支持`readystatechange`事件的每个对象都有一个`readyState`属性，该属性具有一个以下列出的可能的字符串值。

+ `uninitialized`：对象存在并尚未初始化。
+ `loading`：对象正在加载数据。
+ `loaded`：对象已经加载完数据。
+ `interactive`：对象可以交互，但尚未加载完成。
+ `complete`：对象加载完成。

其实并非所有对象都会经历所有`readystate`阶段。文档中说有些对象会完全跳过某个阶段，但并未说明哪些阶段适用于哪些对象。这意味着`readystatechange`事件经常会触发不到4次，而`readyState`未必会依次呈现上述值。

!> 注意　使用`readystatechange`只能尽量模拟`DOMContentLoaded`，但做不到分毫不差。`load`事件和`readystatechange`事件发生的顺序在不同页面中是不一样的。


#### `pageshow`与`pagehide`事件

Firefox和Opera开发了一个名为**往返缓存**（bfcache，back-forward cache）的功能，此功能旨在使用浏览器“前进”和“后退”按钮时加快页面之间的切换。这个缓存不仅存储页面数据，也存储DOM和JavaScript状态，实际上是把整个页面都保存在内存里。如果页面在缓存中，那么导航到这个页面时就不会触发`load`事件。通常，这不会导致什么问题，因为整个页面状态都被保存起来了。不过，Firefx决定提供一些事件，把往返缓存的行为暴露出来。

`pageshow`事件，其会在页面显示时触发，无论是否来自往返缓存。在新加载的页面上，`pageshow`会在`load`事件之后触发；在来自往返缓存的页面上，`pageshow`会在页面状态完全恢复后触发。注意，虽然这个事件的目标是`document`，但事件处理程序必须添加到`window`上。
```js
(function() {
  let showCount = 0;

  window.addEventListener("load", () => {
    console.log("Load fired");
  });

  window.addEventListener("pageshow", () => {
    showCount++;
    console.log(`Show has been fired ${showCount} times.`);
  });
})();
```
这个例子使用了私有作用域来保证`showCount`变量不进入全局作用域。在页面首次加载时，`showCount`的值为0。之后每次触发`pageshow`事件，`showCount`都会加1并输出消息。如果从包含以上代码的页面跳走，然后又点击“后退”按钮返回以恢复它，就能够每次都看到`showCount`递增的值。这是因为变量的状态连同整个页面状态都保存在了内存中，导航回来后可以恢复。如果是点击了浏览器的“刷新”按钮，则`showCount`的值会重置为0，因为页面会重新加载。

`pageshow`的`event`对象中还包含一个名为`persisted`的属性。这个属性是一个布尔值，如果页面存储在了往返缓存中就是`true`，否则就是`false`。

`pagehide`事件，这个事件会在页面从浏览器中卸载后，在`unload`事件之前触发。与`pageshow`事件一样，`pagehide`事件同样是在`document`上触发，但事件处理程序必须被添加到`window`。`event`对象中同样包含`persisted`属性，但用法稍有不同。
```js
window.addEventListener("pagehide", (event) => {
  console.log("Hiding. Persisted? " + event.persisted);
});
```
对`pagehide`事件来说，`persisted`为`true`表示页面在卸载之后会被保存在往返缓存中。第一次触发`pageshow`事件时`persisted`始终是`false`，而第一次触发`pagehide`事件时`persisted`始终是`true`（除非页面不符合使用往返缓存的条件）。

!> 注意　注册了`onunload`事件处理程序（即使是空函数）的页面会自动排除在往返缓存之外。这是因为`onunload`事件典型的使用场景是撤销`onload`事件发生时所做的事情，如果使用往返缓存，则下一次页面显示时就不会触发`onload`事件，而这可能导致页面无法使用。

#### `hashchange`事件

HTML5增加了`hashchange`事件，用于在URL散列值（URL最后`#`后面的部分）发生变化时通知开发者。这是因为开发者经常在Ajax应用程序中使用URL散列值存储状态信息或路由导航信息。

`onhashchange`事件处理程序必须添加给`window`，每次URL散列值发生变化时会调用它。`event`对象有两个新属性：`oldURL`和`newURL`。这两个属性分别保存变化前后的URL，而且是包含散列值的完整URL。
```js
window.addEventListener("hashchange", (event) => {
  console.log(`Old URL: ${event.oldURL}, New URL: ${event.newURL}`);
});
```

如果想确定当前的散列值，最好使用`location`对象：
```js
window.addEventListener("hashchange", (event) => {
  console.log(`Current hash: ${location.hash}`);
});
```

### 设备事件

#### `orientationchange`事件

苹果公司在移动Safari浏览器上创造了`orientationchange`事件，以方便开发者判断用户的设备是处于垂直模式还是水平模式。移动Safari在`window`上暴露了`window.orientation`属性，它有以下3种值之一：0表示垂直模式，90表示左转水平模式（主屏幕键在右侧），–90表示右转水平模式（主屏幕键在左）。虽然相关文档也提及设备倒置后的值为180，但设备本身至今还不支持。

![](../assets/image/javascript/event__事件类型__设备事件01.png ':size=40%')

```js
window.addEventListener("load", (event) => {
  let div = document.getElementById("myDiv");
  div.innerHTML = "Current orientation is " + window.orientation;

  window.addEventListener("orientationchange", (event) => {
    div.innerHTML = "Current orientation is " + window.orientation;
  });
});
```

#### `deviceorientation`事件

`deviceorientation`是DeviceOrientationEvent规范定义的事件。如果可以获取设备的加速计信息，而且数据发生了变化，这个事件就会在`window`上触发。要注意的是，`deviceorientation`事件只反映设备在空间中的朝向，而不涉及移动相关的信息。

设备本身处于3D空间即拥有x轴、y轴和z轴的坐标系中。如果把设备静止放在水平的表面上，那么三轴的值均为0，其中，x轴方向为从设备左侧到右侧，y轴方向为从设备底部到上部，z轴方向为从设备背面到正面。

![](../assets/image/javascript/event__事件类型__设备事件02.png ':size=40%')

当`deviceorientation`触发时，`event`对象中会包含各个轴相对于设备静置时坐标值的变化，主要是以下5个属性。
+ `alpha`：0~360范围内的浮点值，表示围绕z轴旋转时y轴的度数（左右转）。
+ `beta`：–180~180范围内的浮点值，表示围绕x轴旋转时z轴的度数（前后转）。
+ `gamma`：–90~90范围内的浮点值，表示围绕y轴旋转时z轴的度数（扭转）。
+ `absolute`：布尔值，表示设备是否返回绝对值。
+ `compassCalibrated`：布尔值，表示设备的指南针是否正确校准。

alpha、beta和gamma值的计算方式:

![](../assets/image/javascript/event__事件类型__设备事件03.png ':size=60%')

```js
window.addEventListener("deviceorientation", (event) => {
  let output = document.getElementById("output");
  output.innerHTML =
    `Alpha=${event.alpha}, Beta=${event.beta}, Gamma=${event.gamma}<br>`;
});
```

基于这些信息，可以随着设备朝向的变化重新组织或修改屏幕上显示的元素。 *只适用于移动WebKit浏览器，因为使用的是专有的`webkitTransform`属性（CSS标准的`transform`属性的临时版本）*
```js
window.addEventListener("deviceorientation", (event) => {
  let arrow = document.getElementById("arrow");
  arrow.style.webkitTransform = `rotate(${Math.round(event.alpha)}deg)`;
});
```

#### `devicemotion`事件

DeviceOrientationEvent规范也定义了`devicemotion`事件。这个事件用于提示设备实际上在移动，而不仅仅是改变了朝向。

例如，`devicemotion`事件可以用来确定设备正在掉落或者正拿在一个行走的人手里。

当`devicemotion`事件触发时，`event`对象中包含如下额外的属性。
+ `acceleration`：对象，包含`x`、`y`和`z`属性，反映不考虑重力情况下各个维度的加速信息。
+ `accelerationIncludingGravity`：对象，包含`x`、`y`和`z`属性，反映各个维度的加速信息，包含z轴自然重力加速度。
+ `interval`：毫秒，距离下次触发`devicemotion`事件的时间。此值在事件之间应为常量。
+ `rotationRate`：对象，包含`alpha`、`beta`和`gamma`属性，表示设备朝向。

如果无法提供`acceleration`、`accelerationIncludingGravity`和`rotationRate`信息，则属性值为`null`。为此，在使用这些属性前必须先检测它们的值是否为`null`。
```js
window.addEventListener("devicemotion", (event) => {
  let output = document.getElementById("output");
  if (event.rotationRate !== null) {
    output.innerHTML += `Alpha=${event.rotationRate.alpha}` +
                        `Beta=${event.rotationRate.beta}` +
                        `Gamma=${event.rotationRate.gamma}`;
  }
});
```

### 触摸及手势事件

Safari为iOS定制了一些专有事件，以方便开发者。因为iOS设备没有鼠标和键盘，所以常规的鼠标和键盘事件不足以创建具有完整交互能力的网页。同时，WebKit也为Android定制了很多专有事件，成为了事实标准，并被纳入W3C的Touch Events规范。

#### 触摸事件

手指放在屏幕上、在屏幕上滑动或从屏幕移开时，**触摸事件**即会触发。

+ `touchstart`：手指放到屏幕上时触发（即使有一个手指已经放在了屏幕上）。
+ `touchmove`：手指在屏幕上滑动时连续触发。在这个事件中调用`preventDefault()`可以阻止滚动。
+ `touchend`：手指从屏幕上移开时触发。
+ `touchcancel`：系统停止跟踪触摸时触发。文档中并未明确什么情况下停止跟踪。

这些事件都会冒泡，也都可以被取消。尽管触摸事件不属于DOM规范，但浏览器仍然以兼容DOM的方式实现了它们。因此，每个触摸事件的`event`对象都提供了鼠标事件的公共属性：`bubbles`、`cancelable`、`view`、`clientX`、`clientY`、`screenX`、`screenY`、`detail`、`altKey`、`shiftKey`、`ctrlKey`和`metaKey`。

除了这些公共的DOM属性，触摸事件还提供了以下3个属性用于跟踪触点。
+ `touches`：`Touch`对象的数组，表示当前屏幕上的每个触点。
+ `targetTouches`：`Touch`对象的数组，表示特定于事件目标的触点。
+ `changedTouches`：`Touch`对象的数组，表示自上次用户动作之后变化的触点。

每个`Touch`对象都包含下列属性。
+ `clientX`：触点在视口中的x坐标。
+ `clientY`：触点在视口中的y坐标。
+ `identifier`：触点ID。
+ `pageX`：触点在页面上的x坐标。
+ `pageY`：触点在页面上的y坐标。
+ `screenX`：触点在屏幕上的x坐标。
+ `screenY`：触点在屏幕上的y坐标。
+ `target`：触摸事件的事件目标。

这些属性可用于追踪屏幕上的触摸轨迹。例如：
```js
function handleTouchEvent(event) {
  // 只针对一个触点
  if (event.touches.length == 1) {
    let output = document.getElementById("output");
    switch(event.type) {
      case "touchstart":
        output.innerHTML += `<br>Touch started:` +
                            `(${event.touches[0].clientX}` +
                            ` ${event.touches[0].clientY})`;
        break;
      case "touchend":
        output.innerHTML += `<br>Touch ended:` +
                            `(${event.changedTouches[0].clientX}` +
                            ` ${event.changedTouches[0].clientY})`;
        break;
      case "touchmove":
        event.preventDefault(); // 阻止滚动
        output.innerHTML += `<br>Touch moved:` +
                            `(${event.changedTouches[0].clientX}` +
                            ` ${event.changedTouches[0].clientY})`;
        break;
    }
  }
}

document.addEventListener("touchstart", handleTouchEvent);
document.addEventListener("touchend", handleTouchEvent);
document.addEventListener("touchmove", handleTouchEvent);
```
注意，`touchend`事件触发时`touches`集合中什么也没有，这是因为没有滚动的触点了。此时必须使用`changedTouches`集合。

这些事件会在文档的所有元素上触发，因此可以分别控制页面的不同部分。当手指点触屏幕上的元素时，依次会发生如下事件（包括鼠标事件）：

(1) `touchstart` > (2) `mouseover` > (3) `mousemove`（1次） > (4) `mousedown` > (5) `mouseup` > (6) `click` > (7) `touchend`


#### 手势事件

iOS 2.0中的Safari还增加了一种手势事件。手势事件会在两个手指触碰屏幕且相对距离或旋转角度变化时触发。手势事件有以下3种。
+ `gesturestart`：一个手指已经放在屏幕上，再把另一个手指放到屏幕上时触发。
+ `gesturechange`：任何一个手指在屏幕上的位置发生变化时触发。
+ `gestureend`：其中一个手指离开屏幕时触发。

只有在两个手指同时接触事件接收者时，这些事件才会触发。在一个元素上设置事件处理程序，意味着两个手指必须都在元素边界以内才能触发手势事件（这个元素就是事件目标）。因为这些事件会冒泡，所以也可以把事件处理程序放到文档级别，从而可以处理所有手势事件。使用这种方式时，事件的目标就是两个手指均位于其边界内的元素。

?> 触摸事件和手势事件存在一定的关系。当一个手指放在屏幕上时，会触发`touchstart`事件。当另一个手指放到屏幕上时，`gesturestart`事件会首先触发，然后紧接着触发这个手指的`touchstart`事件。如果两个手指或其中一个手指移动，则会触发`gesturechange`事件。只要其中一个手指离开屏幕，就会触发`gestureend`事件，紧接着触发该手指的`touchend`事件。

与触摸事件类似，每个手势事件的`event`对象都包含所有标准的鼠标事件属性：`bubbles`、`cancelable`、`view`、`clientX`、`clientY`、`screenX`、`screenY`、`detail`、`altKey`、`shiftKey`、`ctrlKey`和`metaKey`。新增的两个`event`对象属性是`rotation`和`scale`。

`rotation`属性表示手指变化旋转的度数，负值表示逆时针旋转，正值表示顺时针旋转（从0开始）。`scale`属性表示两指之间距离变化（对捏）的程度。开始时为1，然后随着距离增大或缩小相应地增大或缩小。
```js
function handleGestureEvent(event) {
  let output = document.getElementById("output");
  switch(event.type) {
    case "gesturestart":
      output.innerHTML += `Gesture started: ` +
                          `rotation=${event.rotation},` +
                          `scale=${event.scale}`;
      break;
    case "gestureend":
      output.innerHTML += `Gesture ended: ` +
                          `rotation=${event.rotation},` +
                          `scale=${event.scale}`;
      break;
    case "gesturechange":
      output.innerHTML += `Gesture changed: ` +
                          `rotation=${event.rotation},` +
                          `scale=${event.scale}`;
      break;
  }
}

document.addEventListener("gesturestart", handleGestureEvent, false);
document.addEventListener("gestureend", handleGestureEvent, false);
document.addEventListener("gesturechange", handleGestureEvent, false);
```

!> 注意　触摸事件也会返回`rotation`和`scale`属性，但只在两个手指触碰屏幕时才会变化。一般来说，使用两个手指的手势事件比考虑所有交互的触摸事件使用起来更容易一些。

### 事件参考

DOM规范、HTML5规范，以及概述事件行为的其他当前已发布规范中定义的所有浏览器事件。这些事件按照API和/或规范分类。*不包含带厂商前缀事件的规范*
```js
Ambient Light events
devicelight

App Cache events
cached
checking
downloading
noupdate
obsolete
updateready

Audio Channels API events
headphoneschange
mozinterruptbegin
mozinterruptend

Battery API events
chargingchange
chargingtimechange
dischargingtimechange
levelchange

Broadcast Channel API events
message

Channel Messaging API events
message

Clipboard API events
beforecopy
beforecut
beforepaste
copy
cut
paste

Contacts API events
contactchange
error
success

CSS Font Loading API events
loading
loadingdone
loadingerror

CSSOM events
animationend
animationiteration
animationstart
transitionend

CSSOM View events
resize
scroll

Device Orientation events
compassneedscalibration
devicemotion
deviceorientation

Device Storage API events
change

DOM events
abort
beforeinput
blur
click
compositionend
compositionstart
compositionupdate
dblclick
error
focus
focusin
focusout
input
keydown
keypress
keyup
load
mousedown
mouseenter
mouseleave
mousemove
mouseout
mouseover
mouseup
resize
scroll
select
unload
wheel

Download API events
statechange

Encrypted Media Extensions events
encrypted
keystatuschange
message
waitingforkey

Engineering Mode API events
message

File API events
abort
error
load
loadend
loadstart
progress

File System API events
error
writeend

FMRadio API events
antennaavailablechange
disabled
enabled
frequencychange

Fullscreen API events
fullscreenchange
fullscreenerror

Gamepad API events
gamepadconnected
gamepaddisconnected

HTML DOM events
DOMContentLoaded
abort
afterprint
afterscriptexecute
beforeprint
beforescriptexecute
beforeunload
blur
cancel
canplay
canplaythrough
change
click
close
connect
contextmenu
durationchange
emptied
error
focus
hashchange
input
invalid
languagechange
load
loadeddata
loadedmetadata
loadend
loadstart
message
offline
online
open

pagehide
pageshow
play
playing
popstate
progress
readystatechange
rejectionhandled
reset
seeked
seeking
select
show
sort
stalled
storage
submit
suspend
timeupdate
toggle
unhandledrejection
unload
volumechange
waiting

HTML Drag and Drop API events
drag
dragend
dragenter
dragexit
dragleave
dragover
dragstart
drop

IndexedDB events
abort
blocked
close
complete
error
success
upgradeneeded
versionchange

Inter-App Connection API events
message

Media Capture and Streams events
active
addtrack
devicechange
ended
inactive
mute
overconstrained
ratechange
removetrack
started
unmute

Media Source Extensions events
abort
addsourcebuffer
error
removesourcebuffer
sourceclose
sourceended
sourceopen
update
updateend
updatestart

MediaStream Recording events
dataavailable
error
pause
resume
start
stop

Mobile Connection API events
cardstatechange
icccardlockerror

Mobile Messaging API events
close
deliveryerror
deliverysuccess
error
failed
message
open
received
retrieving
sending
sent

Network Information API events
change

Page Visibility API events
visibilitychange

Payment Request API events
shippingaddresschange
shippingoptionchange

Performance API events
resourcetimingbufferfull

Pointer events
gotpointercapture
lostpointercapture
pointercancel
pointerdown
pointerenter
pointerleave
pointermove
pointerout
pointerover
pointerup

Pointer Lock API events
pointerlockchange
pointerlockerror

Presentation API events
change
sessionavailable
sessionconnect

Proximity events
deviceproximity
userproximity

Push API events
push
pushsubscriptionchange

Screen Orientation API events
change

Selection API events
selectionchange
selectstart

Server Sent events
error
message
open

Service Workers API events
activate
controllerchange
error
fetch
install
message
statechange
updatefound

Settings API events
settingchange

Simple Push API events
error
success

Speaker Manager API events
speakerforcedchange

SVG events
DOMAttrModified
DOMCharacterDataModified
DOMNodeInserted
DOMNodeInsertedIntoDocument
DOMNodeRemoved
DOMNodeRemovedFromDocument
DOMSubtreeModified
SVGAbort
SVGError
SVGLoad
SVGResize
SVGScroll
SVGUnload
SVGZoom
activate
beginEvent
click
endEvent
focusin
focusout
mousedown
mousemove
mouseout
mouseover
mouseup
repeatEvent

TCP Socket API events
connect
data
drain
error

Time and Clock API events
moztimechange

Touch events
touchcancel
touchend
touchmove
touchstart

TV API events
currentchannelchanged
currentsourcechanged
eitbroadcasted
scanningstatechanged

UDP Socket API events
message

Web Audio API events
audioprocess
complete
ended
loaded
message
nodecreate
statechange

Web Components events
slotchange

WebGL events
webglcontextcreationerror
webglcontextlost
webglcontextrestored

Web Manifest events
install

Web MIDI API events
midimessage
statechange

Web Notifications events
click
close
error
show

WebRTC events
addstream
close
datachannel
error
icecandidate
iceconnectionstatechange
icegatheringstatechange
identityresult
idpassertionerror
idpvalidationerror
isolationchange
message
negotiationneeded
open
peeridentity
removestream
signalingstatechange
tonechange
Websockets API events
close
error
message
open

Web Speech API events
audioend
audiostart
boundary
end_(SpeechRecognition)
end_(SpeechSynthesis)
error_(SpeechRecognitionError)
error_(SpeechSynthesis)
mark
nomatch
pause_(SpeechSynthesis)
result
resume
soundend
soundstart
speechend
speechstart
start_(SpeechRecognition)
start_(SpeechSynthesis)

Web Storage API events
storage

Web Telephony API events
incoming

WebVR API events
vrdisplayactivate
vrdisplayblur
vrdisplayconnected
vrdisplaydeactivate
vrdisplaydisconnected
vrdisplayfocus
vrdisplaypresentchange

WebVTT events
addtrack
change
cuechange
enter
exit
removetrack

WiFi Information API events
connectioninfoupdate
statuschange

WiFi P2P API events
disabled
enabled
peerinfoupdate
statuschange

XMLHttpRequest events
abort
error
load
loadend
loadstart
progress
readystatechange
timeout
```


## 内存与性能

因为事件处理程序在现代Web应用中可以实现交互，所以很多开发者会错误地在页面中大量使用它们。在创建GUI的语言如C#中，通常会给GUI上的每个按钮设置一个`onclick`事件处理程序。这样做不会有什么性能损耗。在JavaScript中，页面中事件处理程序的数量与页面整体性能直接相关。原因有很多。首先，每个函数都是对象，都占用内存空间，对象越多，性能越差。其次，为指定事件处理程序所需访问DOM的次数会先期造成整个页面交互的延迟。


### 事件委托

“过多事件处理程序”的解决方案是使用事件委托。事件委托利用事件冒泡，可以只使用一个事件处理程序来管理一种类型的事件。例如，`click`事件冒泡到`document`。这意味着可以为整个页面指定一个`onclick`事件处理程序，而不用为每个可点击元素分别指定事件处理程序。

```html
<ul id="myLinks">
  <li id="goSomewhere">Go somewhere</li>
  <li id="doSomething">Do something</li>
  <li id="sayHi">Say hi</li>
</ul>
```

通常的做法:
```js
let item1 = document.getElementById("goSomewhere");
let item2 = document.getElementById("doSomething");
let item3 = document.getElementById("sayHi");

item1.addEventListener("click", (event) => {
  location.href = "http:// www.wrox.com";
});

item2.addEventListener("click", (event) => {
  document.title = "I changed the document's title";
});

item3.addEventListener("click", (event) => {
  console.log("hi");
});
```

事件委托:
```js
list.addEventListener("click", (event) => {
  let target = event.target;

  switch(target.id) {
    case "doSomething":
      document.title = "I changed the document's title";
      break;

    case "goSomewhere":
      location.href = "http:// www.wrox.com";
      break;

    case "sayHi":
      console.log("hi");
      break;
  }
});
```

事件委托具有如下优点:
+ `document`对象随时可用，任何时候都可以给它添加事件处理程序（不用等待`DOMContentLoaded`或`load`事件）。这意味着只要页面渲染出可点击的元素，就可以无延迟地起作用。
+ 节省花在设置页面事件处理程序上的时间。只指定一个事件处理程序既可以节省DOM引用，也可以节省时间。
+ 减少整个页面所需的内存，提升整体性能。

最适合使用事件委托的事件包括：`click`、`mousedown`、`mouseup`、`keydown`和`keypress`。`mouseover`和`mouseout`事件冒泡，但很难适当处理，且经常需要计算元素位置（因为`mouseout`会在光标从一个元素移动到它的一个后代节点以及移出元素之外时触发）。

### 删除事件处理程序

把事件处理程序指定给元素后，在浏览器代码和负责页面交互的JavaScript代码之间就建立了联系。这种联系建立得越多，页面性能就越差。除了通过事件委托来限制这种连接之外，还应该及时删除不用的事件处理程序。很多Web应用性能不佳都是由于无用的事件处理程序长驻内存导致的。

导致这个问题的原因主要有两个。

**第一个是删除带有事件处理程序的元素**。比如通过真正的DOM方法`removeChild()`或`replaceChild()`删除节点。最常见的还是使用`innerHTML`整体替换页面的某一部分。这时候，被`innerHTML`删除的元素上如果有事件处理程序，就不会被垃圾收集程序正常清理。
```html
<div id="myDiv">
  <input type="button" value="Click Me" id="myBtn">
</div>
<script type="text/javascript">
  let btn = document.getElementById("myBtn");
  btn.onclick = function() {

    // 执行操作

    document.getElementById("myDiv").innerHTML = "Processing...";
    // 不好！
  };
</script>
```

这里的按钮在`<div>`元素中。单击按钮，会将自己删除并替换为一条消息，以阻止双击发生。这是很多网站上常见的做法。问题在于，按钮被删除之后仍然关联着一个事件处理程序。在`<div>`元素上设置`innerHTML`会完全删除按钮，但事件处理程序仍然挂在按钮上面。某些浏览器，特别是IE8及更早版本，在这时候就会有问题了。很有可能元素的引用和事件处理程序的引用都会残留在内存中。如果知道某个元素会被删除，那么最好在删除它之前手工删除它的事件处理程序，比如：
```html
<div id="myDiv">
  <input type="button" value="Click Me" id="myBtn">
</div>
<script type="text/javascript">
  let btn = document.getElementById("myBtn");
  btn.onclick = function() {

    // 执行操作

    btn.onclick = null;   // 删除事件处理程序

    document.getElementById("myDiv").innerHTML = "Processing...";
  };
</script>
```
在事件处理程序中删除按钮会阻止事件冒泡。只有事件目标仍然存在于文档中时，事件才会冒泡。

!> 注意　事件委托也有助于解决这种问题。如果提前知道页面某一部分会被使用`innerHTML`删除，就不要直接给该部分中的元素添加事件处理程序了。把事件处理程序添加到更高层级的节点上同样可以处理该区域的事件。

**另一个可能导致内存中残留引用的问题是页面卸载。**同样，IE8及更早版本在这种情况下有很多问题，不过好像所有浏览器都会受这个问题影响。如果在页面卸载后事件处理程序没有被清理，则它们仍然会残留在内存中。之后，浏览器每次加载和卸载页面（比如通过前进、后退或刷新），内存中残留对象的数量都会增加，这是因为事件处理程序不会被回收。

一般来说，最好在`onunload`事件处理程序中趁页面尚未卸载先删除所有事件处理程序。这时候也能体现使用事件委托的优势，因为事件处理程序很少，所以很容易记住要删除哪些。关于卸载页面时的清理，可以记住一点：`onload`事件处理程序中做了什么，最好在`onunload`事件处理程序中恢复。

!> 注意　在页面中使用`onunload`事件处理程序意味着页面不会被保存在往返缓存（bfcache）中。如果这对应用很重要，可以考虑只在IE中使用`onunload`来删除事件处理程序。


## 模拟事件(略懂)

事件就是为了表示网页中某个有意义的时刻。通常，事件都是由用户交互或浏览器功能触发。事实上，可能很少有人知道可以通过JavaScript在任何时候触发任意事件，而这些事件会被当成浏览器创建的事件。这意味着同样会有事件冒泡，因而也会触发相应的事件处理程序。这种能力在测试Web应用时特别有用。DOM3规范指明了模拟特定类型事件的方式。IE8及更早版本也有自己模拟事件的方式。

### DOM事件模拟

任何时候，都可以使用`document.createEvent()`方法创建一个`event`对象。这个方法接收一个参数，此参数是一个表示要创建事件类型的字符串。在DOM2中，所有这些字符串都是英文复数形式，但在DOM3中，又把它们改成了英文单数形式。可用的字符串值是以下值之一。

+ `"UIEvents"`（DOM3中是`"UIEvent"`）：通用用户界面事件（鼠标事件和键盘事件都继承自这个事件）。
+ `"MouseEvents"`（DOM3中是`"MouseEvent"`）：通用鼠标事件。
+ `"HTMLEvents"`（DOM3中没有）：通用HTML事件（HTML事件已经分散到了其他事件大类中）。

注意，键盘事件不是在DOM2 Events中规定的，而是后来在DOM3 Events中增加的。

创建`event`对象之后，需要使用事件相关的信息来初始化。每种类型的`event`对象都有特定的方法，可以使用相应数据来完成初始化。方法的名字并不相同，这取决于调用`createEvent()`时传入的参数。

事件模拟的最后一步是触发事件。为此要使用`dispatchEvent()`方法，这个方法存在于所有支持事件的DOM节点之上。`dispatchEvent()`方法接收一个参数，即表示要触发事件的`event`对象。调用`dispatchEvent()`方法之后，事件就“转正”了，接着便冒泡并触发事件处理程序执行。

#### 模拟鼠标事件

模拟鼠标事件需要先创建一个新的鼠标`event`对象，然后再使用必要的信息对其进行初始化。要创建鼠标`event`对象，可以调用`createEvent()`方法并传入`"MouseEvents"`参数。这样就会返回一个`event`对象，这个对象有一个`initMouseEvent()`方法，用于为新对象指定鼠标的特定信息。`initMouseEvent()`方法接收15个参数，分别对应鼠标事件会暴露的属性。这些参数列举如下。

+ `type`（字符串）：要触发的事件类型，如`"click"`。
+ `bubbles`（布尔值）：表示事件是否冒泡。为精确模拟鼠标事件，应该设置为`true`。
+ `cancelable`（布尔值）：表示事件是否可以取消。为精确模拟鼠标事件，应该设置为`true`。
+ `view`（AbstractView）：与事件关联的视图。基本上始终是`document.defaultView`。
+ `detail`（整数）：关于事件的额外信息。只被事件处理程序使用，通常为0。
+ `screenX`（整数）：事件相对于屏幕的x坐标。
+ `screenY`（整数）：事件相对于屏幕的y坐标。
+ `clientX`（整数）：事件相对于视口的x坐标。
+ `clientY`（整数）：事件相对于视口的y坐标。
+ `ctrlkey`（布尔值）：表示是否按下了Ctrl键。默认为`false`。
+ `altkey`（布尔值）：表示是否按下了Alt键。默认为`false`。
+ `shiftkey`（布尔值）：表示是否按下了Shift键。默认为`false`。
+ `metakey`（布尔值）：表示是否按下了Meta键。默认为`false`。
+ `button`（整数）：表示按下了哪个按钮。默认为0。
+ `relatedTarget`（对象）：与事件相关的对象。只在模拟`mouseover`和`mouseout`时使用。

`initMouseEvent()`方法的这些参数与鼠标事件的event对象属性是一一对应的。前4个参数是正确模拟事件唯一重要的几个参数，这是因为它们是浏览器要用的，其他参数则是事件处理程序要用的。`event`对象的`target`属性会自动设置为调用`dispatchEvent()`方法时传入的节点。
```js
let btn = document.getElementById("myBtn");

// 创建event对象
let event = document.createEvent("MouseEvents");

// 初始化event对象
event.initMouseEvent("click", true, true, document.defaultView,
                     0, 0, 0, 0, 0, false, false, false, false, 0, null);

// 触发事件
btn.dispatchEvent(event);
```


#### 模拟键盘事件

DOM2 Events中没有定义键盘事件，因此模拟键盘事件并不直观。键盘事件曾在DOM2 Events的草案中提到过，但最终成为推荐标准前又被删掉了。要注意的是，DOM3 Events中定义的键盘事件与DOM2 Events草案最初定义的键盘事件差别很大。

在DOM3中创建键盘事件的方式是给`createEvent()`方法传入参数`"KeyboardEvent"`。这样会返回一个`event`对象，这个对象有一个`initKeyboardEvent()`方法。这个方法接收以下参数。

+ `type`（字符串）：要触发的事件类型，如`"keydown"`。
+ `bubbles`（布尔值）：表示事件是否冒泡。为精确模拟键盘事件，应该设置为`true`。
+ `cancelable`（布尔值）：表示事件是否可以取消。为精确模拟键盘事件，应该设置为`true`。
+ `view`（AbstractView）：与事件关联的视图。基本上始终是`document.defaultView`。
+ `key`（字符串）：按下按键的字符串代码。
+ `location`（整数）：按下按键的位置。0表示默认键，1表示左边，2表示右边，3表示数字键盘，4表示移动设备（虚拟键盘），5表示游戏手柄。
+ `modifiers`（字符串）：空格分隔的修饰键列表，如`"Shift"`。
+ `repeat`（整数）：连续按了这个键多少次。

注意，DOM3 Events废弃了keypress事件，因此只能通过上述方式模拟keydown和keyup事件：
```js
let textbox = document.getElementById("myTextbox"),
    event;

// 按照DOM3的方式创建event对象
if (document.implementation.hasFeature("KeyboardEvents", "3.0")) {
   event = document.createEvent("KeyboardEvent");

   // 初始化event对象
   event.initKeyboardEvent("keydown", true, true, document.defaultView, "a",
                           0, "Shift", 0);

}
// 触发事件
textbox.dispatchEvent(event
```
这个例子模拟了同时按住Shift键和键盘上A键的`keydown`事件。在使用`document.create Event("KeyboardEvent")`之前，最好检测一下浏览器对DOM3键盘事件的支持情况，其他浏览器会返回非标准的`KeyboardEvent`对象。

Firefox允许给`createEvent()`传入`"KeyEvents"`来创建键盘事件。这时候返回的`event`对象包含的方法叫`initKeyEvent()`，此方法接收以下10个参数。

+ `type`（字符串）：要触发的事件类型，如`"keydown"`。
+ `bubbles`（布尔值）：表示事件是否冒泡。为精确模拟键盘事件，应该设置为`true`。
+ `cancelable`（布尔值）：表示事件是否可以取消。为精确模拟键盘事件，应该设置为`true`。
+ `view`（AbstractView）：与事件关联的视图，基本上始终是`document.defaultView`。
+ `ctrlkey`（布尔值）：表示是否按下了Ctrl键。默认为`false`。
+ `altkey`（布尔值）：表示是否按下了Alt键。默认为`false`。
+ `shiftkey`（布尔值）：表示是否按下了Shift键。默认为`false`。
+ `metakey`（布尔值）：表示是否按下了Meta键。默认为`false`。
+ `keyCode`（整数）：表示按下或释放键的键码。在`keydown`和`keyup`中使用。默认为0。
+ `charCode`（整数）：表示按下键对应字符的ASCII编码。在`keypress`中使用。默认为0。

键盘事件也可以通过调用`dispatchEvent()`并传入`event`对象来触发：
```js
// 仅适用于Firefox
let textbox = document.getElementById("myTextbox");

// 创建event对象
let event = document.createEvent("KeyEvents");

// 初始化event对象
event.initKeyEvent("keydown", true, true, document.defaultView, false,
                   false, true, false, 65, 65);

// 触发事件
textbox.dispatchEvent(event);
```
这个例子模拟了同时按住Shift键和键盘上A键的keydown事件。同样也可以像这样模拟keyup和keypress事件。


对于其他浏览器，需要创建一个通用的事件，并为其指定特定于键盘的信息：
```js
let textbox = document.getElementById("myTextbox");

// 创建event对象
let event = document.createEvent("Events");

// 初始化event对象
event.initEvent(type, bubbles, cancelable);
event.view = document.defaultView;
event.altKey = false;
event.ctrlKey = false;
event.shiftKey = false;
event.metaKey = false;
event.keyCode = 65;
event.charCode = 65;

// 触发事件
textbox.dispatchEvent(event);
```
以上代码创建了一个通用事件，然后使用`initEvent()`方法初始化，接着又为它指定了键盘事件信息。这里必须使用通用事件而不是用户界面事件，因为用户界面事件不允许直接给`event`对象添加属性（Safari例外）。像这样模拟一个事件虽然会触发键盘事件，但文本框中不会输入任何文本，因为它并不能准确模拟键盘事件。


#### 模拟其他事件

鼠标事件和键盘事件是浏览器中最常见的模拟对象。不过，有时候可能也需要模拟HTML事件。模拟HTML事件要调用`createEvent()`方法并传入`"HTMLEvents"`，然后再使用返回对象的`initEvent()`方法来初始化：
```js
let event = document.createEvent("HTMLEvents");
event.initEvent("focus", true, false);
target.dispatchEvent(event);
```
这个例子模拟了在给定目标上触发focus事件。其他HTML事件也可以像这样来模拟。

!> 注意　HTML事件在浏览器中很少使用，因为它们用处有限。


#### 自定义DOM事件

DOM3增加了**自定义事件**的类型。自定义事件不会触发原生DOM事件，但可以让开发者定义自己的事件。要创建自定义事件，需要调用`createEvent("CustomEvent")`。返回的对象包含`initCustomEvent()`方法，该方法接收以下4个参数。

+ `type`（字符串）：要触发的事件类型，如`"myevent"`。
+ `bubbles`（布尔值）：表示事件是否冒泡。
+ `cancelable`（布尔值）：表示事件是否可以取消。
+ `detail`（对象）：任意值。作为`event`对象的`detail`属性。

自定义事件可以像其他事件一样在DOM中派发，比如：
```js
let div = document.getElementById("myDiv"),
    event;

div.addEventListener("myevent", (event) => {
  console.log("DIV: " + event.detail);
});

document.addEventListener("myevent", (event) => {
  console.log("DOCUMENT: " + event.detail);
});

if (document.implementation.hasFeature("CustomEvents", "3.0")) {
  event = document.createEvent("CustomEvent");
  event.initCustomEvent("myevent", true, false, "Hello world!");
  div.dispatchEvent(event);
}
```
这个例子创建了一个名为`"myevent"`的冒泡事件。`event`对象的`detail`属性就是一个简单的字符串，`<div>`元素和`document`都为这个事件注册了事件处理程序。因为使用`initCustomEvent()`初始化时将事件指定为可以冒泡，所以浏览器会负责把事件冒泡到`document`。

### IE事件模拟

在IE8及更早版本中模拟事件的过程与DOM方式类似：创建`event`对象，指定相应信息，然后使用这个对象触发。IE实现每一步的方式都不一样。

首先，要使用`document`对象的`createEventObject()`方法来创建`event`对象。与DOM不同，这个方法不接收参数，返回一个通用`event`对象。

然后，可以手工给返回的对象指定希望该对象具备的所有属性。（没有初始化方法。）

最后一步是在事件目标上调用`fireEvent()`方法，这个方法接收两个参数：`事件处理程序的名字`和`event对象`。调用`fireEvent()`时，`srcElement`和`type`属性会自动指派到`event`对象（其他所有属性必须手工指定）。这意味着IE支持的所有事件都可以通过相同的方式来模拟。

例如，下面的代码在一个按钮上模拟了`click`事件：
```js
var btn = document.getElementById("myBtn");

// 创建event对象
var event = document.createEventObject();

/// 初始化event对象
event.screenX = 100;
event.screenY = 0;
event.clientX = 0;
event.clientY = 0;
event.ctrlKey = false;
event.altKey = false;
event.shiftKey = false;
event.button = 0;

// 触发事件
btn.fireEvent("onclick", event);
```

同样的方式也可以用来模拟keypress事件：
```js
var textbox = document.getElementById("myTextbox");

// 创建event对象
var event = document.createEventObject();

// 初始化event对象
event.altKey = false;
event.ctrlKey = false;
event.shiftKey = false;
event.keyCode = 65;

// 触发事件
textbox.fireEvent("onkeypress", event);
```
由于鼠标事件、键盘事件或其他事件的`event`对象并没有区别，因此使用通用的`event`对象可以触发任何类型的事件。注意，与DOM方式模拟键盘事件一样，这里模拟的`keypress`虽然会触发，但文本框中也不会出现字符。



## 总结<o>（旧）</o>

DOM2级事件(addEventListener) 
 
DOM0级事件(onclick)(只支持事件冒泡)

**DOM2级事件的阶段:**

* (w3c)  捕获阶段->处于目标阶段->冒泡阶段
* (ie)   处于目标阶段->冒泡阶段

```html
		<div class="baba">
			<div class="baba2">
				<div class="son">
				</div>	
			</div>
		</div>
```

```js
	<script type="text/javascript">
		var baba=document.querySelector(".baba");
		var baba2=document.querySelector(".baba2");
		var son=document.querySelector(".son");
		baba.addEventListener("click",function(){
			alert("baba");
		},true);  //核心,true为捕获事件
		baba2.addEventListener("click",function(){
			alert("baba2");
		});
		son.addEventListener("click",function(){
			alert("son");
		});
		document.addEventListener("click",function(){
			alert("document");
		});
	</script>
	
	// 结果： baba > son > baba2 > document
	
	// 如果把true去掉，结果为 son > baba >baba2 >document
		
```

**e.stopPropagation(); //阻止冒泡事件，阻止后面的冒泡**
```js
		baba2.addEventListener("click",function(e){
			e.stopPropagation(); //阻止冒泡事件，阻止后面的冒泡
			alert("baba2");
		});
		
		// 上面的baba2监听改成这样，结果： baba > son > baba2 
		// document没有执行
```

**e.preventDefault()； //阻止默认事件**
```html
		<a href="http://baidu.com">点我</a>
```

```js
		var n=0;
		a.addEventListener("click",function(e){
			e.preventDefault();  //阻止a标签的默认跳转
			n++;
			console.log(n);
			location.href="//baidu.com"
		})
```

**事件委托**

事件委托-通过事件冒泡的原理:将当前节点的事件,注册到该节点的"父级节点"中

```js
		baba.addEventListener("click",function(e){
			if(e.target.className=="son"&&e.target.nodeName=="DIV"){
				console.log("yeye");
			}
		})
```
