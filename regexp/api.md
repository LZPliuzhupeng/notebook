#### 正则表达式相关 API

Javascript 中可以通过以下两种方式写正则:
+ 正则表达式字面量
+ 通过构造函数 RegExp 的实例

```js
let regExp = /test/

let regExp = new RegExp('test') 
// 或者
let regExp = new RegExp(/test/)
```

## 修饰符

### g
global 简写，全局匹配，找到所有满足匹配的子串，而不是默认只匹配首次结果。

### i
ignore case 简写，匹配过程中，忽略英文字母大小写，如 /test/i 可以匹配 Test、teSt、TesT 等。

### m
multiline 简写，多行匹配，把 ^ 和 $ 变成行开头和行结尾。比如可以匹配文本域(textarea)元素中的值。

### u *(不懂)*
unicode 简写，允许使用 Unicode 点转义符。

### y *(不懂)*
sticky 简写，开启粘连匹配，正则表达式执行粘连匹配时试图从最后一个匹配位置开始。


## RegExp 相关实例方法

### test
判断目标字符串中是否有满足正则匹配的子串。返回布尔值。

### exec *(不懂)*
比 match 更强大的正则匹配操作。如果正则表达式不包含 g 标志，str.match() 将返回与 RegExp.exec(). 相同的结果。

```js
new RegExp(/love*/).test('I love you, I love him')
// true

new RegExp(/love*/).exec('I love you, I love him')
// ['love', index: 2, input: 'I love you, I love hime', groups: undefined]
```


## RegExp 静态属性 *(不懂)*

### 1,...,9
最近一次第 1-9 个分组捕获的数据。

### input	
最近一次目标字符串，可以简写成 $_ 。

### lastMatch	
最近一次匹配的文本，可以简写成 $& 。

### lastParen	
最近一次捕获的文本，可以简写成 $+ 。

### leftContext	
目标字符串中 lastMatch 之前的文本，可以简写成 $` 。

### rightContext	
目标字符串中 lastMatch 之后的文本，可以简写成 $' 。


## String 相关实例方法

### search	
返回正则匹配到的第一个子串在目标字符串中的下标位置。

### split	
以正则匹配到的子串，对目标字符串进行切分。返回一个数组。

### match	
对目标字符串执行正则匹配操作，返回的匹配结果数组中包含具体的匹配信息。

+ groups: 一个命名捕获组对象，其键是捕获组名称，值是捕获组，如果未定义命名捕获组，则为 undefined。有关详细信息，请参阅组和范围。
+ index: 匹配的结果的开始位置
+ input: 搜索的字符串。| | replace | 对目标字符串进行替换操作。正则是其第一个参数。返回替换后的字符串。|

#### replace 第二个参数中的特殊字符

##### 1,2,...,$99	
匹配第 1-99 个分组里捕获的文本

##### $&	
匹配到的子串文本

##### $	
匹配到的子串的左边文本

##### $	
匹配到的子串的右边文本

##### $$	
美元符号


#### replace 第二个参数为函数
你可以指定一个函数作为第二个参数。在这种情况下，当匹配执行后，该函数就会执行。函数的返回值作为替换字符串。(注意：上面提到的特殊替换参数在这里不能被使用。) 另外要注意的是，如果第一个参数是正则表达式，并且其为全局匹配模式，那么这个方法将被多次调用，每次匹配都会被调用。

##### match	
匹配的子串。（对应于上述的$&。）

##### p1,p2, ...	
假如 replace() 方法的第一个参数是一个RegExp 对象，则代表第 n 个括号匹配的字符串。（对应于上述的1，1，1，2 等。）例如，如果是用 /(\a+)(\b+)/ 这个来匹配，p1 就是匹配的 \a+，p2 就是匹配的 \b+。

##### offset	
匹配到的子字符串在原字符串中的偏移量。（比如，如果原字符串是 'abcd'，匹配到的子字符串是 'bc'，那么这个参数将会是 1）

##### string	
被匹配的原字符串。

##### NamedCaptureGroup	
命名捕获组匹配的对象


<!-- ## 正则表达式的使用方法

* `exec`	一个在字符串中执行查找匹配的RegExp方法，它返回一个数组（未匹配到则返回null）。
* `test`	一个在字符串中测试是否匹配的RegExp方法，它返回true或false。
* `match`	一个在字符串中执行`查找`匹配的`String方法`，它`返回一个数组`或者在未匹配到时返回null。
* `search`	一个在字符串中`测试`匹配的`String方法`，它`返回匹配到的位置索引`，或者在失败时返回-1。
* `replace`	一个在字符串中执行`查找`匹配的`String方法`，并且`使用替换字符串替换掉匹配到的子字符串`。
* `split`	一个使用正则表达式或者一个固定字符串分隔一个字符串，并将分隔后的子字符串存储到数组中的String方法。

```
var imgurl="http://p0.meituan.net/w.h/movie/fed73337610278fbabeede42f6075b81653499.jpg"
/\w.h/.test(imgurl)
``` -->

## 练习

1.   
匹配所有符合 XML 规则的标签

```js
const regex = new RegExp(/<(\w+)>.+<\/(\1)>/);

regex.test('<div>code</div>')  // true
regex.test('<span>I Love U</span>') // true
regex.test('<h1>This is title</p>') // false
regex.test('<p></p>') // false

//'\1' 匹配的是 所获取的第1个()匹配的引用。
```

2.   
请用正则表达式匹配所有的小数

```js
const regex = new RegExp(/(?<!\.)\d+\.\d+$/);
// const regex = new RegExp(/^\d+(?<=\d)\.\d+$/);

regex.test(0.1)  // true
regex.test(1.30) // true
regex.test(13.14) // true
regex.test('1.3.1.4') // false
regex.test(1)   // false

//(?<!\.)反向后行断言，匹配一个位置其左边不为“.”；接着 \d+\.\d+匹配一位以上的数字 + “.” + 一位以上的数字。
```


3.   
提取下列数据中所有人的生日，使用两个分组，第一个分组提取“月”，第二个分组提取“日”。王伟 1993年1月2日 张伟 1996.8.24 李伟 1996.3.21 李秀 1994-7-5

```js
const regex = new RegExp(/((?<=[年.-])\d{1,2})[月.-](\d{1,2})/);

regex.exec('王伟 1993年1月2日')
// ['1月2', '1', '2', index: 8, input: '王伟 1993年1月2日', groups: undefined]

regex.exec('李伟 1996.3.21')
// ['3.21', '3', '21', index: 8, input: '李伟 1996.3.21', groups: undefined]

//主要还是 (?<=[年.-])反向先行断言，匹配一个位置其左边为 [年.-] 中的一个，其余的就比较容易理解了，最后用 exec 提取捕获组即可。
```

4.   
编写正则表达式进行密码强度的验证，规则如下：
+ 至少一个大写字母
+ 至少一个小写字母
+ 至少一个数字
+ 至少 8 个字符

```js
const regex = new RegExp(/(?=.*?[a-z])(?=.*?[A-Z])(?=.*?[0-9]).{8,}/);

regex.test('123456789') // false
regex.test('12ABab') // false
regex.test('12345ABCabc') // true
regex.test('ADMIN1234()') // false
regex.test('Hmm5201314') // true

//这段正则看着非常长，其实都是重复的逻辑，我们来拆解一下: (?=.*?[a-z]) 这段正则表达式规定了匹配的字符串中必须存在一个右边为任意字符和小写字母的位置，(?=.*?[a-z])(?=.*?[A-Z])(?=.*?[0-9])那这一整串连起来就是必须存在小写、大写、数字。
```

5.   
实现一个模板引擎，能够满足如下场景使用 let template = '我是{{name}}，年龄{{age}}，性别{{sex}}'; let data = { name: '姓名', age: 18 } render(template, data); // 我是姓名，年龄18，性别undefined

```js
function render(template, data) {
  if(typeof template !== 'string' || typeof data !== 'object') {
   return null
  }
 return template.replace(/{{(.*?)}}/g, (match, $1) => data[$1])
}
```

6.   
写一个方法把下划线命名转成大驼峰命名

```js
function strToCamel(str) {
    return str.replace(/(^|_)(\w)/g, (m, $1, $2) => $2.toUpperCase());
}
```