#### ulid

设计为128 bit大小，与UUID兼容

每毫秒生成1.21e+24个唯一的ULID（高性能）

按字典顺序（字母顺序）排序

标准编码为26个字符的字符串，而不是像UUID那样需要36个字符

使用Crockford的base32算法来提高效率和可读性（每个字符5 bit）

不区分大小写

没有特殊字符串（URL安全，不需要进行二次URL编码）

单调排序（正确地检测并处理相同的毫秒，所谓「单调性」，就是毫秒数相同的情况下，能够确保新的ULID随机部分的在最低有效位上加1位）

```
01AN4Z07BY      79KA1307SR9X4MV3
|----------|    |----------------|
 Timestamp          Randomness
   48bits             80bits
```

## Installation
`npm install --save ulid`


## Import

#### TypeScript, ES6+, Babel, Webpack, Rollup, etc.. environments
```js
import { ulid } from 'ulid'
 
ulid() // 01ARZ3NDEKTSV4RRFFQ69G5FAV
```


#### CommonJS environments
```js
const ULID = require('ulid')
 
ULID.ulid()
```


#### AMD (RequireJS) environments
```js
define(['ULID'] , function (ULID) {
  ULID.ulid()
});
```


#### Browser
```js
<script src="/path/to/ulid.js"></script>
<script>
    ULID.ulid()
</script> 
```


## Usage

### ulid 
```js
import { ulid } from 'ulid'
 
ulid() // 01ARZ3NDEKTSV4RRFFQ69G5FAV

//您还可以输入种子时间，它将始终为时间组件提供相同的字符串。这对于迁移到ulid很有用。
ulid(1469918176385) // 01ARYZ6S41TSV4RRFFQ69G5FAV 
```


### monotonicFactory 
```js
import { monotonicFactory } from 'ulid'
 
const ulid = monotonicFactory()
 
//对相同的时间戳进行严格排序，将最低有效随机位加1
ulid(150000) // 000XAL6S41ACTAV9WEVGEMMVR8
ulid(150000) // 000XAL6S41ACTAV9WEVGEMMVR9
ulid(150000) // 000XAL6S41ACTAV9WEVGEMMVRA
ulid(150000) // 000XAL6S41ACTAV9WEVGEMMVRB
ulid(150000) // 000XAL6S41ACTAV9WEVGEMMVRC
 
//即使传递(或生成)较低的时间戳，它也将保持排序顺序
ulid(100000) // 000XAL6S41ACTAV9WEVGEMMVRD
```


### factory, detectPrng
默认情况下，ulid将不使用Math。随机，因为那是不安全的。允许使用数学。随机，你必须使用factory和detectPrng。
```js
import { factory, detectPrng } from 'ulid'
 
const prng = detectPrng(true) // pass `true` to allow insecure
const ulid = factory(prng)
 
ulid() // 01BXAVRG61YJ5YSBRM51702F6M
```

要使用您自己的伪随机数生成器，请导入工厂，并将生成器函数传递给它。
```js
import { factory } from 'ulid'
import prng from 'somewhere'
 
const ulid = factory(prng)
 
ulid() // 01BXAVRG61YJ5YSBRM51702F6M
```

你也可以传递一个prng给单调工厂函数。
```js
import { monotonicFactory } from 'ulid'
import prng from 'somewhere'
 
const ulid = monotonicFactory(prng)
 
ulid() // 01BXAVRG61YJ5YSBRM51702F6M
```

### replaceCharAt
### incrementBase32
### randomChar
### encodeTime
### encodeRandom
### decodeTime
