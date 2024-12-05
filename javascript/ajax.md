# ajax
AJAX 指异步 JavaScript 及 XML（Asynchronous JavaScript And XML）

## 兼容
```
var xmlhttp;
if (window.XMLHttpRequest)
  {// code for IE7+, Firefox, Chrome, Opera, Safari
  xmlhttp=new XMLHttpRequest();
  }
else
  {// code for IE6, IE5
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
```

## onreadystatechange 事件
```
属性	描述
onreadystatechange	
存储函数（或函数名），每当 readyState 属性改变时，就会调用该函数。

## readyState
readyState(状态值)
存有 XMLHttpRequest 的状态。从 0 到 4 发生变化。
0  UNSENT: 请求未初始化(已创建XMLHttpRequest对象，但未调用open()方法。)
1  OPENED: 服务器连接已建立(已调用open()方法，未send)
2  HEADERS_RECEIVED: 请求已接收(send()方法已经被调用，并且头部和状态已经可获得。)
3  LOADING: 请求处理中(下载中,responseText属性已经包含部分数据。)
4  DONE: 请求已完成，且响应已就绪(下载操作已完成。)

status(状态码)	
200: "OK"
404: 未找到页面
```

## status
```
1xx 接收到请求并继续处理

---------------------
2xx	处理成功响应类(201-206都表示服务器成功处理了请求的状态代码，说明网页可以正常访问。)
	200（成功）  服务器已成功处理了请求。通常，这表示服务器提供了请求的网页。
	201（已创建）  请求成功且服务器已创建了新的资源。 
	202（已接受）  服务器已接受了请求，但尚未对其进行处理。 
	203（非授权信息）  服务器已成功处理了请求，但返回了可能来自另一来源的信息。 
	204（无内容）  服务器成功处理了请求，但未返回任何内容。 
	205（重置内容） 服务器成功处理了请求，但未返回任何内容。
与 204 响应不同，此响应要求请求者重置文档视图（例如清除表单内容以输入新内容）。 
	206（部分内容）  服务器成功处理了部分 GET 请求。

---------------------

3xx	重定向响应类 (300-3007表示的意思是：要完成请求，您需要进一步进行操作。
通常，这些状态代码是永远重定向的。)
	303：重定向 
	
	300（多种选择）  服务器根据请求可执行多种操作。
服务器可根据请求者来选择一项操作，或提供操作列表供其选择。
	301（永久移动）  请求的网页已被永久移动到新位置。服务器返回此响应时，会自动将请求者转到新位置。
您应使用此代码通知搜索引擎蜘蛛网页或网站已被永久移动到新位置。 
	302（临时移动） 服务器目前正从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求。
会自动将请求者转到不同的位置。
但由于搜索引擎会继续抓取原有位置并将其编入索引，因此您不应使用此代码来告诉搜索引擎页面或网站已被移动。 
	303（查看其他位置） 当请求者应对不同的位置进行单独的 GET 请求以检索响应时，服务器会返回此代码。
对于除 HEAD 请求之外的所有请求，服务器会自动转到其他位置。 
	304（未修改） 自从上次请求后，请求的网页未被修改过。服务器返回此响应时，不会返回网页内容。
如果网页自请求者上次请求后再也没有更改过，您应当将服务器配置为返回此响应。
由于服务器可以告诉 搜索引擎自从上次抓取后网页没有更改过，因此可节省带宽和开销。 
	305（使用代理） 请求者只能使用代理访问请求的网页。
如果服务器返回此响应，那么，服务器还会指明请求者应当使用的代理。 
	307（临时重定向）  服务器目前正从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求。
会自动将请求者转到不同的位置。但由于搜索引擎会继续抓取原有位置并将其编入索引，因此您不应使用此代码来告诉搜索引擎某个页面或网站已被移动。

---------------------

4xx	客户端错误，语法错误，有些语句不能正确执行 (HTTP状态码表示请求可能出错，会妨碍服务器的处理。)
	400：请求错误、
	401：未授权、
	403：禁止访问、
	404：文件未找到 
	
	400（错误请求） 服务器不理解请求的语法。 
	401（身份验证错误） 此页要求授权。您可能不希望将此网页纳入索引。 
	403（禁止） 服务器拒绝请求。
	404（未找到） 服务器找不到请求的网页。例如，对于服务器上不存在的网页经常会返回此代码。
例如：http://www.0631abc.com/20100aaaa，就会进入404错误页面
	405（方法禁用） 禁用请求中指定的方法。
	406（不接受） 无法使用请求的内容特性响应请求的网页。 
	407（需要代理授权） 此状态码与 401 类似，但指定请求者必须授权使用代理。如果服务器返回此响应，还表示请求者应当使用代理。 
	408（请求超时） 服务器等候请求时发生超时。 
	409（冲突） 服务器在完成请求时发生冲突。服务器必须在响应中包含有关冲突的信息。
服务器在响应与前一个请求相冲突的 PUT 请求时可能会返回此代码，以及两个请求的差异列表。 
	410（已删除） 请求的资源永久删除后，服务器返回此响应。
该代码与 404（未找到）代码相似，但在资源以前存在而现在不存在的情况下，有时会用来替代 404 代码。
如果资源已永久删除，您应当使用 301 指定资源的新位置。 
	411（需要有效长度） 服务器不接受不含有效内容长度标头字段的请求。 
	412（未满足前提条件） 服务器未满足请求者在请求中设置的其中一个前提条件。 
	413（请求实体过大） 服务器无法处理请求，因为请求实体过大，超出服务器的处理能力。 
	414（请求的 URI 过长） 请求的 URI（通常为网址）过长，服务器无法处理。 
	415（不支持的媒体类型） 请求的格式不受请求页面的支持。 
	416（请求范围不符合要求） 如果页面无法提供请求的范围，则服务器会返回此状态码。 
	417（未满足期望值） 服务器未满足"期望"请求标头字段的要求。

---------------------


5xx	(500至505表示的意思是：服务器在尝试处理请求时发生内部错误。这些错误可能是服务器本身的错误，而不是请求出错。)
	500（服务器内部错误）  服务器遇到错误，无法完成请求。 
	501（尚未实施） 服务器不具备完成请求的功能。
例如，当服务器无法识别请求方法时，服务器可能会返回此代码。 
	502（错误网关） 服务器作为网关或代理，从上游服务器收到了无效的响应。 
	503（服务不可用） 目前无法使用服务器（由于超载或进行停机维护）。通常，这只是一种暂时的状态。 
	504（网关超时）  服务器作为网关或代理，未及时从上游服务器接收请求。 
	505（HTTP 版本不受支持） 服务器不支持请求中所使用的 HTTP 协议版本。 

---------------------

```

> 在IE中，状态有着不同的名称，并不是 UNSENT，OPENED ， HEADERS_RECEIVED ， LOADING 和 DONE,      
而是 READYSTATE_UNINITIALIZED (0)，READYSTATE_LOADING (1) ， READYSTATE_LOADED (2) ， READYSTATE_INTERACTIVE (3) 和 READYSTATE_COMPLETE (4) 。	

> Response Headers.Access-Control-Allow-Origin: 跨域白名单


## 传参请求

**html**
```
		<input type="text" name="" id="text" value="周杰伦" />
		<input type="submit" name="" id="button" value="搜索" />
		<p id="p"></p>
```

```
		<script type="text/javascript">
			
			var url="https://netease.bluej.cn/search";
				//蓝景搜索

//			var	url="http://localhost:3333/";
				//本地服务器
//			var url="https://u.y.qq.com/cgi-bin/musicu.fcg?callback=recom22626547841037548&g_tk=5381&jsonpCallback=recom22626547841037548&loginUin=0&hostUin=0&format=jsonp&inCharset=utf8&outCharset=utf-8&notice=0&platform=yqq&needNewCode=0&data=%7B%22comm%22%3A%7B%22ct%22%3A24%7D%2C%22category%22%3A%7B%22method%22%3A%22get_hot_category%22%2C%22param%22%3A%7B%22qq%22%3A%22%22%7D%2C%22module%22%3A%22music.web_category_svr%22%7D%2C%22recomPlaylist%22%3A%7B%22method%22%3A%22get_hot_recommend%22%2C%22param%22%3A%7B%22async%22%3A1%2C%22cmd%22%3A2%7D%2C%22module%22%3A%22playlist.HotRecommendServer%22%7D%2C%22playlist%22%3A%7B%22method%22%3A%22get_playlist_by_category%22%2C%22param%22%3A%7B%22id%22%3A8%2C%22curPage%22%3A1%2C%22size%22%3A40%2C%22order%22%3A5%2C%22titleid%22%3A8%7D%2C%22module%22%3A%22playlist.PlayListPlazaServer%22%7D%2C%22new_song%22%3A%7B%22module%22%3A%22QQMusic.MusichallServer%22%2C%22method%22%3A%22GetNewSong%22%2C%22param%22%3A%7B%22type%22%3A0%7D%7D%2C%22new_album%22%3A%7B%22module%22%3A%22music.web_album_library%22%2C%22method%22%3A%22get_album_by_tags%22%2C%22param%22%3A%7B%22area%22%3A1%2C%22company%22%3A-1%2C%22genre%22%3A-1%2C%22type%22%3A-1%2C%22year%22%3A-1%2C%22sort%22%3A2%2C%22get_tags%22%3A1%2C%22sin%22%3A0%2C%22num%22%3A40%2C%22click_albumid%22%3A0%7D%7D%2C%22toplist%22%3A%7B%22module%22%3A%22music.web_toplist_svr%22%2C%22method%22%3A%22get_toplist_index%22%2C%22param%22%3A%7B%7D%7D%2C%22focus%22%3A%7B%22module%22%3A%22QQMusic.MusichallServer%22%2C%22method%22%3A%22GetFocus%22%2C%22param%22%3A%7B%7D%7D%7D";
				//QQ音乐
			var text=document.querySelector("#text");
			var but=document.querySelector("#button");
			var p=document.querySelector("#p");
			but.addEventListener("click",function(e){
				e.preventDefault();
				url+="?keywords="+text.value;
				
			var xhr=new XMLHttpRequest();
				xhr.open("GET",url);
				xhr.send();
				xhr.addEventListener("readystatechange",function(){
				if(xhr.readyState==4&&xhr.status==200)
				{
					console.log(JSON.parse(xhr.response))
				}
			})
			})
			
		</script>
```

**开启本地服务器测试**

node写法
```
var http=require("http");
http.createServer(function(req,res){
	res.writeHead(200,{"Access-Control-Allow-Origin":"http://127.0.0.1:8020"})
	res.end(JSON.stringify({
		msg:"hello worid"
	}))
}).listen(3333);
```

> 因为[同源策略](AJAX/同源策略.md)，出现跨域问题，    
res.writeHead(200,{"Access-Control-Allow-Origin":"http://127.0.0.1:8020"})    
在本地服务器添加白名单


**通过JSONP测试**

通过测试QQ音乐的musicu数据链接

```
		<script type="text/javascript">
			function recom6089657945649074(response){
				console.log(response);
			}
		</script>
		
		<script src="https://u.y.qq.com/cgi-bin/musicu.fcg?callback=recom6089657945649074&g_tk=5381
		&jsonpCallback=recom6089657945649074&loginUin=0&hostUin=0
		&format=jsonp&inCharset=utf8&outCharset=utf-8&notice=0&platform=yqq
		&needNewCode=0&data=%7B%22comm%22%3A%7B%22ct%22%3A24%7D%2C%22
		category%22%3A%7B%22method%22%3A%22get_hot_category%22%2C%22
		param%22%3A%7B%22qq%22%3A%22%22%7D%2C%22module%22%3A%22
		music.web_category_svr%22%7D%2C%22recomPlaylist%22%3A%7B%22method%22%3A%22
		get_hot_recommend%22%2C%22param%22%3A%7B%22async%22%3A1%2C%22cmd%22%3A2%7D%2C%22
		module%22%3A%22playlist.HotRecommendServer%22%7D%2C%22playlist%22%3A%7B%22
		method%22%3A%22get_playlist_by_category%22%2C%22param%22%3A%7B%22id%22%3A8%2C%22
		curPage%22%3A1%2C%22size%22%3A40%2C%22order%22%3A5%2C%22titleid%22%3A8%7D%2C%22
		module%22%3A%22playlist.PlayListPlazaServer%22%7D%2C%22new_song%22%3A%7B%22
		module%22%3A%22QQMusic.MusichallServer%22%2C%22method%22%3A%22GetNewSong%22%2C%22
		param%22%3A%7B%22type%22%3A0%7D%7D%2C%22new_album%22%3A%7B%22module%22%3A%22
		music.web_album_library%22%2C%22method%22%3A%22get_album_by_tags%22%2C%22
		param%22%3A%7B%22area%22%3A1%2C%22company%22%3A-1%2C%22genre%22%3A-1%2C%22
		type%22%3A-1%2C%22year%22%3A-1%2C%22sort%22%3A2%2C%22get_tags%22%3A1%2C%22
		sin%22%3A0%2C%22num%22%3A40%2C%22click_albumid%22%3A0%7D%7D%2C%22toplist%22%3A%7B%22
		module%22%3A%22music.web_toplist_svr%22%2C%22method%22%3A%22get_toplist_index%22%2C%22
		param%22%3A%7B%7D%7D%2C%22focus%22%3A%7B%22module%22%3A%22QQMusic.MusichallServer%22%2C%22
		method%22%3A%22GetFocus%22%2C%22param%22%3A%7B%7D%7D%7D">
			
		</script>
```

> 	JSONP允许script标签不遵循浏览器的"同源策略"安全限制，    
		引入外部资源，通过callback属性传入回调函数，从而拉取数据。    
		局限性：接口需要支持JSONP，请求方式为GET，只支持http方式，    
		请求失败不会返回状态码，而且还可能存在安全漏洞，容易发生xss漏洞.
		
> 获取jsonpCallback    
F12 --> Network -->  刷新  -->  选取js文件  --> Headers --> Query String Parameters

## 浏览器同源策略

同源策略(Same origin policy)是一种约定，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能可能都会受到影响。

可以说Web是构建在同源策略基础之上的，浏览器只是针对同源策略的一种实现。


同源策略，它是由Netscape提出的一个著名的安全策略。

现在所有支持JavaScript 的浏览器都会使用这个策略。

<span>所谓同源是指，域名，协议，端口相同。</span>

当一个浏览器的两个tab页中分别打开来 百度和谷歌的页面

当浏览器的百度tab页执行一个脚本的时候会检查这个脚本是属于哪个页面的，

即检查是否同源，只有和百度同源的脚本才会被执行。

如果非同源，那么在请求数据时，浏览器会在控制台中报一个异常，提示拒绝访问。

> location.origin