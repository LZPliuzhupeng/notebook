# 本地存储
解决数据的持久化问题

## 1、cookies

<!-- **用途：携带数据给服务端、一定时间内，免登陆** -->

## 2、localStorage
localStorage 对象存储的数据没有时间限制。第二天、第二周或下一年之后，数据依然可用。    
直至用户删除

localStorage对象

* 保存数据：localStorage.setItem(key,value);
* 读取数据：localStorage.getItem(key);
* 删除单个数据：localStorage.removeItem(key);
* 删除所有数据：localStorage.clear();
* 得到某个索引的key：localStorage.key(index);

> 存储前先将json数据转化为字符串，JSON.stringify()，    
反之，读取后要将数据转为json数据JSON.parse()

## 3、sessionStorage
sessionStorage 方法针对一个 session 进行数据存储。当用户关闭浏览器窗口后，数据会被删除。


> 扩展:    
http协议，无状态的传输协议(通过http协议访问网站，网站无法识别用户，没有保存数据的能力)    
身份识别-浏览器提供给服务端(服务端可以伪造UA，达到欺骗功能)    
保存数据-浏览器实现    