####  viewer安装
[参考网址](https://www.jianshu.com/p/84042c7b1b5b)

`npm install v-viewer --save`

```
//引入viewer

import Viewer from 'v-viewer'
import 'viewerjs/dist/viewer.css'
Vue.use(Viewer)
Viewer.setDefaults({
  Options: { 'inline': true, 'button': true, 'navbar': true, 'title': true, 'toolbar': true, 'tooltip': true, 'movable': true, 'zoomable': true, 'rotatable': true, 'scalable': true, 'transition': true, 'fullscreen': true, 'keyboard': true, 'url': 'data-source' }
})
```

```
//使用viewer

<viewer :images="photo"> 
    //photo 一定要一个数组，否则报错
        <img
            v-for="(src,index) in photo"
            :src="src"
            :key="index"
            :onerror="errorImg"
            >
</viewer>
```