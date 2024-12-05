# Vue
[官方文档](http://doc.vue-js.com/v2/guide/)

[api](vue/vueApi.md)

## **vue的基本语法**

### {{messaage}} 文本插值

```
		<div class="app" >
		 原文本：	{{message}}
		</div>	
```
```
			var app=new Vue({
				el:'.app',
				data:{
					message:'hello'  //message:'<b>hello</b>' 会直接输出，不会识别html，不会覆盖原标签内的文本
				}
			});
```

> el表示当前创建的Vue实例，要控制页面上的哪个区域

> 说明：data属性中存放el中要用到的数据，    
将data里面的数据绑定到视图上，数据的更新会直接同步到DOM    

#### v-cloak
解决插值表达式闪烁问题（vue文件未加载时出现{{message}}）
```
<style>
	[v-vloak]{display:none}
</style>
```
```
		<div v-vloak class="app" >
			{{message}}
		</div>	
```

> `v-text`与`插值表达式`用法基本一样，且`v-text`没有闪烁问题

### v-text

```
		<div class="app" v-text='message'>  原本文字 </div>	
```
```
			var app=new Vue({
				el:'.app',
				data:{
					message:'hello'  //message:'<b>hello</b>' 会直接输出，不会识别html，相当于innerText,会覆盖掉原标签内的文本
				}
			});
```

### v-html

```
		<div class="app" v-html='message'>
		</div>	
```
```
			var app=new Vue({
				el:'.app',
				data:{
					message:'hello'  //message:'<b>hello</b>' 可以识别html，相当于innerHTML，会覆盖掉原标签内的文本
				}
			});
```


### v-bind
v-bind 属性被称为指令。指令带有前缀 v-，表示是由 Vue 提供的专用属性。


#### v-bind:title

将此元素的 title 属性与 Vue 实例的 title 属性保持关联更新

 鼠标悬停此处几秒，可以看到此处动态绑定的 title！
```
		<div class="app" >
			<span v-bind:title="title">
				{{message}}
			</span>
		</div>
```
```
			var app=new Vue({
				el:'.app',
				data:{
					message:'hello',
					title:'hellotitle',
				}
			});
```

> vue绑定的title，会将`""`内进行js代码块的解析,即`v-bind:title="title + '123'"`，也是可以的

---------

> `v-bind:`可以简写为`:`

#### v-bind:class

> 简写:`:class`

通过isbg的true、false来控制类的使用
```
			.bg_red{
				background-color: red;
			}
```
```
		<div class="app" >
			<span v-bind:title="title" v-bind:class="{'bg_red':isbg}">
				{{message}}
			</span>
		</div>
```
```
			var app=new Vue({
				el:'.app',
				data:{
					message:'hello',
					title:'hellotitle',
					isbg:true   
				}
			});
```

**以变量把类名存起来(数组方式)**

```
		<div class="app" >
			<span v-bind:class="[classA,classB]">
				{{message}}
			</span>
		</div>
```
```
			var app=new Vue({
				el:'.app',
				data:{
					message:'hello',
					classA:'bg_red',
					classB:'color'
				}
			});
```

以对象的方式



> 可做有条件来判断是否给予类的属性，比如选项卡

```
//可视为不见
			*{
				padding: 0;
				margin: 0;
				box-sizing: border-box;
			}
			.sav{
				border: 1px solid black;
				width: 500px;
				height: 300px;
			}
			ul{
				width: 100%;
				height: 15%;
			}
			li{
				width: 25%;
				border: 1px solid black;
				height: 100%;
				float: left;
				list-style-type: none;
				text-align: center;
				font-size: 20px;
			}
			.bg_blue{
				background-color: skyblue;
				display: block;
			}
			.content{
				height: 85%;
				background-color: skyblue;
				
			}
			.op{
				opacity: 1;
			}
```
```
		<div class="sav">
			<ul>
				<li v-for="(item,index) in li_arr" 
					v-on:click="say(index)" 
					v-bind:class="{'bg_blue':cur_index===index}">  //核心语句
					{{item}}
				</li>
			</ul>
			<div class="content" v-for="(div_content,index) in div_arr" 
								v-show="cur_index===index">  、、核心语句
				{{div_content}}
			</div>
		</div>
```
```
			var app=new Vue({
				el:'.sav',
				methods:{
					say(i){
						console.log(i);
						this.cur_index=i;  //点击获取当前下标
					}
				},
				data:{
					cur_index:0,
					blue:'bg_blue',
					li_arr:["选项1","选项2","选项3","选项4"],
					div_arr:["内容1","内容2","内容3","内容4"]
				}
			})
```

#### v-bind:style
与内联style绑定

```
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
```
data: {
  activeColor: 'red',
  fontSize: 30
}
```

**通常、简洁、较好的做法，`直接与 data 中的 style 对象绑定`**
```
<div v-bind:style="styleObject"></div>
```
```
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

> 注意:对象写法的属性名要符合小驼峰命名方法。如：`font-size` -> `fontSize`

----------

> 注意:`v-bind:style `的对象语法，通常也会和 `computed `属性结合使用，此` computed` 属性所对应的` getter `函数需要返回一个对象。

**自动添加前缀**

在 v-bind:style 中使用具有浏览器厂商前缀(vendor prefixes)的 CSS 属性时（例如 transform），Vue 会自动检测并向 style 添加合适的前缀。


**多个值**

从 2.3.0+ 起，你可以为每个 style 属性提供一个含有多个（前缀）值的数组，例如：

```<div v-bind:style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>```

> 最终，这只会从数组中找出「最后一个浏览器所支持的值」进行渲染。    
在这个示例中，对于支持「无需前缀版本的 flexbox」的浏览器，最终将渲染为 display: flex。


### v-if

符合条件下true，才会渲染DOM节点，不符合、为false就以删除元素的方式隐藏

> v-show和v-if一样是条件判断，不同的是v-show是以display：none的方式隐藏掉不符合条件的元素

----------

> 如果一开始就不用显示的元素，用v-show虽然隐藏起来了，但还是会渲染DOM的，会加大初始渲染消耗。     
而对于频繁切换的元素，用 v-show以减少切换性能消耗。


### v-for

循环渲染

```
<div class="sav">
		<div class="sav">
			<ul>
				<li v-for="(item,index) in li_arr"   //也可以循环对象(value,key) in li_obj
					v-on:click="say(index)" 
					v-bind:class="{[blue]:cur_index===index}">
					{{item}}
				</li>
			</ul>
		</div>
```
```
			var app=new Vue({
				el:'.sav',
				data:{
					blue:'bg_blue',
					li_arr:["选项1","选项2","选项3","选项4"]   //li_obj:{name:"liu",age:"18"}
				}
			})
```

> v-for还可以循环数字v-for="const in 10"，要注意的是循环数字是从1开始的

-------

> 在使用v-for时，如果v-for有问题，必须使用v-for的同时，指定唯一的 字符串/数字 类型 的v-bind：key值。

### v-on
v-on:click="say"

> 简写：`@click="say"`

事件绑定机制，定义事件类型和方法
#### 事件修饰符
* `.stop`
* `.prevent`
* `.capture`
* `.self`
* `.once`
* `.passive`

```
<!-- 停止点击事件冒泡 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重新载入页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以链式调用 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时，使用事件捕获模式 -->
<!-- 也就是说，内部元素触发的事件先在此处处理，然后才交给内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只有在 event.target 是元素自身时，才触发处理函数。 -->
<!-- 也就是说，event.target 是子元素时，不触发处理函数 -->
<div v-on:click.self="doThat">...</div>
```

> 实践:v-on:click.prevent.once阻止第一次点击的默认事件

----

> 使用修饰符时的顺序会产生一些影响，因为相关的代码会以相同的顺序生成。    
所以，使用 `v-on:click.prevent.self` 会阻止所有点击，而 `v-on:click.self.prevent` 只阻止元素自身的点击。


#### 按键修饰符(Key Modifiers)
```
<!-- 只在 `keyCode` 是 13 时，调用 `vm.submit()` -->
<input v-on:keyup.13="submit">
```
```
<!-- 和上面的示例相同 -->
<input v-on:keyup.enter="submit">

<!-- 也可用于简写语法 -->
<input @keyup.enter="submit">
```

**这里列出所有的按键修饰符别名：**

* `.enter`
* `.tab`
* `.delete (捕获“删除”和“退格”按键)`
* `.esc`
* `.space`
* `.up`
* `.down`
* `.left`
* `.right`

> 还可以自定义按键修饰符别名，通过全局` config.keyCodes` 对象设置：
```
// 可以使用 `v-on:keyup.f1`
Vue.config.keyCodes.f1 = 112
```

### v-model

双向绑定，输入框的输入内容会实时更新`message`的数据，并且`p`标签会实时显示
```
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
```
```
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```

> v-model只能用于表单元素

#### 手写一个数据绑定

```
<input id="input" type="text" />
<div id="text"></div>

let input = document.getElementById("input");
let text = document.getElementById("text");
let data = { value: "" };
Object.defineProperty(data, "value", {
  set: function(val) {
    text.innerHTML = val;
    input.value = val;
  },
  get: function() {
    return input.value;
  }
});
input.onkeyup = function(e) {
  data.value = e.target.value;
};
```

## **自定义指令**

**自定义`v-focus`**

使页面加载完元素获取焦点
```
	<input type="text" v-model="searchText" @input="search" v-focus/>
```

传入的两个参数，参数1位指令名，参数2为对象，对象内可编写函数
```
Vue.directive('focus',{
//	在元素绑定时执行
	bind:function(){},
//	在元素插入DOM时执行
	inserted:function(el){
		el.focus()  //el为绑定指令的元素，focus()为es6的方法
	},
//	在元素更新时执行
	updated:function(){}
});
```

> 注意：在自定义指令名时，不需加`v-`前缀，在调用时才需要加

**自定义`v-color`**

在传入el时，同时还可以传进binding对象

同时还可以传进binding对象
* binding
 * 	name：指令名，不包括 v- 前缀。
 * 	value：指令的绑定值，例如：v-my-directive="1 + 1" 中，绑定值为 2。
 * expression：字符串形式的指令表达式。例如 v-my-directive="1 + 1" 中，表达式为 "1 + 1"。


使字体颜色改变
```
	<input type="text" v-model="searchText" @input="search" v-focus v-color="'red'"/>
```
```
Vue.directive('color',{
//	在元素绑定时执行
	bind:function(el,binding){
		el.style.color=binding.value
	},
});
```

### 定义私有指令

```
		var app=new Vue({
				el:'#app',
				directives:{
					'fontsize':{
						bind:function(el,binding){
							el.style.fontSize=binding.value
						}
					}
				}
				
			})
```

#### 函数简写

在很多时候，你可能想在 bind 和 update 时触发相同行为，而不关心其它的钩子。

```
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```


## **computed 属性和 watcher**
如果要想对例如`message`数据进行处理时，传统的方法是：
```
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```

### computed

```
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
```
```
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    reversedMessage: function () {
      return this.message.split('').reverse().join('')
    }
  }
})
```

> `vm.reversedMessage` 的值始终取决于 `vm.message` 的值。    
>> computed定义的属性叫做`计算属性`，属性的本质是一个方法，不过我们会直接把`计算属性`当做属性来使用，    
不会把`计算属性`当做方法来调用。

---------------

> `reversedMessage: function(){}`也可以写到`methods`里，每当重新渲染的时候，`method` 调用总会执行函数。    
而`computed`里是基于它的依赖缓存，只有当`message`值改变时`reversedMessage`才会重新取值。

### watched

Vue.js 提供了一个方法 $watch ，它用于观察 Vue 实例上的数据变动.

```
var vm = new Vue({
   el: '#demo',
   data: {
      firstName: 'Foo',
    },
    watch: {
      firstName: function (newval,oldval) {  //改变够新的值和改变前旧的值
         
      }
  }
```

**监听路由地址的改变**
```
var vm = new Vue({
   el: '#demo',
   data: {},
    watch: {
      '$route.path': function (newval,oldval) {  //改变够新的值和改变前旧的值
         console.log(newval+"  "+oldval)
         if(newval=="/login"){
         	console.log("欢迎进入登录界面。")
         }
      }
  }
```

**watch 的一个特点是，最初绑定的时候是不会执行的,如果要最初绑定的时候就执行呢？**

`immediate`

```
watch: {
 firstName: {
  handler(newName, oldName) {
   this.fullName = newName + ' ' + this.lastName;
  },
  // 代表在wacth里声明了firstName这个方法之后立即先去执行handler方法
  immediate: true
 }
}
```



**两者区别**
```
<div id="demo">{{ fullName }}</div>
```
```
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```

上面代码是命令式的和重复的。跟计算属性对比：
```
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

> 在单纯进行输出处理时,如：字符串的拼接，用`computed`，当有复杂的逻辑判断时，如：边界判断，`watcher`更好

-----------

> 计算属性(`computed`)在大多情况下`更合适`，但有时也需要一个自定义的侦听器(`watcher`)，
当需要在数据变化时执行异步或开销较大的操作的时候，这个方法是最有用的.

## **Vue实例的生命周期**

### 生命钩子
```
var LIFECYCLE_HOOKS = [
  'beforeCreate',
  'created',
  'beforeMount',
  'mounted',
  'beforeUpdate',
  'updated',
  'beforeDestroy',
  'destroyed',
  'activated',
  'deactivated',
  'errorCaptured'
];
```

* `beforeCreate`

实例化之前触发

Vue只初始化,事件对象以及生命周期,data和methods中的数据都没有被初始化

* `created`

创建组件，data和methods中的数据已经初始化

Vue初始化一些处理数据的工具函数,特定义好数据,注入到Vue的实例中

* `beforeMount`

将注入的数据,传递到$vndo(类似虚拟DOM、内存中的DOM),对数据进行响应式处理

此时模板已在内存中，未把模板挂在到页面

* `mounted`

挂载数据（将响应式数据替换到页面上）

模板已经挂在在页面中，可以看到已经渲染好的页面

如果要操作DOM，最早要在`mounted`中进行

> 此时实例已经创立完成

* `beforeUpdate`

数据已经改变，但未更新到界面

* `updated`

响应式数据通过虚拟DOM比较后触发的

新的数据已经更新到页面

* `beforeDestroy`

组件销毁之前触发的，从运行阶段进入到销毁阶段，绑定的事件、数据Vue还没销毁，还处于可用状态，还没进入正真的销毁阶段

* `destroyed`

Vue内部已经销毁响应式数据、事件、watch属性

* `activated`

组件被激活触发

* `deactivated`
组件被雪藏（休眠）

> 使用（<keep-alive></keep-alive>）才会触发，因为不适用这标签，切换时会直接销毁组件

* `errorCaptured`

错误捕抓

## **组件**

### 自定义全局组件
组件化和模块化的不同：
* 模块化：从代码逻辑的角度进行划分；方便代码分层开发，保证每个模块功能的职能单一。
* 组件化：从UI界面角度进行分析；方便UI组件的重用。
```
<div id="example">
  <my-component></my-component>
</div>
```
```
// 注册
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})
```

> `template`属性指向的内容必须有且只能一个根元素

```
// 创建根实例
new Vue({
  el: '#example'
})
```

**另一种创建组件的方式**

在app容器外编写的html模板结构
```
<template id="tmp1">
	<div><h1>我是h1</h1></div>
<.template>
```
```
			Vue.component('my-component', {
  				template: '#tmp1'
			})
			var app=new Vue({
				el:'#app',
				data:{
				}
			})
```

**自定义开关组件**

注释才是关键
```
//可视而不见
		<style type="text/css">
			*{
				margin: 0;
				padding: 0;
			}
			span{
				display: block;
			}
			.switchWrap{
				width: 48px;
				height: 24px;
				line-height: 22px;
				background-color: #ccc;
				border: 1px solid #ccc;
				border-radius: 24px;
				vertical-align: middle;
				position: relative;
			 	cursor: pointer;
			}
			.switchWrap:after{
				content: "";
				width: 24px;
				height: 24px;
				border-radius: 24px;
				background-color: #fff;
			    position: absolute;
			    left: 1px;
			    top: 0px;
			    cursor: pointer;
			    transition: left;
			 	transition: all 0.5s;
			}
			.switchinner{
				color: #fff;
			    font-size: 12px;
			    position: absolute;
			    left: 25px;
			}
			.switch-checked{
				border-color: #39f;
    			background-color: #39f;
			}
			.switch-checked:after{
				left: 25px;
			}
			.switch-checked .switchinner{
				left: 8px;
			}
		</style>
```
```
		<div id="app">
				<Switch-btn></Switch-btn>
				<Switch-btn></Switch-btn>
		</div>
```
```
		<script src="vue.js" type="text/javascript" charset="utf-8"></script>
		<script type="text/javascript">
//			自定义全局Vue组件
			Vue.component("SwitchBtn",{
//			定义模板
				template: `
					<span class="switchWrap" v-bind:class="{'switch-checked':myswitch}" @click="switchClick">
						<span class="switchinner">
							<span v-if="myswitch">开</span>
							<span v-if="!myswitch">关</span>
						</span>
					</span>
				`,
//				data属性必须要以函数的方法获取
				data:function(){   
					return {
							myswitch:false
							}
				},
				methods:{
					switchClick(){
						if(this.myswitch){
							this.myswitch=false
						}else{
							this.myswitch=true
						}
					}
				}
			})
		</script>
		<script type="text/javascript">
			var app = new Vue({
				el:"#app"
			})
		</script>
```

### 定义私有组件
```
var app = new Vue({
				el:"#app",
				components:{
					login:{  //组件名
						templat:`<h1>我是私有组件</h1>`
					}
				}
			})
```


### 单向数据流(父子组件间的通讯)
**上面的data属性是在组件内部获取的，当要复用组件用外部不同的数据时，要使用Props传递数据**

```
		<div id="app">
				<!--Vue单向数据流-->
				<my-sav v-bind:list="outlist"></my-sav>  //子组件通过v-bind:list="outlist"注入到子组件中
				<my-sav v-bind:list="outsoo"></my-sav>
		</div>
```
```
		<script src="vue.js" type="text/javascript" charset="utf-8"></script>
		<script type="text/javascript">
//			自定义全局Vue组件
			Vue.component("mySav",{
				template: `<ul>
							<li v-for="item in list">
								{{msg}}{{item}}
							</li>
							</ul>
						`,
				props:["list"],   //子组件通过props定义接收list
				data:function(){  //组件中data必须要以函数返回的方式获取
					return {
						msg:`我是组件的date，`  //必须返回一个对象
					} 
				}
			})
		</script>
		<script type="text/javascript">
			var app = new Vue({
				el:"#app",
				data:{
					outlist:[1,2,3],
					outsoo:["首页","分页"]
				}
			})
		</script>
```

> 为什么组件中的data是一个function
```			
			var obj={con:0}
			Vue.component("mySav",{
				template: `<ul>
								<li>{{con}}</li>
							</ul>
						`,
				data:function(){  
					return obj   //因使用return {con:0}
				}
			})
```
>> 此时组件重用时，改变一个组件的数据时，其他组件的数据也会被改变

-----------

> 当父组件的属性变化时，将传导给子组件，但是不会反过来。    
就是子组件不会修改父组件属性的值，如果想做刀这一点，需要自定义事件。

### 兄弟组件间的通讯

借助一个媒介Bus（实例化的对象）


### 自定义事件
* 使用 `$on(eventName)` 监听事件
* 使用 `$emit(eventName)` 触发事件

```
		<div id="app">
				 //注入了自定义事件update
				 //v-on:update=anhao 触发update调用anhao
				<news v-bind:newstext="text" v-on:update22=anhao></news> 
		</div>
```
```
		<script src="vue.js" type="text/javascript" charset="utf-8"></script>
		<script type="text/javascript">
			Vue.component("news",{ 
			// 模板，定义update函数
				template: `<input type="text" v-bind:value="newstext" 
							v-on:input="update($event)" />`,
				methods:{
					update($event){
						this.$emit("update22",$event.target.value)
					}
				},
				props:["newstext"]
			})
		</script>
		
		<script type="text/javascript">
			var app = new Vue({
				el:"#app",
				data:{
					text:'123',
				},
				methods:{
					anhao(str){
//						str相当于接收的是updatad的$event.target.value
						this.text=str
					}
				}
			})
		</script>
```


> 要点：当子组件修改了父组件传进来的值后，需要派发一个“通知”，    
告诉父组件更新值，然后再重新注入到子组件中。

-----

> 这样做保证了数据的来源（发布订阅模式）

### 动态组件

```
	<div id="app">
			<button v-for="item in list" @click="curComponent=item">{{item}}</button>
//			<!-->核心代码<-->
			<component :is="curComponent"></component>  //:is=""里面的值为组件名
	</div>
```
```
			Vue.component("home",{
				template: `<div>首页</div>`
			})
			Vue.component("my",{
				template: `<div>我的</div>`
			})
			
			var app=new Vue({
				el:'#app',
				data:{
					list:["home","my"],
					curComponent:"home"
				}
			})
```

**另一种写法**

```
		<div id="app">
			<button v-for="item in tabs" @click="curComponent=item">{{ item.name }}</button>
			<component :is="curComponent"></component>
		</div>
```
```
			var tabs=[
			  {
			  	name:"home",
			  	template:`<div>im a home</div>`
			  },
			  {
			  	name:"about",
			  	template:`<div>im a about</div>`
			  }
			]
			
			var app=new Vue({
				el:'#app',
				data:{
					tabs:tabs,
					curComponent:tabs[0]
				}
			})
```

## **vue 过渡&动画**

Vue 提供了 transition 的封装组件，在下列情形中，可以给任何元素和组件添加进入/离开过渡


**过渡的类名**

在进入/离开的过渡中，会有 6 个 class 切换。
* 1.`v-enter` （加入、显现）

* 2.`v-enter-active`

* 3.`v-enter-to`

* 4.`v-leave` （移出、消失）

* 5.`v-leave-active`

* 6.`v-leave-to`

要动画的的元素放在`transition`标签内
```
			<transition name="fade">
				<component :is="curComponent"></component>
			</transition>
```

> 在对多个元素需要实现动画的，比如通过`v-for`渲染出来的，要用`transition-group`标签包裹起来    
>> 而且要为`v-for`循环的动画设置属性，每一个元素必须设置`:key`属性

----------

> 当删除`v-for`循环元素中的一个元素时，其他元素`位移`占用位置时的动画设置(固定写法)：    
```
	.v-move{transition:all 0.6 ease}
	.v-leave-active{position:absolute}
```
>> 被删除的元素离场是会缩小，给定宽或高即可

-----------

> 为`v-for`循环渲染的元素加载到页面时入场动画效果：`<transition-group appear></transition-group>`

--------

> `<ul><transition-group appear><li v-for=""></li></transition-group></ul>`    
此时渲染出来的`html`结构为`<ul><span><li v-for=""></li></span></ul>`,不符合规范
>> 解决方法：`<transition-group appear tag="ul"><li v-for=""></li></transition-group>`

事例样式
```
	.fade-enter-active, .fade-leave-active {
  		transition: all .5s;
	}
	.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  		transform: translateX(-100%);
	}
```

假设当前显示A元素，点击切换B元素

1. A元素在开始时`v-leave`类的属性为`translateX(0%)`

2. 同时B元素`v-enter`类开始时属性为 `translateX(-100%)`

2. 开始动画，`v-enter-active`类和`v-leave-active`，加入入过渡效果`transition: opacity .5s`

3. A达到动画目标`v-leave-to`类，`translateX(-100%)`，瞬间删除

4. B元素`v-enter-to`类属性为`translateX(0%)`

总的来说就是`原始状态`和`目标状态`的切换，不同状态给予不同的类，当前的元素向目标状态转`,另一个元素从目标状态向当前状态转换。

> 此时进场动画和离场动画是同时出发的，设置`mode="out-in"`使离场动画结束后再进场动画

### 使用第三方类实现动画
[Animtion.css演示](https://daneden.github.io/animate.css/)

引入Animtion.css

直接引用动画类名, `:duration="毫秒值"`使用设置入场、离场的动画时长
也可以入场和离场的动画时长分别设置`:duration="{enter:200,leave:200}"`
```
			<transition enter-active-class="animated bounceIn" leave-active-class="animated bounceOut" :duration="200">  
				<h3 v-if="true">这是一个h3</h3>
			</transition>
```

### 设置半场动画（入场或离场）
借助钩子函数（动画的生命周期函数）

```
<transition
  v-on:before-enter="beforeEnter"  //beforeEnter(el){}设置动画入场前的起始样式,el为原生JS DOM对象
  v-on:enter="enter"			//enter(el,done){done()} 调用done，立即执行afterEnter，否则会有动画结束延迟
  v-on:after-enter="afterEnter"
  v-on:enter-cancelled="enterCancelled"
  v-on:before-leave="beforeLeave"
  v-on:leave="leave"
  v-on:after-leave="afterLeave"
  v-on:leave-cancelled="leaveCancelled"
>
  <!-- ... -->
</transition>
```

>> 动画不生效加`el.offsetWidth`试试？

## vue-resource (推荐使用axios)

### 实现get，post，jsonp请求

引入vue-resource.js文件

**实现get**
```
			var app=new Vue({
				el:'#app',
				methods:{
					this.$http.get('请求地址')
					.then(function(result){consoloe.log(result)},function(){console.log(“请求失败”)})
				}
			})
```

**post**

`this.$http.post()`接收三个参数

1.请求URL地址  

2.提交给服务器的数据，以对象形式提交给服务器

3.配置对象，以哪种形式提交给服务器，{emulateJSon:true}以普通表格形式

在result.body中才是所需要的数据

```
		this.$http.post('请求地址',{},{})
		.then(function(result){consoloe.log(result)},function(){console.log(“请求失败”)})
			
		手动发起的请求没有表单格式，有些服务器处理不了
		this.$http.post('请求地址',{},{emulateJSON:true})
		.then(function(result){consoloe.log(result)})
			
```

> 可以全局启用emulateJSON:true，不用每次都发送配置信息给服务器。    
`Vue.http.options.emulateJSON=true;`


**jsonp请求**
```
		this.$http.jsonp('请求地址',{},{emulateJSON:true})
		.then(function(result){consoloe.log(result)})
			
```

### 设置baseURL
在实例化Vue对象前设置

`Vue.http.options.root="http://www.baidu.com/"`

> 如果我们通过了全局配置了请求接口，每次单独发起http请求的路径要以相对路径开头，前面不能带`/`，否则不会启用路径拼接。

## **axios**

在项目内安装`$ npm install axios`

引入`import axios from "axios"`

### GET

```
// 为给定 ID 的 user 创建请求
axios.get('/user?ID=12345')
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```
```
// 可选地，上面的请求可以这样做
axios.get('/user', {
    params: {
      ID: 12345
    }
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```

### POST

```
axios.post('/user', {
    firstName: 'Fred',
    lastName: 'Flintstone'
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```

### 执行多个并发请求

```
function getUserAccount() {
  return axios.get('/user/12345');
}

function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}

axios.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread(function (acct, perms) {
    // 两个请求现在都执行完成
  }));
```

### axios拦截器

在使用axios做ajax请求时，可直接填写`/login`，不用拼接baseUrl
```
import axios from "axios"

let baseUrl="http://www.baidu.com"
axios.interceptors.ququest.use(function(config){
	console.log(config)
	config.url=baseUrl+config.url
	return config
})
```

## ref获取DOM元素和组件

ref (reference)

```
		<div id="app">
			<h3 ref="myh3">我是h3</h3>
		</div>
```
```
		var app=new Vue({
			el:'#app',
			methods:{
				getElement(){
					console.log(this.$refs.myh3.innerText)
				}	
			}
		})
```

> 注意：console.log(this.`$refs`.myh3.innerText)



## **vue-router(路由)**
```
<router-link to="/login"></router-link>  //默认渲染为a标签  ，
<router-link to="/register"></router-link>
<router-view></router-view>   //路由匹配的组件展示到这
```
```
	var login={
		tamplate:'<h1>登录组件</h1>'
	}
	var register={
		tamplate:'<h1>注册组件</h1>'
	}
```
```
	var routerobj=new VueRouter({
		routes:[
			{
				path:'/login',   //path表示监听路由地址
				component:login  //匹配对应path展示component属性对应的组件
			},
		    {
		    	path:'/register',
				component:register
		    }
		]
	})
```
```
	var app=new Vue({
		el:'#app',
		router:routerobj  //将路由规则对象，注册到app实例上，用来监听URL地址的变化，然后展示对应的组件
	})
```


### 重定向和别名

**重定向**

当用户访问` /`时，URL 将会被替换成` /home`，然后匹配路由指向为` /home`
```
routes: [
	{
		path:"/",
		redirect:'/home'  //对象写法redirect:{name:'home'}
	},
	{
      path: '/home',
      component: Home
    }
	]
```

**别名**

`/a `的别名是 `/b`，意味着，当用户访问` /b` 时，URL 会保持为` /b`，但是路由匹配则为` /a`，就像用户访问 `/a `一样。
```
const router = new VueRouter({
  routes: [
    { path: '/a', component: A, alias: '/b' }
  ]
})
```

结合使用
```
routes: [
	{
		path:"/",
		redirect:'/index'
	},
    {
      path: '/home',
      component: Home,
	  alias:'/index'
    }
    ]
```

> 访问`/a`和 `/b`，路由匹配都为` /a`

### 激活类active-class

默认情况下`active-class`为`router-link-active `

当路由被选中时会添加激活类，选中状态是根据URL来判断的

而`router-link-exact -active` 是更为严格的匹配，也就是会精确匹配 Url。    
所以如果给主路由设置了 exact 属性的话，当处于子路由时，Url 就匹配不到主路由了，那么主路由也就不会处于选中状态。

**也可以在路由的构造函数里自定义激活类**
```
const router = new VueRouter({
  routes: [
		linkActiveClass:'myactive'
  ]
})
```

> 也可以在路由构造函数选项 `linkExactActiveClass`进行全局配置。

### 路由的嵌套


在router-view里展示的组件的里面，还有一个router-view
在子路由的router-link的to属性to:="/home/hitnow"
```
routes: [
	{
		path:"/",
		redirect:'/home'
	},
    {
      path: '/home',
      component: Home,
	  children:[    //	  子路由
	  		{
	  			path:'hitnow',       //  path为 /hitnow 时，不会拼接父级路由的 /home
	  			component:hitnow
	  		},
	  		{
	  			path: 'about',
      			component: mytext
	  		}
	  ]
    }
```

### 命名视图

```
<router-view></router-view>
<router-view name="left"></router-view>   //name的值为字符串,:name的值为变量
<router-view name="main"></router-view>
```

```
routes: [
	{
		path:"/",     //匹配path路径，在对应的router-view的name属性放对应的组件
		components:{
			'default':A,
			'left':B,
			'main':C
		}
	}
	]
```

> 注意：是`components`


## **路由带参数的跳转**

1.路由注册
```
	routes=[
		{
	    	path:'/details/:id',
	    	component:mydetails
	    }
    ]
```

2.添加点击事件

3.点击事件触发
```
this.$router.push({path:'/details/123')  //传变量path:'/details/${id}'  //  效果：details/123
router.push({ name: 'details', params: { id }})  //  效果：details/123
```

通过query传参，效果`mydetails?id=1`
```
this.$router.push({path:'/details',query:{id:1})
```

获取传过来的数据
```
this.$route.query.id  
```

### vuex

```
		<div class="a">组件A</div>
		<div class="b">组件B</div>
		<div class="c">组件C</div>
		
		<script type="text/javascript">
//			传统方式
			
//			将组件A的值传给C
			var a=document.querySelector(".a");
			var b=document.querySelector(".b");
			var c=document.querySelector(".c");
			b.innerText+=a.innerText
			
//			store模式
			var store={
				state:{
					toC:""
				},
				mutation:{
					setToc(newvalue){
						store.state.toC=newvalue
					}
				},
				commit:function(type,newvalue){
					store.mutation[type](newvalue)
				}
			}
			store.commit("setToc",123)
			c.innerText=store.state.toC
		</script>
```

获取`this.$store.state.htitle`
设置`this.$store.commit("sethtitle",{htitle:"影院"})`

## **安装vue-lazyload**

`$ npm install vue-lazyload -D` 安装

`import VueLazyload from 'vue-lazyload'`  在main.js引入

`Vue.use(VueLazyload)` 使用

```
Vue.use(VueLazyload, {
  preLoad: 1.3,
  error: 'dist/error.png',  //加载失败图片
  loading: 'dist/loading.gif', //加载中图片
  attempt: 1
})
```

vue文件中将需要懒加载的图片绑定 v-bind:src 修改为 v-lazy 

`<img class="item-pic" v-lazy="newItem.picUrl"/>`

## **vue-cli**
`npm install @vue/cli -g`

### 创建一个vue项目

* `vue create hello-world`     //hello-world为项目名
* 选择`Manually select features`(手动选择功能)
* 按`空格键`多选功能，`回车键`确定. Babel、Progressive Web App (PWA) Support、Router、Vuex、CSS Pre-processors、Linter / Formatter
* `Use history mode for router?` `Y`
* Pick a CSS pre-processor:`Sass/SCSS`
* Pick a linter / formatter config:`ESLint with error prevention only`
* Pick additional lint features:`Lint on save`
* Where do you prefer placing config for Babel, PostCSS, ESLint, etc.?`In package.json`
*  Save this as a preset for future projects?(将次保存为未来的项目配置？)`N`

#### vue项目结构

按上面的方式创建的vue项目一般为以下结构：

* node_modules(工具包)
* public（单页index）
* src
 * assets(存放外部的静态资源)
 * compnents(存放组件)
 * views(存放页面)
* App.vue
* main.js
*
*
* 等等（后面暂时用不上）



#### 引入svg模块
* 在项目的根目录创建vue.config.js文件。

编写：
```
// vue.config.js
module.exports = {
  chainWebpack: config => {
    const svgRule = config.module.rule('svg')

    // 清除已有的所有 loader。
    // 如果你不这样做，接下来的 loader 会附加在该规则现有的 loader 之后。
    svgRule.uses.clear()

    // 添加要替换的 loader
    svgRule
      .use('vue-svg-loader')
        .loader('vue-svg-loader')
  }
}
```

* 在项目根目录`npm install vue-svg-loader -D`

* 然后就可以把svg文件当组件一样引用了

> 注意：在引用`vue-svg-loader`模块的时候，默认情况下`removeViewBox:false`，也就是ViewBox属性被删除了。    
即使在svg有ViewBox属性但也不会编译，所以在没有ViewBox的情况下，在外部改svg的宽高是出现错误的布局的。

----------

> 解决方法：在`node_modules`文件夹先找到svg-to-vue文件夹，然后在svgoConfig里添加属性
```
svgoConfig = {
    	plugins:[
    		{
    			removeViewBox:false
    		}
```

----------

> 更好的解决方法：在`.loader('vue-svg-loader')`后面添加
```
.options({
    svgo：{
    	plugins:[
    		{removeViewBox:false}
    	]
    }
})
```

#### 打包项目

`npm run build`
