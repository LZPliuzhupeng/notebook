# 待整理

## TypeScript `!` 非空断言

<!-- https://cloud.tencent.com/developer/article/1610693 -->

```ts
function sayHello(name: string | undefined) {
  let sname: string = name; // Error
}

//Type 'string | undefined' is not assignable to type 'string'.
//Type 'undefined' is not assignable to type 'string'.
```

一般处理：
```ts
function sayHello(name: string | undefined) {
  let sname: string;
  if (name) {
    sname = name;
  }
}
```

使用 TypeScript 2.0 提供的非空断言操作符：
```js
function sayHello(name: string | undefined) {
  let sname: string = name!;
}
```

**在上下文中当类型检查器无法断定类型时，一个新的后缀表达式操作符` ! `可以用于断言操作对象是非null和非undefined类型。具体而言，` x! ` 将从 x 值域中排除 ` null ` 和 ` undefined `。**

### 注意事项

因为 `!` 非空断言操作符会从编译生成的 JavaScript 代码中移除，所以在实际使用的过程中，要特别注意。

**示例一：**
```ts
const a: number | undefined = undefined;
const b: number = a!;
console.log(b);


// TS 编译生成 ES5
"use strict";
const a = undefined;
const b = a;
console.log(b);
```
虽然在 TS 代码中，我们使用了非空断言，使得 `const b: number = a!`; 语句可以通过 TypeScript 类型检查器的检查。但在生成的 ES5 代码中，`!` 非空断言操作符被移除了，所以在浏览器中执行以上代码，在控制台会输出 `undefined`。


**示例二：**
```ts
type NumGenerator = () => number;

function myFunc(numGenerator: NumGenerator | undefined) {
   const num1 = numGenerator!();
}

// Uncaught TypeError: numGenerator is not a function
myFunc(undefined); // Error


// TS 编译生成 ES5
"use strict";
function myFunc(numGenerator) {
  var num1 = numGenerator();
}

// Uncaught TypeError: numGenerator is not a function
myFunc(undefined); // Error
```

浏览器中运行以上代码，在控制台会输出以下错误信息：  
```js
Uncaught TypeError: numGenerator is not a function
    at myFunc (eval at <anonymous> (main-3.js:1239), <anonymous>:3:16)
    at eval (eval at <anonymous> (main-3.js:1239), <anonymous>:6:1)
    at main-3.js:1239
```

!> 需要注意的是，非空断言操作符仅在启用 `strictNullChecks` 标志的时候才生效。当关闭该标志时，编译器不会检查 undefined 类型和 null 类型的赋值。


## for of 与 for in
`for … of`是作为ES6新增的遍历方式,允许遍历一个含有`iterator`接口的数据结构并且返回各项的值,

区别：
+ `for … of`遍历获取的是对象的键值, `for … in` 获取的是对象的键名
+ `for … in`会遍历对象的整个原型链,性能非常差不推荐使用, 而`for … of`只遍历当前对象不会遍历原型链
+ 对于数组的遍历, `for … in`会返回数组中所有可枚举的属性(包括原型链上可枚举的属性), `for … of`只返回数组的下标对应的属性值

```js
let list = [4, 5, 6];

for (let i in list) {
    console.log(i); // "0", "1", "2",
}

for (let i of list) {
    console.log(i); // "4", "5", "6"
}
```

`for..in`可以操作任何对象；它提供了查看对象属性的一种方法。 但是 `for..of`关注于迭代对象的值。内置对象Map和Set已经实现了`Symbol.iterator`方法，让我们可以访问它们保存的值。
```js
let pets = new Set(["Cat", "Dog", "Hamster"]);
pets["species"] = "mammals";

for (let pet in pets) {
    console.log(pet); // "species"
}

for (let pet of pets) {
    console.log(pet); // "Cat", "Dog", "Hamster"
}
```


## instanceof 与 isArray

```js
// 一个经典的ECMAScript问题是判断一个对象是不是数组。在只有一个网页（因而只有一个全局作用域）的情况下，使用instanceof操作符就足矣：

if (value instanceof Array){
  // 操作数组
}
// 使用instanceof的问题是假定只有一个全局执行上下文。如果网页里有多个框架，则可能涉及两个不同的全局执行上下文，因此就会有两个不同版本的Array构造函数。如果要把数组从一个框架传给另一个框架，则这个数组的构造函数将有别于在第二个框架内本地创建的数组。

// 为解决这个问题，ECMAScript提供了Array.isArray()方法。这个方法的目的就是确定一个值是否为数组，而不用管它是在哪个全局执行上下文中创建的。来看下面的例子：

if (Array.isArray(value)){
  // 操作数组
}
```

## Babel7 

## Webpack
<!-- [带你深度解锁Webpack系列(基础篇)-掘金](https://juejin.cn/post/6844904079219490830) -->

### 1.webpack 是什么？
`webpack` 是一个现代 `JavaScript` 应用程序的`静态模块打包器`，当 `webpack` 处理应用程序时，会递归构建一个依赖关系图，其中包含应用程序需要的每个模块，然后将这些模块打包成一个或多个 `bundle`。

### 2.webpack 的核心概念

+ entry：入口
+ output：输出
+ loader：模块转换器，用于把模块原内容按需求转成新的内容
+ 插件（plugins）:扩展插件，在webpack构建流程中的特定时机注入扩展逻辑来改变构建结果或做你想要做的事情

### 3.初始化项目

安装 `webpack`、`webpack-cli`

+ `npm install webpack webpack-cli -D` 

进行初始化

+ `npm init -y` 

> -y、-yes 在init的时候省去了敲回车选配置的步骤，生成的默认的package.json

使用 `npx webpack --mode=development` 进行构建，默认是 `production` 模式，我们为了更清楚得查看打包后的代码，使用 `development` 模式。

### 4.将JS转义为低版本

将JS代码向低版本转换，我们需要使用 `babel-loader`。

#### babel-loader

安装 `babel-loader`

+ `npm install babel-loader -D`

还需要配置 babel，为此我们安装一下以下依赖:

+ `npm install @babel/core @babel/preset-env @babel/plugin-transform-runtime -D`

+ `npm install @babel/runtime @babel/runtime-corejs3`

新建 `webpack.config.js`
```js
//webpack.config.js
module.exports = {
    module: {
        rules: [
            {
                test: /\.jsx?$/,
                use: ['babel-loader'],
                exclude: /node_modules/ //排除 node_modules 目录
            }
        ]
    }
}
```
?> 建议给 `loader` 指定 `include` 或是 `exclude`，指定其中一个即可，因为 `node_modules` 目录通常不需要我们去编译，排除后，有效提升编译效率。

#### 我们可以在 .babelrc 中编写 babel 的配置，也可以在 webpack.config.js 中进行配置

##### 创建一个 .babelrc

```js
{
    "presets": ["@babel/preset-env"],
    "plugins": [
        [
            "@babel/plugin-transform-runtime",
            {
                "corejs": 3
            }
        ]
    ]
}
```

##### 在webpack中配置 babel

```js
//webpack.config.js
module.exports = {
    // mode: 'development',
    module: {
        rules: [
            {
                test: /\.jsx?$/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ["@babel/preset-env"],
                        plugins: [
                            [
                                "@babel/plugin-transform-runtime",
                                {
                                    "corejs": 3
                                }
                            ]
                        ]
                    }
                },
                exclude: /node_modules/
            }
        ]
    }
}
```

这里有几点需要说明：

+ `loader` 需要配置在 `module.rules` 中，`rules` 是一个数组。
+ `loader` 的格式为:
```js
{
    test: /\.jsx?$/,//匹配规则
    use: 'babel-loader'
}
```

复制代码或者也可以像下面这样:

```js
//适用于只有一个 loader 的情况
{
    test: /\.jsx?$/,
    loader: 'babel-loader',
    options: {
        //...
    }
}
```

`test` 字段是匹配规则，针对符合规则的文件进行处理。

`use` 字段有几种写法

+ 可以是一个字符串，例如上面的 `use: 'babel-loader`'

+ `use` 字段可以是一个数组，例如处理CSS文件是，`use: ['style-loader', 'css-loader']`

+ `use` 数组的每一项既可以是字符串也可以是一个对象，当我们需要在webpack 的配置文件中对 `loader` 进行配置，就需要将其编写为一个对象，并且在此对象的 `options` 字段中进行配置，如：

```js
rules: [
    {
        test: /\.jsx?$/,
        use: {
            loader: 'babel-loader',
            options: {
                presets: ["@babel/preset-env"]
            }
        },
        exclude: /node_modules/
    }
]
```

### 5.mode

将 `mode` 增加到 `webpack.config.js` 中:

```js
module.exports = {
    //....
    mode: "development",
    module: {
        //...
    }
}
```

`mode` 配置项，告知 webpack 使用相应模式的内置优化。


`mode` 配置项，支持以下两个配置:

+ `development`：将 `process.env.NODE_ENV` 的值设置为 `development`，启用 `NamedChunksPlugin` 和 `NamedModulesPlugin`

+ `production`：将 `process.env.NODE_ENV` 的值设置为 `production`，启用 `FlagDependencyUsagePlugin`, `FlagIncludedChunksPlugin`, `ModuleConcatenationPlugin`, `NoEmitOnErrorsPlugin`, `OccurrenceOrderPlugin`, `SideEffectsFlagPlugin` 和 `UglifyJsPlugin`

使用 `npx webpack` 进行编译

### 6.处理样式文件

`webpack` 不能直接处理 `css`，需要借助 `loader`。

如果是 `.css`，我们需要的 `loader` 通常有： `style-loader`、`css-loader`，考虑到兼容性问题，还需要 `postcss-loader`，

而如果是 `less` 或者是 `sass` 的话，还需要 `less-loader` 和 `sass-loader`，这里配置一下 `less` 和 `css` 文件(`sass` 的话，使用 `sass-loader`即可):

安装依赖

+ `npm install style-loader less-loader css-loader postcss-loader autoprefixer less -D` 

```js
//webpack.config.js
module.exports = {
    //...
    module: {
        rules: [
            {
                test: /\.(le|c)ss$/,
                use: ['style-loader', 'css-loader', {
                    loader: 'postcss-loader',
                    options: {
                        plugins: function () {
                            return [
                                require('autoprefixer')({
                                    "overrideBrowserslist": [
                                        ">0.25%",
                                        "not dead"
                                    ]
                                })
                            ]
                        }
                    }
                }, 'less-loader'],
                exclude: /node_modules/
            }
        ]
    }
}
```

+ `style-loader` 动态创建 `style` 标签，将 `css` 插入到 `head` 中.

+ `css-loader` 负责处理 `@import` 等语句。

+ `postcss-loader` 和 `a`utoprefixer`，自动生成浏览器兼容性前缀

+ `less-loader` 负责处理编译 `.less` 文件,将其转为 `css`

这里，我们之间在 webpack.config.js 写了 autoprefixer 需要兼容的浏览器，仅是为了方便展示。推荐大家在根目录下创建 .browserslistrc，将对应的规则写在此文件中，除了 autoprefixer 使用外，@babel/preset-env、stylelint、eslint-plugin-conmpat 等都可以共用。

!>
`loader` 的执行顺序是从右向左执行的，也就是后面的 `loader` 先执行，上面 `loader` 的执行顺序为: `less-loader` ---> `postcss-loader` ---> `css-loader` ---> `style-loader`
当然，`loader` 其实还有一个参数，可以修改优先级，`enforce` 参数，其值可以为: `pre`(优先执行) 或 `post` (滞后执行)。

### 7.图片/字体文件处理

如果使用了本地的图片，例如:

```css
body{
    background: url('../images/thor.png');
}
```

我们可以使用 `url-loader` 或者 `file-loader` 来处理本地的资源文件。`url-loader` 和 `file-loader` 的功能类似，但是 `url-loader` 可以指定在文件大小小于指定的限制时，返回 `DataURL`。

安装依赖:

+ `npm install url-loader -D`

如提示需安装`file-loader`则安装 `npm install file-loader -D`

```js
//webpack.config.js
module.exports = {
    //...
    modules: {
        rules: [
            {
                test: /\.(png|jpg|gif|jpeg|webp|svg|eot|ttf|woff|woff2)$/,
                use: [
                    {
                        loader: 'url-loader',
                        options: {
                            limit: 10240, //10K
                            esModule: false 
                        }
                    }
                ],
                exclude: /node_modules/
            }
        ]
    }
}
```

!>
此处设置 limit 的值大小为 10240，即资源大小小于 10K 时，将资源转换为 base64，超过 10K，将图片拷贝到 dist 目录。`esModule` 设置为 false，否则，`<img src={require('XXX.jpg')} />` 会出现 `<img src=[Module Object] />`   
将资源转换为 base64 可以减少网络请求次数，但是 base64 数据较大，如果太多的资源是 base64，会导致加载变慢，因此设置 limit 值时，需要二者兼顾。

### 8.处理 html 中的本地图片

+ `npm install html-withimg-loader -D`

```js
//webpack.config.js
module.exports = {
    //...
    module: {
        rules: [
            {
                test: /.html$/,
                use: 'html-withimg-loader'
            }
        ]
    }
}
```

### 9.入口配置

***entry***

```js
//webpack.config.js
module.exports = {
    entry: './src/index.js' //webpack的默认配置
}
```

> `entry` 的值可以是一个字符串，一个数组或是一个对象。

```js
entry: [
    './src/polyfills.js',
    './src/index.js'
]
```

> `polyfills.js` 文件中可能只是简单的引入了一些 `polyfill`，例如 `babel-polyfill`，`whatwg-fetch` 等，需要在最前面被引入

### 10.出口配置

配置 `output` 选项可以控制 `webpack` 如何输出编译文件。

```js
const path = require('path');
module.exports = {
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'), //必须是绝对路径
        filename: 'bundle.js',
        publicPath: '/' //通常是CDN地址
    }
}
```

> 考虑到CDN缓存的问题，我们一般会给文件名加上 hash.

```js
//webpack.config.js
module.exports = {
    output: {
        path: path.resolve(__dirname, 'dist'), //必须是绝对路径
        filename: 'bundle.[hash].js',
        publicPath: '/' //通常是CDN地址
    }
}
```

如果你觉得 hash 串太长的话，还可以指定长度，例如 bundle.[hash:6].js。

### 11.每次打包前清空dist目录

***clean-webpack-plugin***

+ `npm install clean-webpack-plugin -D`

```js
//webpack.config.js
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = {
    //...
    plugins: [
        //不需要传参数喔，它可以找到 outputPath
        new CleanWebpackPlugin() 
    ]
}
```

?> `cleanOnceBeforeBuildPatterns`标记那个文件或子目录不删除

```js
//webpack.config.js
module.exports = {
    //...
    plugins: [
        new CleanWebpackPlugin({
            cleanOnceBeforeBuildPatterns:['**/*', '!dll', '!dll/**'] //不删除dll目录下的文件
        })
    ]
}
```

### exclude/include
<!-- [带你深度解锁Webpack系列(优化篇)](https://juejin.cn/post/6844904093463347208) -->

我们可以通过 `exclude`、`include` 配置来确保转译尽可能少的文件。顾名思义，`exclude` 指定要排除的文件，`include` 指定要包含的文件。

`exclude` 的优先级高于 `include`，在 `include` 和 `exclude` 中使用绝对路径数组，尽量避免 `exclude`，更倾向于使用 `include`。

```js
//webpack.config.js
const path = require('path');
module.exports = {
    //...
    module: {
        rules: [
            {
                test: /\.js[x]?$/,
                use: ['babel-loader'],
                include: [path.resolve(__dirname, 'src')]
            }
        ]
    },
}
```




## 更多
### bind、call、apply区别
改变`this`指向

`call`与`apply`是立即执行，传的第一个参数都为this要指向的对象。第二个参数apply的为`数组形式`，call为`多个形参形式`

`bind`不会立即执行，第二个参数以多个形参的方式

> object.call(this,a,b)   
  object.apply(this,[a,b])


### 性能优化
+ 减少请求量
+ 资源压缩
+ cdn托管静态资源
+ https

代码方面
+ 少用全局变量
+ 减少DOM操作
+ 避免使用CSS Expression（css表达式）
+ 多个变量声明合并

移动端
+ 合理使用requestAnimationFrame动画代替setTimeout
+ 适当使用touch事件代替click事件。

[参考文章](https://blog.csdn.net/allenliu6/article/details/76609929)


### vue父子组件生命周期调用顺序

+ 加载渲染过程

　　```父beforeCreate->父created->父beforeMount->子beforeCreate->子created->子beforeMount->子mounted->父mounted```

+ 子组件更新过程

　　```父beforeUpdate->子beforeUpdate->子updated->父updated```

+ 父组件更新过程

　　```父beforeUpdate->父updated```

+ 销毁过程

　　```父beforeDestroy->子beforeDestroy->子destroyed->父destroyed```


### Http请求

<!-- 1.域名解析

2.发起TCP的3次握手

4.服务器端响应http请求，浏览器得到html代码

5. 浏览器解析html代码，并请求html代码中的资源

6.浏览器对页面进行渲染呈现给用户 -->

-------------------

<!-- （1）建立TCP连接

（2） Web浏览器向Web服务器发送请求命令

（3） Web服务器应答

（4）Web服务器关闭TCP连接

（5）浏览器接受到服务器响应的数据 -->

--------------------

1、TCP建立连接

HTTP协议是基于TCP协议来实现的，因此首先就是要通过TCP三次握手与服务器端建立连接，一般HTTP默认的端口号为80；

2、浏览器发送请求命令

在与服务器建立连接后，Web浏览器会想服务器发送请求命令

3、浏览器发送请求头消息

在浏览器发送请求命令后，还会发送一些其它信息，最后以一行空白内容告知服务器已经完成头信息的发送；

4、服务器应答

在收到浏览器发送的请求后，服务器会对其进行回应，应答的第一部分是协议的版本号和应答状态码；

5、服务器回应头信息

与浏览器端同理，服务器端也会将自身的信息发送一份至浏览器

6、服务器发送数据

在完成所有应答后，会以Content-Type应答头信息所描述的格式发送用户所需求的数据信息

7、断开TCP连接

在完成此次数据通信后，服务器会通过TCP四次挥手主动断开连接。但若此次连接为长连接，那么浏览器或服务器的头信息会加入keep-alive的信息，会保持此连接状态，在有其它数据发送时，可以节省建立连接的时间；


### 算法的时间复杂度的和空间复杂度

称n为问题的规模

+ 时间复杂度
    - 时间频度

        一个算法`花费的时间`与算法中语句的`执行次数`成正比例，哪个算法中语句执行次数多，它花费时间就多。一个算法中的`语句执行次数`称为`语句频度或时间频度`。

        记为T(n)。

    - 时间复杂度

        算法的时间复杂度是指执`行算法所需要的计算工作量`。

        一般情况下，算法中基本操作重复执行的次数是问题规模n的某个函数，用T(n)表示，若有某个辅助函数f(n),存在一个正常数c使得fn*c>=T(n)恒成立。记作T(n)=O(f(n)),称O(f(n)) 为算法的渐进时间复杂度，简称时间复杂度。

        在各种不同算法中，若算法中语句执行次数为一个常数，则时间复杂度为O(1),另外，在时间频度不相同时，时间复杂度有可能相同，如T(n)=n^2+3n+4与T(n)=4n^2+2n+1它们的频度不同，但时间复杂度相同，都为O(n^2)。

+ 空间复杂度

    与时间复杂度类似，空间复杂度是指算法在计算机内执行时所需存储空间的度量。记作:S(n)=O(f(n))

    算法执行期间所需要的存储空间包括3个部分：
        - 算法程序所占的空间；
        - 输入的初始数据所占的存储空间；
        - 算法执行过程中所需要的额外空间。

    在许多实际问题中，为了减少算法所占的存储空间，通常采用压缩存储技术。


### diff算法

[Vue中的Diff算法-csdn-Hayden丶](https://blog.csdn.net/qq_34179086/article/details/88086427)


### 回调地狱

回调函数嵌套引起。

使用Promise、async await 解决


### 雪碧、减少图片请求数量有什么用？

1、减少网站的HTTP请求数量。可以减小建立连接的消耗。减少对服务器的请求次数，降低服务器压力。

2、减少体积，由所需图片拼成的一张图片的尺寸会明显小于所有图片拼合前的大小。单张的图片只有相关的一个色表，而单独分割的每一张图片都有自己的一个色表，这就增加了总体的大小。


### 页面渲染过程


### var、let、const

+ var声明会被拿到函数或全局作用域的顶部，位于作用域中所有代码之前。这个现象叫作“提升”（hoisting）。

+ let与var的另一个不同之处是在同一作用域内不能声明两次。重复的var声明会被忽略，而重复的let声明会抛出SyntaxError。

+ ES6同时还增加了const关键字。使用const声明的变量必须同时初始化为某个值。一经声明，在其生命周期的任何时候都不能再重新赋予新值。

+ const声明只应用到顶级原语或者对象。换句话说，赋值为对象的const变量不能再被重新赋值为其他引用值，但对象的键则不受限制。



### 执行上下文与作用域



### mixins 

当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”。

+ 数据对象在内部会进行递归合并，并在发生冲突时以组件数据优先。

+ 同名钩子函数将合并为一个数组，因此都将被调用。另外，混入对象的钩子将在组件自身钩子之前调用。

+ 值为对象的选项，例如 methods、components 和 directives，将被合并为同一个对象。两个对象键名冲突时，取组件对象的键值对。


### vuex

+ State

+ Getter

+ Mutation

+ Action

+ Module

```js
const moduleA = {
  namespaced: false,
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

命名空间。namespaced: true
```js
dispatch('moduleA/actionsItem')
```

+ 辅助函数
    - mapGetters
    - mapMutations
    - mapActions

```js
import { mapGetters } from 'vuex'

export default {
  computed: {
    ...mapGetters([
      'doneTodosCount',
    ])
  }
}
```

### axios

[axios中文文档](http://www.axios-js.com/zh-cn/docs/)


#### 案例

```js
axios.get('/user?ID=12345')

axios.get('/user', {
    params: {
      ID: 12345
    }
  })
```

```js
axios.post('/user', {
    firstName: 'Fred',
    lastName: 'Flintstone'
  })
```

**`axios(config)`**
```js
axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flintstone'
  }
});
```

**`axios(url[, config])`**
```js
axios('/user/12345');
```

#### 请求方法的别名

+ axios.request(config)
+ axios.get(url[, config])
+ axios.delete(url[, config])
+ axios.head(url[, config])
+ axios.options(url[, config])
+ axios.post(url[, data[, config]])
+ axios.put(url[, data[, config]])
+ axios.patch(url[, data[, config]])


!> 在使用别名方法时， url、method、data 这些属性都不必在配置中指定。

#### 创建实例

```js
const instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
```

##### 请求配置

```js
{
   // `url` 是用于请求的服务器 URL
  url: '/user',

  // `method` 是创建请求时使用的方法
  method: 'get', // default

  // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
  baseURL: 'https://some-domain.com/api/',

  // `transformRequest` 允许在向服务器发送前，修改请求数据
  // 只能用在 'PUT', 'POST' 和 'PATCH' 这几个请求方法
  // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
  transformRequest: [function (data, headers) {
    // 对 data 进行任意转换处理
    return data;
  }],

  // `transformResponse` 在传递给 then/catch 前，允许修改响应数据
  transformResponse: [function (data) {
    // 对 data 进行任意转换处理
    return data;
  }],

  // `headers` 是即将被发送的自定义请求头
  headers: {'X-Requested-With': 'XMLHttpRequest'},

  // `params` 是即将与请求一起发送的 URL 参数
  // 必须是一个无格式对象(plain object)或 URLSearchParams 对象
  params: {
    ID: 12345
  },

   // `paramsSerializer` 是一个负责 `params` 序列化的函数
  // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // `data` 是作为请求主体被发送的数据
  // 只适用于这些请求方法 'PUT', 'POST', 和 'PATCH'
  // 在没有设置 `transformRequest` 时，必须是以下类型之一：
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - 浏览器专属：FormData, File, Blob
  // - Node 专属： Stream
  data: {
    firstName: 'Fred'
  },

  // `timeout` 指定请求超时的毫秒数(0 表示无超时时间)
  // 如果请求话费了超过 `timeout` 的时间，请求将被中断
  timeout: 1000,

   // `withCredentials` 表示跨域请求时是否需要使用凭证
  withCredentials: false, // default

  // `adapter` 允许自定义处理请求，以使测试更轻松
  // 返回一个 promise 并应用一个有效的响应 (查阅 [response docs](#response-api)).
  adapter: function (config) {
    /* ... */
  },

 // `auth` 表示应该使用 HTTP 基础验证，并提供凭据
  // 这将设置一个 `Authorization` 头，覆写掉现有的任意使用 `headers` 设置的自定义 `Authorization`头
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  },

   // `responseType` 表示服务器响应的数据类型，可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
  responseType: 'json', // default

  // `responseEncoding` indicates encoding to use for decoding responses
  // Note: Ignored for `responseType` of 'stream' or client-side requests
  responseEncoding: 'utf8', // default

   // `xsrfCookieName` 是用作 xsrf token 的值的cookie的名称
  xsrfCookieName: 'XSRF-TOKEN', // default

  // `xsrfHeaderName` is the name of the http header that carries the xsrf token value
  xsrfHeaderName: 'X-XSRF-TOKEN', // default

   // `onUploadProgress` 允许为上传处理进度事件
  onUploadProgress: function (progressEvent) {
    // Do whatever you want with the native progress event
  },

  // `onDownloadProgress` 允许为下载处理进度事件
  onDownloadProgress: function (progressEvent) {
    // 对原生进度事件的处理
  },

   // `maxContentLength` 定义允许的响应内容的最大尺寸
  maxContentLength: 2000,

  // `validateStatus` 定义对于给定的HTTP 响应状态码是 resolve 或 reject  promise 。如果 `validateStatus` 返回 `true` (或者设置为 `null` 或 `undefined`)，promise 将被 resolve; 否则，promise 将被 rejecte
  validateStatus: function (status) {
    return status >= 200 && status < 300; // default
  },

  // `maxRedirects` 定义在 node.js 中 follow 的最大重定向数目
  // 如果设置为0，将不会 follow 任何重定向
  maxRedirects: 5, // default

  // `socketPath` defines a UNIX Socket to be used in node.js.
  // e.g. '/var/run/docker.sock' to send requests to the docker daemon.
  // Only either `socketPath` or `proxy` can be specified.
  // If both are specified, `socketPath` is used.
  socketPath: null, // default

  // `httpAgent` 和 `httpsAgent` 分别在 node.js 中用于定义在执行 http 和 https 时使用的自定义代理。允许像这样配置选项：
  // `keepAlive` 默认没有启用
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // 'proxy' 定义代理服务器的主机名称和端口
  // `auth` 表示 HTTP 基础验证应当用于连接代理，并提供凭据
  // 这将会设置一个 `Proxy-Authorization` 头，覆写掉已有的通过使用 `header` 设置的自定义 `Proxy-Authorization` 头。
  proxy: {
    host: '127.0.0.1',
    port: 9000,
    auth: {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  },

  // `cancelToken` 指定用于取消请求的 cancel token
  // （查看后面的 Cancellation 这节了解更多）
  cancelToken: new CancelToken(function (cancel) {
  })
}
```

#### 配置的优先顺序

配置会以一个优先顺序进行合并。这个顺序是：在 lib/defaults.js 找到的库的默认值，然后是实例的 defaults 属性，最后是请求的 config 参数。后者将优先于前者。
```js
// 使用由库提供的配置的默认值来创建实例
// 此时超时配置的默认值是 `0`
var instance = axios.create();

// 覆写库的超时默认值
// 现在，在超时前，所有请求都会等待 2.5 秒
instance.defaults.timeout = 2500;

// 为已知需要花费很长时间的请求覆写超时设置
instance.get('/longRequest', {
  timeout: 5000
});
```

#### 拦截器

```js
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```


### 闭包

闭包指的是`那些引用了另一个函数作用域中变量`的`函数`，通常是在嵌套函数中实现的。闭包能够读取其他函数内部变量的函数。

在本质上，闭包是将函数内部和函数外部连接起来的桥梁。


```js
function a(){
    let i=0
    return ()=>alert(++i)
}
let b=a()
b();
```

**运用**

+ 私用变量

+ 节点循环绑定click事件（现使用let声明变量可解决）



### 浏览器文件缓存

#### Cache-Control

+ General    
    Status Code: 200  (from disk cache)

+ Response Headers    
    cache-control: max-age=31536000


| 允许的指令  |	 值  |	 含义  |
| ---- | ------ | ---- |
| max-age  |  delta-seconds  |	客户端不愿意接受age超过这个值的缓存。并且不接受过期缓存，除非max-stale存在。  |
| max-stale  |  delta-seconds  |  如果有值，客户端可以接受过期时间不超过指定值的缓存 如果没有值，客户端愿意接受过期缓存而无论过期过久。  |
| min-fresh  |  delta-seconds  |  客户端愿意接受一个新鲜度不小于当前age加上指定时间的响应。简单说在指定的后续一段时间内不会过期的响应。  |
| no-cache  |  无  |  客户端示意缓存，在使用缓存的时候必须进行校验。  |
| no-store  |  无  |  客户端示意缓存，不要存储本次请求的响应。但是对于已经缓存的内容则没有影响。  |
| no-transform  |  无  |  不得对资源进行转换或转变。Content-Encoding, Content-Range, Content-Type等HTTP头不能由代理修改。例如，非透明代理可以对图像格式进行转换，以便节省缓存空间或者减少缓慢链路上的流量。 no-transform指令不允许这样做。  |
| only-if-cached  |  无  |  客户端只接受缓存给出的响应，如果缓存没有命中应该返回一个504  |


| 允许的指令  |  值  |  含义  |
| ---- | ------ | ---- |
| must-revalidate  |  无  |  一旦缓存过期，必须向源服务器进行校验，不得使用过期内容。如果无法连接必须返回504。  |
| proxy-revalidate  |    |  与must-revalidate相同，但仅对公共缓存生效。  |
| no-cache  |  field-name  |  如果值，在没有成功通过源站校验的情况下不得使用缓存。 有值，在进行验证的时候不要发送值指示的头域。 如Cache-Control: no-cache="set-cookie,set-cookie2"，表示不要携带cookie进行验证。  |
| no-store  |  无  |  不要缓存当前请求的响应  |
| no-transform  |  无  |  与请求头语义相同  |
| public  |  无  |  任何缓存都可以进行缓存，即使响应默认是不可缓存或仅私有缓存可存的情况。  |
| private  |  field-name  |  没有值，公有缓存不可存储；即使默认是不可缓存的，私有缓存也可以存储 。有值，将无值时的作用，限制到指定头字段上。公有有缓存不可存储指定的头字段，而其他字段可以缓存。  |
| max-age  |  delta-seconds  |  在经过指定时间后将过期  |
| s-maxage  |  delta-seconds  |  指定响应在公共缓存中的最大存活时间，它覆盖max-age和expires字段。  |


#### ETag

Etag 是URL的Entity Tag，用于标示URL对象是否改变，区分不同语言和Session等等。具体内部含义是使服务器控制的，就像Cookie那样。



### XSS、CSRF攻击与防范

**什么是XSS攻击？**

*XSS攻击全称`跨站脚本攻击`*

XSS是一种在web应用中的计算机安全漏洞，它允许恶意web用户将代码植入到提供给其它用户使用的页面中。

防范

+ 1.不要使用 innerHTML，改成 innerText，script 就会被当成文本，不执行
+ 2.如果你一样要用 innerHTML，字符过滤
    - 把 < 替换成 <
    - 把 > 替换成 >
    - 把 & 替换成 &
    - 把 ' 替换成 '
    - 把 ' 替换成 "
    - 代码 div.innerHTML = userComment.replace(/>/g, '<').replace...
+ 3.使用 CSP Content Security Policy

**什么是CSRF攻击？**

*CSRF（Cross-site request forgery）`跨站请求伪造`*

CSRF通过伪装来自受信任用户的请求来利用受信任的网站。

防范
+ 1.尽量对要修改数据的请求使用post而不是get
+ 2.给每一次用户登陆分配一个临时token，用服务端的setCookie头将此token种入用户cookie中，每次请求比对用户方token与服务器端token是否吻合。
+ 3.服务器禁止跨域请求
+ 4.及时清除用户的无效cookie

> 举例说明    
1.用户在 qq.com 登录    
2.用户切换到 hacker.com（恶意网站）    
3.hacker.com 发送一个 qq.com/add_friend 请求，让当前用户添加 hacker 为好友。    
4.用户在不知不觉中添加 hacker 为好友。    
5.用户没有想发这个请求，但是 hacker 伪造了用户发请求的假象。    


### （函数柯里化）实现add(2,1)(3,5) => (2+1)*(3+5)=24

```js
function add() {
    // 第一次执行时，arguments为第一次调用的参数
    var _args = [ [...arguments].reduce((a,b)=>a+b) ];

    // 在内部声明一个函数，利用闭包的特性保存_args并收集所有的参数值
    var _adder = function() {
        _args.push( [...arguments].reduce((a,b)=>a+b) );
        console.log('----------------')
        return _adder;
    };
    // 利用toString隐式转换的特性，当最后执行时隐式转换，并计算最终的值返回
    _adder.toString = function () {
        return _args.reduce((a,b)=> a*b);
    }
    
    console.log('+++++++++++++++++++')
    return _adder;
}

console.log(  add(2,1)(3,5)  )  // (2+1)*(3+5)=24
console.log(  add(2)(3,5)(1,2)  )  // 2*(3+5)*(1+2)=48

```


输出：
```js
+++++++++++++++++++
----------------
ƒ 24
+++++++++++++++++++
----------------
----------------
ƒ 48
```


### get和post

GET和POST是什么？HTTP协议中的两种发送请求的方法。

HTTP是基于TCP/IP的关于数据如何在万维网中如何通信的协议。

HTTP的底层是TCP/IP。所以GET和POST的底层也是TCP/IP，也就是说，GET/POST都是TCP链接。GET和POST能做的事情是一样一样的。你要给GET加上request body，给POST带上url参数，技术上是完全行的通的。 


+ （大多数）浏览器通常都会限制url长度在2K个字节，而（大多数）服务器最多处理64K大小的url。

+ GET请求会被浏览器主动cache，而POST不会，除非手动设置。

+ GET请求只能进行url编码，而POST支持多种编码方式。

+ GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。

+ GET请求在URL中传送的参数是有长度限制的，而POST没有。

+ 对参数的数据类型，GET只接受ASCII字符，而POST没有限制。

+ GET参数通过URL传递，POST放在Request body中。


<!-- [参考](https://www.cnblogs.com/logsharing/p/8448446.html) -->


<!-- ### 排序算法（待完善）

混排队列、插入排序、选择排序、冒泡排序、快速排序、归并排序、希尔排序

[用HTML5实现的各种排序算法的动画比较](https://www.webhek.com/post/comparison-sort.html) -->

### 任务队列试题示例

```js
setTimeout(() => {
  console.log(1)
}, 0);
new Promise((resolve)=>{
  console.log(2)
  for(let i = 1;i<=100;i++){

  }
  console.log(3)
  resolve()
})
.then(()=>{
  console.log(4)
})
console.log(5)

//23541
```

### vue组件调用自身

```vue
<template>
    <div>
        <my-tree></my-tree>
    </div>
</template>

<script>

export default {
  name: "myTree"
}
```

### http缓存

http缓存都是从第二次请求开始的。（get请求）

第一次请求资源时，服务器返回资源，并在respone header头中回传资源的缓存参数；

第二次请求时，浏览器判断这些请求参数，命中强缓存就直接200，否则就把请求参数加到request header头中传给服务器，看是否命中协商缓存，命中则返回304，否则服务器会返回新的资源。

<!-- [http缓存](https://www.jianshu.com/p/227cee9c8d15) -->


+ 强缓存
    - Cache-Control（优先级从上到下）
        - Pragma：no-cache（不直接使用缓存，根据新鲜度来使用缓存，http1.1已废弃）
        - Cache-Control
            - no-cache（不直接使用缓存，根据新鲜度来使用缓存）
            - no-store（不使用缓存，每次都是请求下载资源）
            - max-age（xx秒，缓存时长）
            - public/private
            - must-revalidate
    - Expires。GMT过期时间。
    - 强制缓存在缓存数据未失效的情况下（即Cache-Control的max-age没有过期或者Expires的缓存时间没有过期），那么就会直接使用浏览器的缓存数据，不会再向服务器发送任何请求。
    - 强制缓存生效时，http状态码为200。
    - Ctrl + F5强制刷新
    - from memory cache (从内存中获取/一般缓存更新频率较高的js、图片、字体等资源)
    - from disk cache (从磁盘中获取/一般缓存更新频率较低的js、css等资源)


+ 协商缓存
    - 当第一次请求时服务器返回的响应头中没有Cache-Control和Expires或者Cache-Control和Expires过期还或者它的属性设置为no-cache时(即不走强缓存)，那么浏览器第二次请求时就会与服务器进行协商，与服务器端对比判断资源是否进行了修改更新。如果服务器端的资源没有修改，那么就会返回304状态码，告诉浏览器可以使用缓存中的数据。如果数据有更新就会返回200状态码，服务器就会返回更新后的资源并且将缓存信息一起返回。
    - 当浏览器第一次向服务器发送请求时，会在响应头中返回协商缓存的头属性：ETag和Last-Modified,其中ETag返回的是一个hash值，Last-Modified返回的是GMT格式的最后修改时间。然后浏览器在第二次发送请求的时候，会在请求头中带上`与ETag对应的If-Not-Match`，其值就是响应头中返回的ETag的值，`Last-Modified对应的If-Modified-Since`。服务器在接收到这两个参数后会做比较，如果返回的是304状态码，则说明请求的资源没有修改，浏览器可以直接在缓存中取数据，否则，服务器会直接返回数据。
    - ETag/If-Not-Match（http1.1）
    - Last-Modified/If-Modified-Since（http1.0）





## 发现问题

### jq几种绑定事件的方式

1、bind()函数只针对已经存在的元素进行事件的设置。live(),on.delegate()均支持未来新添加元素的事件设置。

2、bind()函数在jquery1.7版本以前比较受推崇，1.7版本出来之后，官方已经不推荐用bind()，
替代函数为on(),这也是1.7版本新添加的函数，同样，可以用来代替live()函数，live()函数在1.9版本已经删除；

3、bind()、.live()或.delegate()，它们实际上调用的都是.on()方法

4、移除.on()绑定的事件用.off()方法。

5、bind()支持Jquery所有版本，live()支持jquery1.9-，delegate()支持jquery1.4.2+，on()支持jquery1.7+


### body和header中引入js文件的区别
**加载顺序不同**

header中的在页面加载之前就会进行预加载，body中的会在按照页面从上到下的顺序进行加载。


**功能不同**

heaer中的通常用来加载一些外部的JavaScript文件，从而提高效率；

body中的主要用来实现一些页面内容的动态创建，所以向获取DOM节点这种操作必须在目标节点对应的标签被加载后才可以进行，否则是获取不到的。

> 总结:
外部js文件的加载放在`header`中的`<script>`标签中    
动态创建内容的代码放在`body`中的`<script>`标签中    
函数放在`header`或者`body`中的`<script>`标签中没有区别    

### window.onload和document.ready

这两种事件都代表的是页面文档加载时触发的，但两者之间区别在于：

ready 事件的触发，表示文档结构已经加载完成（不包含图片等非文字媒体文件）。

onload 事件的触发，表示页面包含图片等文件在内的所有元素都加载完成。

### js三大经典面试题
1、原型与原型链
2、new操作符，在实例化对象中做了什么
3、如何理解this

### Vue事件修饰符，`.self`和`.stop`的区别
```
<div class="div1" @click:"click1">
	<div class="div2"  @click:"click2">
		<div class="div3"  @click:"click3">
		</div>
	</div>
</div>
```

> 点击div3时，因为事件冒泡，div3、div2、div1的点击事件都会执行。    
当`@click.self:"click2"`，点击div3，`div2不会执行`，div1执行，    
当`@click.stop:"click3"`，点击div3，`div2和div1都不会执行`.

### JQ wrap()和wrapInner()的区别
**wrap()方法**
```
<p>我是占位子的。</p>
<p>我是占位子的。</p>
 ```
 ```
 $("p").wrap("<strong></strong>");
 ```
 结果
```
<strong>
	<p>我是占位子的。</p>
</strong>
<strong>
	<p>我是占位子的。</p>
</strong>
```

**wrapInner()方法**
```
<p>我是占位子的。</p>
<p>我是占位子的。</p>
 ```
 ```
 $("p").wrapInner("<strong></strong>");
 ```
 结果
```
<p><strong>我是占位子的。</strong></p>
<p><strong>我是占位子的。</strong></p>
```
### this和e.target的区别
js中事件是会冒泡的，所以this是可以变化的，但event.target不会变化，它永远是直接接受事件的目标DOM元素；

例如：
```
		<ul>
            <li>这是公告标题1</li>
            <li>这是公告标题2</li>
            <li>这是公告标题3</li>
            <li>这是公告标题4</li>
        </ul>
```
```
			var ul=document.querySelector("ul");
			ul.addEventListener("click",function(e){
				console.log(this);
				console.log(e.target)
			})
```
**this指向的一直是ul，e.target指向的可以是li**

### jQuery中attr()和prop()的区别
1.所有的DOM对象都有一个attribute属性，而prop可以操作属性，所以也可以操作属性节点

2.官方推荐：在操作属性节点时，具有true和false两个属性的属性节点，
如checked，selected或者disabled使用prop()，其他使用attr()

因为，如果具有true和false两个属性的属性节点，如果没有编写默认attr返回undefined，而prop返回false

> 注意：attr()获取属性节点时，只会获取到所有元素中第一个元素的属性节点，但是设置时是给所有找到的元素设置属性节点，prop（）也是一样的



### jQuery 的attr()与css()的区别
**attr是用来获得或设置标签属性的值**
```
var myId = $("#myId");

myId.attr("data-name", "baidu");

// 设置属性名data-name，值baidu

// 结果为 ： <div id="myId" data-name="baidu"></div>

var attr = myId.attr("data-name"); // 获取
```
**css是设置元素的style样式的**
```
var myId = $("#myId");

myId.css("background-color", "red"); // 设置背景颜色为红色

var bg = myId.css("background-color"); // 获取背景颜色
```
### parentNode和parentElement区别
```
<div></div>
<script>
var div=document.querySelector("div");
console.log(div.parentNode..parentNode.nodeName);  //html
console.log(div.parentElement.parentElement.nodeName); //html
</script>
```

如果再查找上一级
```
<div></div>
<script>
var div=document.querySelector("div");
console.log(div.parentNode.parentNode.parentNode.nodeName);  //#document
console.log(div.parentElement.parentElement.parentElement.nodeName);
 //报错Uncaught TypeError: Cannot read property 'nodeName' of null
</script>
```
这里的唯一区别就出来了，因为parentElement找的是元素，因此当找到根部document时候就是出现值为null的报错，而且parentNode找的是节点，当然就可以显示出来了！ 


### childNodes和children的区别

childNodes指的是返回当前元素子节点的所有类型节点，其中连空格和换行符都会默认文本节点， 

childern指的是返回当前元素的所有元素节点

> 一般情况用children

### align-items和align-content的区别
两个属性都是对垂直方向的对齐

align-items是对每个元素进行设置，它是用来设置每个flex元素在侧轴上的默认对齐方式。 

align-content是对整体的设置，而且在单行的元素内无效	；align-content属性只适用于多行的flex容器，并且当侧轴上有多余空间使flex容器内的flex线对齐。 

### in和instanceof
* **in**

in运算符用来判断对象是否拥有给定属性.

* **instanceof**

instanceof 运算符判断一个对象是否是另一个对象的实例.

### offsetWidth和clientWidth

```
 var dOffsetWidth = divObj.offsetWidth;
 //返回元素的宽度（包括元素宽度、内边距和边框，不包括外边距）
```
```
 var dClientWidth = divObj.clientWidth;
 //返回元素的宽度（包括元素宽度、内边距，不包括边框和外边距）
```
```
var dWidth = divObj.style.width;
//返回元素的宽度（包括元素宽度，不包括内边距、边框和外边距）
```

```
 var dscrollWidth = divObj.scrollWidth;
 //返回元素的宽度（包括元素宽度、内边距和溢出尺寸，不包括边框和外边距），无溢出的情况，与clientWidth相同
 ```
### addEventListener和addListener的区别

**addListener是用于鼠标，键盘等特殊元素的一些监听**

**addEventListener是对组件监听的**

> 要注意的是div必须放到js前面才行

一般情况下，如果给一个dom对象绑定同一个事件，只有最后一个会生效，比如：

复制代码 代码如下:
```
document.getElementById("btn").onclick = method1;
document.getElementById("btn").onclick = method2;
document.getElementById("btn").onclick = method3;

那么将只有method3生效。
```

如果是Mozilla系列，用addEventListener可以让多个事件按顺序都实现，比如：

复制代码 代码如下:
```
var btn1Obj = document.getElementById("btn1");
//element.addEventListener(type,listener,useCapture);
btn1Obj.addEventListener("click",method1,false);
btn1Obj.addEventListener("click",method2,false);
btn1Obj.addEventListener("click",method3,false);

执行顺序为method1->method2->method3
```

### slice和concat克隆数组

**一、数组浅拷贝**

如下代码，如果只是简单才用赋值的方法，那么我们只要更改其中的任何一个，然后其他的也会跟着改变，这就导致了问题的发生

```
var arr1 = ["red","yellow","black"];
var arr2 = arr1;
arr2[1] = "green";
console.log("数组的原始值：" + arr1 );
console.log("数组的新值：" + arr2);
```
> 结论：像上面的这种直接赋值的方式就是<span>数组的浅拷贝</span>，浅拷贝改变其中一个数组，另外一个数组也会跟着改变。

**二、数组深拷贝方法**

slice方法

```
var arr1 = ["1","2","3"];
var arr2 = arr1.slice(0);
arr2[1] = "9";
console.log("数组的原始值：" + arr1 );  //1,2,3
console.log("数组的新值：" + arr2 );    //1,9,3
```

concat方法

```
var arr1 = ["1","2","3"];
var arr2 = arr1.concat();
arr2[1] = "9";
console.log("数组的原始值：" + arr1 );	//1,2,3
console.log("数组的新值：" + arr2 );		//1,9,3
```

**三、slice,concat方法的局限性**

```
var a1=[["1","2","3"],"2","3"],a2;
a2=a1.slice(0);
a1[0][0]=0;     //改变a1第一个元素中的第一个元素
console.log(a2[0][0]);  //影响到了a2
```
> 从上面两个例子可以看出，由于数组内部属性值为引用对象，因此使用slice和concat对对象数组的拷贝，整个拷贝还是浅拷贝，拷贝之后数组各个值的指针还是指向相同的存储地址。    
<span>因此，slice和concat这两个方法，仅适用于对不包含引用对象的一维数组的深拷贝</span>

###  img和background-img区别

1、 是否占位

`background-image`是背景图片，是css的一个样式，不占位

`<img />`是一个块状元素，它是一个图片，是html的一个标签，占位

2、否可操作

`background-image`是只能看的，只能设置`background-position, background-attachment,  background-repeat`

`<img />`是一个document对象，它是可以操作的。比如更换img src的路径可以达到更换图片的目的，也可以移动它的位置，从document中移除等等操作。所以如果是装饰性的图片就使用background-img，如果和文体内容很相关就使用img。

3、 加载顺序不同

在网页加载的过程中，以css背景图存在的图片background-image会等到结构加载完成（网页的内容全部显示以后）才开始加载，

而html中的标签img是网页结构（内容）的一部分会在加载结构的过程中加载，换句话讲，网页会先加载标签img的内容，再加载背景图片background-image，如果你用引入了一个很大的图片，那么在这个图片下载完成之前，img后的内容都不会显示。而如果用css来引入同样的图片，网页结构和内容加载完成之后，才开始加载背景图片，不会影响你浏览网页内容。

 
4、从解析机制看

`Img属于html标签，background是css方法。`一个页面由html、css、js组成，按照浏览器解析机制，html标签优先解析。

大家都知道css文件会放在head加载，但是这并不意味着它会立即执行，而是在html加载完后才执行的。所以重要的元素，如logo就应该使用img。

 如果仅仅是为了显示一张图片，比如banner、广告图等，建议采用background方式。因为不重要的自动往后排，避免占用带宽，造成数据阻塞。

如果图片很多，这里又出现一个新的问题，不同的浏览器支持的并发加载数量是不一样的，（最新测试）IE是10个，FF是10个，图片多，就会出现严重的延迟或者404，因为图片加载慢会影响到页面主要数据呈现，那么让用户看到的都是空白，你好意思让他继续等下去吗！所以，如果不采用lazyload还是采用background比较好。

<span>Img标签优点是自闭和，可以避免空标签出现（空标签也是w3c验证的内容之一）。</span>采用background方式就会出现空标签，bootstrap中的icon都是用i标签加入的，而i标签中是空内容，这样就产生了空标签（并不是说这样做不好，利弊等会聊）。

 

5、从SEO角度看

    刚才说了，Img标签是自闭和的，不能添加文本内容，但是，Img有一个alt属性，而且这个属性在w3c标准中，
    
    要求必须有，这样做的优点还是很多的。

第一，图片比较大，或者用户网速比较窝火的时候，加载失败了，至少还有文字提示告诉用户这里是个什么内容的图片。如果用户非要看这个图，那就多刷几次去加载。另外，alt属性有利于辅助阅读，尤其是对盲人朋友，他们使用阅读器浏览页面，如果没有文本提示，就太不厚道了。

第二，alt属性有利于SEO，搜索引擎实现很好的图像识别还是有一段路要走。

当然缺点也是有滴：

第一，Img加载的图片是通过src拿到的，如果HTML代码不允许修改，那怎么换图，只有同名文件替换，但是有可能遇到304状态，需要服务器端做相应的设置。这时background的优点就出来了，换皮肤就是这个栗子。

第二，如果图片显示区域空间大小是预留的，那么图片必须和预留的空间一致，否则图片要么被拉伸要么被压缩，都不是等比例操作。当然，避免这种问题就需要前端和视觉设计师遵守一定的规范。

 

6、语义化角度

    Background和语义化不沾边了，Img是HTML标签，语义明确。

 

> 建议：重要的需要优先加载的图片最好采用img。不重要的图片最好采用background。

 

做SEO是最方便的还是background，图片是放在背景上的，前景写内容，两不误。如果不想让内容显示出来，就加text-indent， -99999你懂的。（以前有这种玩法，是搜索引擎算法单一的年代，关键字比重高 排名就靠前）。

 

刚才提了一下bootstrap的background方法，bootstrap的做法是用background设置icon，icon的使用太灵活了，所以必须模块化，语义化先靠边站，鱼与熊掌不可兼得。PS：bootstrap v3之后采用了icon font

其次icon的重要性真不高，放在最后加载还减少了带宽占用量，提高PV速度。

img标签语义很明确不能乱用，所以bootstrap采用无语义的i标签来设置icon也挺好。PS：自己项目中使用无语义标签要注意是否向前兼容，要关注HTML5中的定义。


### toLowerCase和toLocaleLowerCase的区别

ECMAScript中涉及字符串大小写转换的方法有4个：toLowerCase()、toLocaleLowerCase()、toUpperCase()和toLocaleUpperCase()。

其中，toLowerCase()和toUpperCase()是两个经典的方法，借鉴自java.lang.String中的同名方法。
而toLocaleLowerCase()和toLocaleUpperCase()方法则是针对特定地区的实现。
对有些地区来说，针对地区的方法与其通用方法得到的结果相同，但少数语言(如土耳其语言)会为Unicode大小写转换应用特殊的规则，这时候就必须使用针对地区的方法来保证实现正确的转换。

以下是几个例子：
```
var stringValue = "hello world";

alert(stringValue.toLocaleUpperCase()); //"HELLO WORLD"

alert(stringValue.toUpperCase()); //"HELLO WORLD"

alert(stringValue.toLocaleLowerCase()); //"hello world"

alert(stringValue.toLowerCase()); //"hello world"123456
```
代码laycode - v1.1
以上代码调用的toLocaleUpperCase()和toUpperCase()都返回了“HELLO WORLD”，
就像调用toLocaleLowerCase()和toLowerCase()都返回“hello world”一样。

一般来说，在不知道自己的代码将在那种语言环境中运行的情况下，还是使用针对地区的方法更稳妥一些。

### valueOf( )和tostring（）

**toString()**

toString()函数的作用是返回object的字符串表示，JavaScript中object默认的toString()方法返回字符串”[object Object]“。
定义类时可以实现新的toString()方法，从而返回更加具有可读性的结果。JavaScript对于数组对象、函数对象、正则表达式对象以及Date日期对象均定义了更加具有可读性的toString()方法：

1.array的toString()方法将返回以逗号分隔的数组成员。比如，[1,2,3].toString()会返回字符串”1,2,3″。

2.function的toString()方法将返回函数的文本定义。比如，(function(x){return x*2;}).toString()会返回字符串”function(x){return x*2;}”。

3.RegExp的toString()方法与function的toString()方法类似，将返回正则表达式的文本定义。比如，/\d+/g.toString()会返回字符串”/\\d+/g”。

4.Date的toString()方法将返回一个具有可读性的日期时间字符串。

5.如果 Boolean 值是 true，则返回 “true”。否则，返回 “false”。

**valueOf()**

valueOf()函数的作用是返回该object自身。
与toString()一样，定义类时可以实现新的valueOf()方法，从而返回需要的结果。
JavaScript对于Date对象定义了更加具有可读性的valueOf()方法：

1.Date的valueOf()方法将返回一个时间戳数值，该数值为Date对象与1970年1月1日零时的时间差(以毫秒为单位)。
其他一律返回对象本身。

```
var arr = [1,2,3]; 
 
alert(Array.isArray(arr.valueOf()));  

alert(Array.isArray(arr.toString())); 
```

结果是第一个是true而第二个是false，为什么呢，其实valueOf()调用完以后还是返回一个数组。
这个数组被alert的时候会调用toString()函数，所以不是valueOf()和toString()函数相同，而是间接的调用了toString()函数！

进一步测试下：

```
var arr = [1,2,3];  
arr.toString = function () {  
    alert("你调用了toString函数");  
}  
alert(arr.valueOf());  
```

结果就是我们会看到“你调用了toString函数”。

**总结：**

valueOf偏向于运算，toString偏向于显示。

1、 在进行强转字符串类型时将优先调用toString方法，强转为数字时优先调用valueOf。

2、 在有运算操作符的情况下，valueOf的优先级高于toString。


### var arr=new Array()和 var arr=[] 的区别

通过new 关键字 实例化一个 数组对象，并把这个数组对象的句柄 赋值给一个变量.

这样的实例具有的生命周期会很长，直到这个对象被销毁，他在堆栈中会独立的开辟一个空间.

但是通过 var Arr = []  的方式创建   在内存结构中 只在栈中声明了这个变量，相对来说比New关键字创建的对象性能高.

### indexOf()与search()
两个方法都是返回目标自字符串索引值的。

indexOf()和search()有什么区别呢？

首先要明确search()的参数必须是正则表达式，而indexOf()的参数只是普通字符串。indexOf()是比search()更加底层的方法。

如果只是对一个具体字符串来查找，那么使用indexOf()的系统资源消耗更小，效率更高；

如果是查找具有某些特征的字符串（比如查找以a开头，后面是数字的字符串），那么indexOf()就无能为力，必须要使用正则表达式和search()方法了。

很多时候用indexOf()不是为了真的想知道子字符串的位置，而是想知道长字符串中没有包含这个子字符串。如果返回索引值是-1，那么说明没有：不等于-1，那么就是有。

所以一般情况下indexOf比search更省资源。

### substring()和slice()
两个方法用于提取字符串中介于两个指定下标之间的字符。

不同的是slice()的参数可以为负数，`string.slice(start, end)`，如果start的值为-1，则指字符串的最后一个字符。





<style>
span{
color:red}
</style>