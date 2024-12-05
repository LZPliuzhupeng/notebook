# console

```js
console.log('hello');
console.info('信息');
console.error('错误');
console.warn('警告');
```

## log()  
```js
console.log(
  `%c vue-devtools %c Detected Vue v1.0.0 %c`,
  'background:#35495e ; padding: 1px; border-radius: 3px 0 0 3px;  color: #fff',
  'background:#41b883 ; padding: 1px; border-radius: 0 3px 3px 0;  color: #fff',
  'background:transparent'
)
```

## count()
用于计数，输出它被调用了多少次。


## group()、groupEnd()
用于将显示的信息分组，可以把信息进行折叠和展开。
```js
console.group('第一层');
  console.group('第二层');

    console.log('error');
    console.error('error');
    console.warn('error');

  console.groupEnd(); 
console.groupEnd();
```

## time()、timeEnd()
计时开始、计时结束

## trace()
追踪函数的调用过程
```js
function d(a) { 
  console.trace();
  return a;
}
function b(a) { 
  return c(a);
}
function c(a) { 
  return d(a);
}
var a = b('123');
```

## table()
将复合类型的数据转为表格显示
```js
var arr= [ 
         { num: "1"},
         { num: "2"}, 
         { num: "3" }
    ];
console.table(arr);

var obj= {
     a:{ num: "1"},
     b:{ num: "2"},
     c:{ num: "3" }
};
console.table(obj);
```


***参考[JavaScript Console 对象 | 菜鸟教程](https://www.runoob.com/w3cnote/javascript-console-object.html)***