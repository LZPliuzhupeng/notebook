## css 沉叠样式表
<b>Cascading Style Sheet</b>
css运行环境为浏览器上

z-index的生效条件：position为非static

后者覆盖前者，改变元素的层叠优先级，默认值为z-index:0


## 浏览器前缀
不同的浏览器可能需要不同的前缀。    
它表示该CSS属性或规则尚未成为W3C标准的一部分，是浏览器的私有属性，虽然目前较新版本的浏览器都是不需要前缀的，但为了更好的向前兼容前缀还是少不了的。

```
-webkit	chrome和safari
-moz	   firefox
-ms  	  IE
-o	     opera
```

## 伪类选择符
```CSS
选择符	版本	描述
E:link	CSS1	设置超链接a在未被访问前的样式。
E:visited	CSS1	设置超链接a在其链接地址已被访问过时的样式。
E:hover	CSS1/2	设置元素在其鼠标悬停时的样式。
E:active	CSS1/2	 设置元素在被用户激活
(在鼠标点击与释放之间发生的事件)时的样式。
E:focus	CSS1/2	 设置元素在成为输入焦点
(该元素的onfocus事件发生)时的样式。
E:lang(fr)	CSS2	匹配使用特殊语言的E元素。很少用
E:not(s)	CSS3	匹配不含有s选择符的元素E。
E:root	CSS3	匹配E元素在文档的根元素。常指html元素
E:first-child	CSS2	匹配父元素的第一个子元素E。
E:last-child	CSS3	匹配父元素的最后一个子元素E。
E:only-child	CSS3	匹配父元素仅有的一个子元素E。
E:nth-child(n)	CSS3	匹配父元素的第n个子元素E。
E:nth-last-child(n)	CSS3	匹配父元素的倒数第n个子元素E。



E:first-of-type	CSS3	匹配同类型中的第一个同级兄弟元素E。
E:last-of-type	CSS3	匹配同类型中的最后一个同级兄弟元素E。
E:only-of-type	CSS3	匹配同类型中的唯一的一个同级兄弟元素E。
E:nth-of-type(n)	CSS3	匹配同类型中的第n个同级兄弟元素E。
E:nth-last-of-type(n)	CSS3	匹配同类型中的倒数第n个同级兄弟元素E。
E:empty	CSS3	匹配没有任何子元素（包括text节点）的元素E。
E:checked	CSS3	匹配用户界面上处于选中状态的元素E。
(用于input type为radio与checkbox时)
E:enabled	CSS3	匹配用户界面上处于可用状态的元素E。
E:disabled	CSS3	匹配用户界面上处于禁用状态的元素E。
E:target	CSS3	匹配相关URL指向的E元素。
```

## 属性选择符
```
选择符	版本	描述
E[att] 	CSS2	选择具有att属性的E元素。
E[att="val"]	CSS2	选择具有att属性且属性值等于val的E元素。
E[att~="val"]	CSS2	选择具有att属性且属性值为一用空格分隔的字词列表，其中一个等于val的E元素。
E[att^="val"]	CSS3	选择具有att属性且属性值为以val开头的字符串的E元素。
E[att$="val"]	CSS3	选择具有att属性且属性值为以val结尾的字符串的E元素。
E[att*="val"]	CSS3	选择具有att属性且属性值为包含val的字符串的E元素。
E[att|="val"]	CSS2	选择具有att属性且属性值为以val开头并用连接符"-"分隔的字符串的E元素。 

```

```
<p class="a">测试数据1</p>
<p class="qq">测试数据2</p>
<p class="xyz abc">测试数据3</p>
<p class="aa123">测试数据4</p>
<p class="test-abc">测试数据5</p>
<p class="hello-z-world">测试数据6</p>
<p class="y-1">测试数据7</p>
<p class="y-2">测试数据7</p>
p[class]{color:green;}
p[class="qq"]{color:red;}     
p[class~="abc"]{color:blue;}
p[class^="aa"]{color:yellow;}       
p[class$="abc"]{color:black;}
p[class*="z"]{color:orange;}
p[class|="y"]{color:#ccc;}
```

## 伪对象选择符
```
选择符  	 版本	描述
E:first-letter/E::first-letter 	CSS1/3  	设置对象内的第一个字符的样式。
E:first-line/E::first-line 	CSS1/3 	设置对象内的第一行的样式。 
E:before/E::before  	CSS2/3	设置在对象前（依据对象树的逻辑结构）发生的内容。用来和content属性一起使用 
E:after/E::after	 CSS2/3 	设置在对象后（依据对象树的逻辑结构）发生的内容。用来和content属性一起使用 
E::placeholder   	CSS3	设置对象文字占位符的样式。
E::selection	CSS3 	设置对象被选择时的颜色。 
```

#### 例子1

```
html: 
<p class="t1">测试css的伪对象选择符，天气晴朗，阳光明媚!</p>  
 
css:
p.t1{width:100px;border:1px solid black;}
/* CSS3的语法改成 ::,原本是: */
p.t1::first-letter {font-size:20px;font-weight:bold;}/*第一个字符*/
p.t1::first-line {color:blue;}/* 第一行 */
```
<img src="../_img/伪对象_例子1.png"></img>


#### 例子2

```
html: <p class="t">test</p>

css:  .t2::before{content:'123';}
      .t2::after{content:'456';}
```
<img src="../_img/伪对象_例子2.png"></img>


#### 例子3

```
html：<input type="search" placeholder="测试">

css: 
   input::-webkit-input-placeholder {
	color: green;}
   input:-ms-input-placeholder { // IE10+
	color: green;}
   input:-moz-placeholder { // Firefox4-18
	color: green;}
   input::-moz-placeholder { // Firefox19+
	color: green;}
```
<img src="../_img/伪对象_例子3.png"></img>

#### 例子4

```
html:    <h3>请使用鼠标选取我</h3>
         <p>请使用鼠标选取我</p>
         
css:
    p::-moz-selection{ background-color:#E13300; color: white;}
    p::selection{ background-color: #E13300; color: white; }
注: 此选择符的兼容性不是很好，需要用到特定浏览器的前缀
```
<style>
p::-moz-selection{ background-color:#E13300; color: white;}
    p::selection{ background-color: #E13300; color: white; }
</style>
<h3>请使用鼠标选取我</h3>
<p>请使用鼠标选取我</p>

<img src="../_img/伪对象_例子4.png"></img>

> 主意：    
伪对象插进的字符不可被选    
在p标签里插入是属于p标签的


## 字体样式


#### 1、字体名称
语法：     
`font-family	:  <family-name>`    
说明：    
设置文字名称，可以使用多个名称，或者使用逗号
分隔，浏览器则按照先后顺序依次使用可用字体。    
例：    
`p { font-family:‘宋体'，'黑体', 'Arial’ }`


#### 2、 font-size(字体大小)
设置文字的尺寸    
语法:    
`font-size ： <length> | <percentage>`    
> 注：如果用百分比作为单位，则是基于父元素字体的大小    
例：    
`p { font-size:14px;}`	


#### 3. font-weight(字体加粗)
控制字体粗细    
语法：    
`font-weight : normal | bold | bolder | lighter | 100 | 200 | 300 | 400 | 500 | 600 | 700 | 800 | 900 `    
例：    
`p { font-weight:bold;}`


#### 4. font-style(字体斜体)
控制字体是否倾斜    
`font-style : normal | italic | oblique `    
例：    	      
```
	p { font-style: normal; } 
	p { font-style: italic; } 
    p { font-style: oblique; } 
```

取值：    
normal：    
指定文本字体样式为正常的字体    
italic：    
指定文本字体样式为斜体。对于没有设计斜体的特殊字体，如果要使用斜体外观将应用oblique    
oblique：   
指定文本字体样式为倾斜的字体。人为的使文字倾斜，而不是去选取字体中的斜体字    

#### 5.行高
控制段落内每行高度    
`line-height : normal | length `    
例：	 
   
```
p { line-height:25px;}
p { line-height:150%}
```

如果字体大小大于行高，布局会混乱    
此时的行高为20px    
`p{font-size:10px}   p { line-height:2;}`    


#### 6. font(字体样式缩写)
`font : font-style || font-variant || font-weight || font-size || / line-height || font-family`    
例：    
```
p{
	font-style:italic;
	font-weight:bold;
	font-size:14px;
	line-height:22px;
	font-family:宋体;
}
```
缩写后：    
`p { font:italic bold 14px/22px 宋体}`    


#### 6. color(字体颜色)
控制文本的字体颜色    
语法：     
`color：<color>`    
说明：    
颜色属性值分三种值的格式    
> 1.	英文单词，比如 red , yellow ,green …
2.	十六进制表示方式，#开头，6个十六进制的字符或数字 组合。比如：#FFFFFF，#000000，#CCCAAA，#22BCAD。十六进制： 0-9 和 a-f 
3.	RGB模式，红 0-255，绿 0-255，蓝 0-255。    
比如： RGB(120,33,234) RGBA(255,0,0,.5)   RGBA模式，最后的A表示透明度50%
例：    
```
p{
	color:#FF0000;
}
```

		
Placeholder    
可在样式里设置字体大小，但不可改颜色    
```
input[type=”text”]{
color:blue;
font-size:20px;
}
```

在谷歌浏览器设置颜色：
```
input：：-webkit-input-placeholder{
 color:red;
}
```
> 不同浏览器设置方式不同    


#### 7. text-decoration(文本装饰线条)
控制文本装饰线条    
`text-decoration : none || underline || blink || overline || line-through `    
例：    	
```		
p { text-decoration:overline;}
p { text-decoration:underline;}
p { text-decoration:line-through;}
```


#### 8. text-shadow(文字阴影)
控制文字的阴影部分。     
```
text-shadow: h-shadow v-shadow blur color;
h-shadow         必需。水平阴影的位置。允许负值。
v-shadow         必需。垂直阴影的位置。允许负值。
blur                   可选。模糊的距离。
color                 可选。阴影的颜色。 
```


实例：    
```
h1{
	text-shadow: 2px 2px #ff0000;
}
   	body>p{
   		font-size: 50px;
   		text-shadow: -2px -2px 8px black;
   	}

   	body>p{
   		font-size: 50px;
   		text-shadow: -2px -2px 8px black,
-6px -6px 8px black,
-10px -10px 8px black,
-14px -14px 8px black;
}
```

#### 9. @font-face嵌入字体

@font-face能够加载服务器端的字体文件，让浏览器端可以显示用户电脑里没有安装的字体。    　
语法：

```
@font-face { 
font-family : 自定义字体名称; 
src : url(字体文件在服务器上的相对或绝对路径)  format(字体类型); 
}
```

例：    
```
@font-face {/*该写法是兼容所有的浏览器*/
	font-family : bgg;
	src: url('fonts/fontawesome-webfont.eot'), /* IE9+ */
	url('fonts/fontawesome-webfont.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
	url('fonts/fontawesome-webfont.woff') format('woff'), /* chrome、firefox */
	url('fonts/fontawesome-webfont.ttf') format('truetype'), /* chrome、firefox、opera、Safari,Android, iOS 4.2+*/
	 url('fonts/fontawesome-webfont.svg') format('svg'); /* iOS 4.1+ */
}
p{ font-family: bgg }
```


#### 10、字体垂直居中
1、	固定高度，单行垂直居中(如：字体在div内垂直居中)
css:height=line-height;    

2、	单行内，元素之间垂直居中（如：radio与文字垂直对齐，与img的align：center相似）
css:vertical-align:middle;    



文字模型
 

```
text-decoration
值	描述
none	默认。定义标准的文本。
underline	定义文本下的一条线。
overline	定义文本上的一条线。
line-through	定义穿过文本下的一条线。


vertical-align
值	描述
baseline	默认。元素放置在父元素的基线上。
sub	垂直对齐文本的下标。
super	垂直对齐文本的上标
top	把元素的顶端与行中最高元素的顶端对齐
text-top	把元素的顶端与父元素字体的顶端对齐
middle	把此元素放置在父元素的中部。
bottom	把元素的顶端与行中最低的元素的顶端对齐。
text-bottom	把元素的底端与父元素字体的底端对齐。
length	 
%	使用 "line-height" 属性的百分比值来排列此元素。允许使用负值。
inherit	规定应该从父元素继承 vertical-align 属性的值。
```


#### 块级元素、行内元素
1、	块级元素    
2、	行内块元素    
分两种：    
一为  置换元素（可以设置宽高）（因为是外部引入进来的，在文档流里没有基准线）    
二为  非置换元素（不可以设置宽高）
3、	行内元素    

img默认的display为inline，与文字的对齐是按基线，无文字按父级对齐，所以img在div底部的空白节点为基线到底线的空白距离，把img设为块级元素即可。


#### 10. font-variant= small-caps
font-variant= small-caps 将文本转换为小型的大写字母    

#### 11.文字间距
`letter-spacing : normal | length `    

#### 12.文字溢出
控制文字内容溢出部分的样式    
语法：    
`text-overflow：clip | ellipsis`    
```
值	描述
clip 	当内联内容溢出块容器时，将溢出部分裁切掉。 
ellipsis	当内联内容溢出块容器时，将溢出部分替换为（...）。 
但是text-overflow只是用来说明文字溢出时用什么方式显示，    
要实现溢出时产生省略号的效果，还须定义强制文本在一行内显示（white-space:nowrap）及溢出内容为隐藏（overflow:hidden），    
只有这样才能实现溢出文本显示省略号的效果。
省略号垂直居中，把字体设置为 SimSun 字体即可
```

#### 例子2：多行溢出 
多行溢出的做法：    
```
div{
overflow : hidden;	/*条件1：超出部分隐藏*/
text-overflow: ellipsis;/*条件2：超出部分显示。。。*/
display: -webkit-box;/*条件3：设置为伸缩盒*/
-webkit-line-clamp: 3;/*这是要显示的行数*/    （共4行文字，在第3行末尾显示…）
-webkit-box-orient: vertical;/*设置排版方向为从上到下*/
font-size:16px;
line-height: 20px;
height: 60px;	
width: 100px;
}
```

#### 13.文字换行
控制内容超过容器的边界时是否断行    
语法：
`white-space ： normal | nowrap | pre-line | pre-wrap`    
```
值	描述
normal 	默认处理方式
nowrap	强制在同一行内显示所有文本，合并文本间的多余空白，直到文本结束或者遭遇br对象。
pre-line	保持文本的换行，不保留文字间的空白距离，当文字碰到边界时发生换行。(ie6-ie7不支持，Firefox2.0-3.0不支持)
pre-wrap	用等宽字体显示预先格式化的文本，不合并文字间的空白距离，当文字碰到边界时发生换行。(ie6-ie7不支持，Firefox2.0需要加-moz-)
```

#### 14.非文字换行
这个属性是基于该容器允许换行的前提下才会生效，例如之前已经设置了white-space:nowrap,则该属性无效    
语法：    
`word-break ： normal | break-all `    
```
值	描述
normal 	依照亚洲语言和非亚洲语言的文本规则，允许在字内换行
break-all	允许非亚洲语言文本行的任意字内断开，例如连续的英文字母和数字
```

#### 15.字间隔 
增加或减少单词间的空白(即字间隔)    
该属性定义元素中字之间插入多少空白符。针对这个属性，“字” 定义为由空白符包围的一个字符串。    
如果指定为长度值，会调整字之间的通常间隔;所以,normal 就等同于设置为 0。允许指定负长度值,这会让字之间挤得更紧    
语法：    
`word-spacing ： normal | length | percentage`     

#### 16.文本书写方式
实际上writing-mode这个CSS属性在上古时代就诞生了，IE5.5浏览器就已经支持了，但那时FireFox, Chrome这些现代浏览器都不支持。    
目前已经得到现代浏览器的支持    
语法(css3)：    
`writing-mode：horizontal-tb | vertical-rl | vertical-lr`    
```
值	描述
horizontal-tb	水平方向自上而下的书写方式。即 left-right-top-bottom
vertical-rl	垂直方向自右而左的书写方式。即 top-bottom-right-left
vertical-lr	垂直方向自左而右的书写方式
```
> 说明:
作为IE的私有属性之一,IE5.5率先实现了 writing-mode ,后期被w3c采纳成标准属性；    
此属性效果不能被累加使用。例如,父对象的此属性值设为 tb-rl ,子对象再设置该属性将不起作用，仍应用父对象的设置.    
安卓，ios，Chrome47之前版本,Safari浏览器需要添加前缀-webkit-,IE浏览器需要用到私有属性，    


## media（媒体查询）

通过不同的媒体类型和条件定义样式表规则。

* 媒体查询让CSS可以更精确作用于不同的媒体类型和同一媒体的不同条件
。
* 媒体查询的大部分媒体特性都接受min和max用于表达“大于或等于”和“小与或等于”。如：width会有min-width和max-width

* 媒体查询可以被用在CSS中的@media和@import规则上，也可以被用在HTML和XML中。

```
@media only screen and (max-width:500px ) {
	body{
		background-color: red;
	}
}
```


## 回流、重绘

**回流paint**

因浮动和定位，有元素需要重新不顾


重绘reflow

在图片加载的时候不影响后面的布局

 > 回流一定引起重绘，重绘不一定引起回流
 

## 颜色渐变
CSS3 Gradient 分为线性渐变(linear)和径向渐变(radial)。

由于不同的渲染引擎实现渐变的语法不同，这里我们只针对线性渐变的 W3C 标准语法来分析其用法，其余大家可以查阅相关资料。
W3C 语法已经得到了 IE10+、Firefox19.0+、Chrome26.0+ 和 Opera12.1+等浏览器的支持。

`background-image:linear-gradient(to left, red 30%,blue);`

–	第一个参数省略时，默认为“180deg”，等同于“to bottom”。    
–	第二个和第三个参数，表示颜色的起始点和结束点，可以有多个颜色值。(颜色值后面可以追加百分比，表示这个颜色要占总背景颜色面积的百分比)


## transition

`transition: background-color 1s linear;`

#### property
**1.transition-property: background-color;**

（显示）过度动画的属性，如果不设置变化就是一瞬间，不会有过度效果，多个值用逗号隔开，     

如：
`transition-property: background-color,width；`

#### duration
**2.transition-duration: 1s;**

动画的持续时间，可以对应不同的动画属性设置对应的时间，

如：`transition-duration: 1s，10s;`

#### timing-function
**3.transition-timing-function: linear;**
动画的播放速率，

linear：
线性过渡。等同于贝塞尔曲线(0.0, 0.0, 1.0, 1.0)

ease：
平滑过渡。等同于贝塞尔曲线(0.25, 0.1, 0.25, 1.0)

ease-in：
由慢到快。等同于贝塞尔曲线(0.42, 0, 1.0, 1.0)

ease-out：
由快到慢。等同于贝塞尔曲线(0, 0, 0.58, 1.0)

ease-in-out：
由慢到快再到慢。等同于贝塞尔曲线(0.42, 0, 0.58, 1.0)

step-start：
等同于 steps(1, start)

step-end：
等同于 steps(1, end)

steps(<integer>[, [ start | end ] ]?)：
接受两个参数的步进函数。第一个参数必须为正整数，指定函数的步数。第二个参数取值可以是start或end，指定每一步的值发生变化的时间点。第二个参数是可选的，默认值为end。

cubic-bezier(<number>, <number>, <number>, <number>)：
特定的贝塞尔曲线类型，4个数值需在[0, 1]区间内

#### delay
**4.transition-delay: 0s;**

动画延迟播放，如果设置为2s，即为2s后播放动画


## transform

**2D Transform Functions：**

#### **matrix()：**

以一个含六值的(a,b,c,d,e,f)变换矩阵的形式指定一个2D变换，相当于直接应用一个[a,b,c,d,e,f]变换矩阵

#### **translate()：**

指定对象的2D translation（2D平移）。第一个参数对应X轴，第二个参数对应Y轴。如果第二个参数未提供，则默认值为0

·transform: translate(100%,20px);·

#### **translatex()：**

指定对象X轴（水平方向）的平移

·translatex(100px)  translatex(100%)  //百分比是基于自身的宽，Y为高·

#### **translatey()：**

指定对象Y轴（垂直方向）的平移

#### **rotate()：**

指定对象的2D rotation（2D旋转），需先有 <' transform-origin '> 属性的定义

`rotate(500deg)`

#### **scale()：**

指定对象的2D scale（2D缩放）。第一个参数对应X轴，第二个参数对应Y轴。
如果第二个参数未提供，则默认取第一个参数的值

`transform: scale(2,4);	`

#### **scalex()：**

指定对象X轴的（水平方向）缩放

#### **scaley()：**

指定对象Y轴的（垂直方向）缩放

#### **skew()：**

指定对象skew transformation（斜切扭曲）。第一个参数对应X轴，第二个参数对应Y轴。如果第二个参数未提供，则默认值为0

`skew(45deg)`

#### **skewx()：**

指定对象X轴的（水平方向）扭曲

相当于正方形的上下边左右偏离

#### **skewy()：**

指定对象Y轴的（垂直方向）扭曲

相当于正方形的左右边上下偏离



**3D Transform Functions：**


#### **matrix3d()：**

以一个4x4矩阵的形式指定一个3D变换

#### **translate3d()：**

指定对象的3D位移。第1个参数对应X轴，第2个参数对应Y轴，第3个参数对应Z轴，参数不允许省略

#### **translatez()：**

指定对象Z轴的平移

#### **rotate3d()：**

指定对象的3D旋转角度，其中前3个参数分别表示旋转的方向x,y,z，第4个参数表示旋转的角度，参数不允许省略

#### **rotatex()：**

指定对象在x轴上的旋转角度

#### **rotatey()：**

指定对象在y轴上的旋转角度

#### **rotatez()：**

指定对象在z轴上的旋转角度

#### **scale3d()：**

指定对象的3D缩放。第1个参数对应X轴，第2个参数对应Y轴，第3个参数对应Z轴，参数不允许省略

#### **scalez()：**

指定对象的z轴缩放

#### **perspective()：**

指定透视距离


## -webkit-filter(滤镜)

通过 -webkit-filter ，我们能够创建各种各样的图片效果。
```
•	grayscale 灰度               值为0-1之间的小数 
•	sepia 褐色　　　　　　   值为0-1之间的小数
•	saturate 饱和度　　　　 值为num
•	hue-rotate 色相旋转　　值为angle
•	invert 反色　　　　　　  值为0-1之间的小数
•	opacity 透明度　　　　　值为0-1之间的小数
•	brightness 亮度　　　　 值为0-1之间的小数
•	contrast 对比度　　　　 值为num
•	blur 模糊　　　　　　     值为length
•	drop-shadow 阴影
```

语法：

* `-webkit-filter: blur(2px);`

例子：
```
div{
 	 -webkit-filter:blur(3px) ;//模糊
```

## 弹性盒子

两种传统布局：

1、display：inline-block 行内块布局

> 优点：方便水平居中，垂直居中    
缺点：容易受“空白节点”影响，导致不必要的换行；左右对齐不方便。

2、float：left 浮动布局

> 优点：方便两端对齐    
缺点：脱离文档流，使用浮动后要“在最后一个 浮动元素的后面”清除浮动；无法实现水平居中、垂直居中。每个元素的高度也要统一，否则引发“一个元素引发的血案”。
		
**提出一种正式的“布局方法”--弹性盒子**

解决元素共行、水平居中、垂直居中、两端对齐等传统问题

使用display：flex;(除了弹性盒子之外，未来浏览器会兼容“网格布局”grid)

> 弹性盒子的属不会被继承

**order**

决定项目的排列顺序，默认值为0，数值越大，排越在后

**flex-grow**

根据弹性盒子元素所设置的扩展因子作为比率来分配剩余空间。

按比例分配剩余空间，默认值为0. 

**flex-shrink**

根据弹性盒子元素所设置的收缩因子作为比率来收缩空间。 

按比例缩减超出部分，默认值为0.

**flex-basis**

如果所有子元素的基准值之和大于剩余空间，则会根据每项设置的基准值，按比率伸缩剩余空间.




**justify-content**

设置或检索弹性盒子元素在主轴（横轴）方向上的对齐方式。 

`justify-content: space-between;`两端对齐

`justify-content: space-around;` 两端对齐，左右两端有缝隙，大小为两个元素间的一半

`justify-content:flex-start;` 弹性盒子元素将向行起始位置对齐。该行的第一个子元素的主起始位置的边界将与该行的主起始位置的边界对齐，同时所有后续的伸缩盒项目与其前一个项目对齐。 

`justify-content:flex-end;` 弹性盒子元素将向行结束位置对齐。该行的第一个子元素的主结束位置的边界将与该行的主结束位置的边界对齐，同时所有后续的伸缩盒项目与其前一个项目对齐。

`justify-content:center	` 弹性盒子元素将向行中间位置对齐。该行的子元素将相互对齐并在行中居中对齐，同时第一个元素与行的主起始位置的边距等同与最后一个元素与行的主结束位置的边距（如果剩余空间是负数，则保持两端相等长度的溢出）

**align-content**

**align-items**

**align-self**
