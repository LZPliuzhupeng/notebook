##  wxParse（小程序富文本解析）

```
//导入样式
@import "/wxParse/wxParse.wxss";

//引用模板
<import src="../../wxParse/wxParse.wxml"/>
<template is="wxParse" data="{{wxParseData:goods_desc.nodes}}"/>

//创建WxParse对象，解析
var WxParse = require('../../wxParse/wxParse.js');
 WxParse.wxParse('goods_desc', 'html', that.data.goods_desc, that, 0)

/**
* WxParse.wxParse(bindName , type, data, target,imagePadding)
* 1.bindName绑定的数据名(必填)
* 2.type可以为html或者md(必填)
* 3.data为传入的具体数据(必填)
* 4.target为Page对象,一般为this(必填)
* 5.imagePadding为当图片自适应是左右的单一padding(默认为0,可选)
*/
```

> 如图片显示过大 设置 `img{width:100%;height:100%}`

## 导出订单报表

安装插件：`npm install --save xlsx file-saver`

```
//组件引入：
import FileSaver from 'file-saver'
import XLSX from 'xlsx'

//调用方法
exportExcel(){
        // this.datastr=this.setClassData(this.date1)+'-'+this.setClassData(this.date2)
         /* generate workbook object from table */
         var wb = XLSX.utils.table_to_book(document.querySelector('#out-table'))  //#out-table为table节点,请设置需要导出报表table的id为out-table
         /* get binary string as output */
         var wbout = XLSX.write(wb, { bookType: 'xlsx', bookSST: true, type: 'array' })
         try {
             FileSaver.saveAs(new Blob([wbout], { type: 'application/octet-stream' }), '报表名.xlsx') //导出文件名
         } catch (e) { if (typeof console !== 'undefined') console.log(e, wbout) }
         return wbout
     },

```

> 注意：表格数据过大会出现数据的DOM节点还没渲染完就导出了报表，需要监听等DOM节点渲染为再导出
`watch：{ 
	data:function(newval,oldval){  
		this.$nextTick(function () {
				this.exportExcel()
		}) 
	} 
}`