# cookie

### 属性

+ `expires`

cookie失效日期。
1. 若没有填写 max-age, expires ,默认都为None。默认有效期为session， 即会话cookie。
2. 只有max-age,  则按秒计算过期时间（秒）, 浏览器会存在本地缓存路径, 并自动删除过期cookie
3. 只有expires,  则按照时间字符串计算过期时间, 浏览器会存在本地缓存路径, 自动删除过期cookie
4. 若 max-age和 expires 同时存在,  则默认使用 max-age
5. 如果设置的cookie时间小于计算机时间, 浏览器则不提取cookie


+ `domain`、`path`
+ `secure`
+ `HttpOnly`