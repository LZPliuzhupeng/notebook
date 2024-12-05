## gulp安装
1、全局安装gulp    
`npm install --global gulp`    
2、作为项目的开发依赖（devDependencies）安装：    
新建项目，打开项目所在路径，打开所在的命令提示符    
`npm install --save-dev gulp`    
3、初始化
`npm init`默认设置按回车键跳过    


## sass安装(css预处理器)
1、因为sass依赖于ruby环境，所以装sass之前先确认装了ruby。先到[官网下载个ruby](https://rubyinstaller.org/downloads/) 。   
在安装的时候，请勾选Add Ruby executables to your PATH这个选项，添加环境变量，不然以后使用编译软件的时候会提示找不到ruby环境 。   
2、安装完ruby之后，在开始菜单中，找到刚才我们安装的ruby，打开Start Command Prompt with Ruby    
3、然后直接在命令行中输入 `gem install sass`

4、按回车键确认，等待一段时间就会提示你sass安装成功。最近因为墙的比较厉害，如果你没有安装成功，可以尝试下面方法，或参考下面的淘宝的RubyGems镜像安装sass，如果成功则忽略。   
`gem source --remove https://rubygems.org `   
`gem source --add http://rubygems.org `
    
 5、查看一下是否更新源成功      
`gem sources  `         

如果要安装beta版本的，可以在命令行中输入    
`gem install sass --pre `  
 
升级sass版本的命令行为    
`gem update sass` 
   
查看sass版本的命令行为    
`sass -v`    



## 定义一个任务
在项目里面创建创建`gulpfile.js`文件    
编写：
```
const gulp = require("gulp");

gulp.task("hi",function(){
	console.log("hello");
})

```
> 注意`const gulp = require("gulp")`,在以下方法都必须添加，同一文件头文件编写即可;    

输入`gulp hi`运行    
运行成功会输出hello


## 压缩css
1、打开项目文件路径，cmd进入命令提示符   
2、`npm install gulp-clean-css --save-dev`    
3、在项目的自己所创建的`gulpfile.js`里编写:
```
const gulp = require("gulp");

//压缩css文件

const CleanCss =require("gulp-clean-css");

gulp.task("minify-css",function(){

//将要处理的css文件导进来
//在项目根目录创建src/css文件夹，即为要被压缩css文件的相对路径
gulp.src("src/css/*.css")

//压缩css代码
  .pipe(CleanCss())

//生成文件，创建并放在dist/css目录下
  .pipe(gulp.dest("dist/css"))
})

```
4、在项目所在位置在命令提示符    
输入`gulp minify-css`


## 导入html
1、所创建的`gulpfile.js`里添加
```
gulp.task("copy-html",function(){
	
//将html文件导入
   gulp.src("src/**/*.html")
     .pipe(gulp.dest("dest/"))
})

```


## 引入gulp-sass
1、打开项目文件的所在位置，打开命令提示符
`npm install gulp-sass --save-dev`    
2、所创建的`gulpfile.js`里添加    
```

const sass=require("gulp-sass");

//将scss编译成css
gulp.task("sass",function(){
	
    //src/scss/里面任意文件夹的任意scss文件都会对应得编译成css生成在src/css/文件里    
	//scss为监听文件夹，css为输出文件夹    
	
	gulp.src("src/scss/**/*.scss")
	.pipe(sass())
	.pipe(gulp.dest("src/css/"))
	
});

```
3、命令行输入执行    
`gulp sass`


## 监听scss
> 按照上面的手动编译，每次写完scss都要手动执行编译方法肯定很麻烦    
所以这个监听scss文件，每当保存scss文件都会自动编译成css

1、打开命令提示符，切换至scss文件夹的根目录，也就是scss文件夹的上一层目录    
输入`scss –watch scss:css`   
> reset.scss会在css文件夹下生成reset.css    
如果scss文件夹下还有其他scss文件，会在css文件下单独生成对应的css文件    
如果名字加下划线，如:_commen.scss不会生成commen.css    
一般写公共样式可这样编写，在其他scss文件内直接加公共样式编写：@import “commen” ,不用_ commen也行    


## 自动添加前缀、抑制错误
> 在scss编译前添加各种浏览器兼容前缀,如：-webkit-    
添加plumber，在每次报错的时候不会停止监听

```
//自动添加前缀
const autoprefixer =require('gulp-autoprefixer');
//抑制错误
const plumber=require('gulp-plumber');
//将scss编译成css
gulp.task("sass",function(){
	gulp.src("luban/scss/**/*.scss")
	.pipe(plumber())
	.pipe(sass())
	.pipe(autoprefixer({
		browsers:['since 2010'], //重要配置详见下面
		cascade:false  //是否美化属性值
	}))
	.pipe(plumber.stop())
	.pipe(gulp.dest("luban/css/"))	
});
```


## 热更新
```
const browserSync = require('browser-sync').create();
// 静态服务器
gulp.task('browser-sync', function() {
    browserSync.init({
        server: {
            baseDir: "./luban" //项目目录，会自动寻找index.html
        },
        port:1234  //端口号
    });

     gulp.watch(["luban/*.html"]).on("change",browserSync.reload)
     gulp.watch("luban/scss/*.scss",["sass"]).on("change",browserSync.reload);
});
```

`gulp browser-sync`