### nvm、ode安装
+ 使用nvm来管理Nodejs版本

#### Window环境

[nvm-window的github库](https://github.com/coreybutler/nvm-windows)



1、下载 点击下载,选择[nvm-setup.zip(安装版)](https://github.com/coreybutler/nvm-windows/releases)，如果之前有安装过nodejs，先卸载。

!>
安装 记得先关闭360等杀毒软件(会误报病毒)      
先选择要安装的目录,注意路径中不要有`空格`和`中文字符  ` 

>    
第一个安装路径为根目录   
第二个安装路径为根目录下的nodejs

2、使用淘宝镜像 去到你的nvm安装目录(例如这里是c:/nvm)，打开settings.txt的文本文件， 增加两行
```
node_mirror: http://npm.taobao.org/mirrors/node/ 
npm_mirror: https://npm.taobao.org/mirrors/npm/
```

3、安装nodejs
```
nvm install 14.15.1       #安装14.15.1  
nvm use 14.15.1            #切换14.15.1版本
```
>   
如果npm安装完后，出现不是内部命令，注意查看环境变量Path是否有包含nodejs目录   
或卸载重装

?>
cnpm安装
npm install -g cnpm --registry=https://registry.npm.taobao.org
