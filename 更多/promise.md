# Promise封装

## AJAX
```js
const request = function(url) {
    return new Promise((resolve, reject) => {
        const xhr = XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject('Mscrosoft.XMLHttp');
        xhr.open('GET', url, false);
        xhr.setRequestHeader('Accept', 'application/json');
        xhr.onreadystatechange = function() {
            if (xhr.readyState !== 4) return;
            if (xhr.status === 200 || xhr.status === 304) {
                resolve(xhr.responseText);
            } else {
                reject(new Error(xhr.responseText));
            }
        }
        xhr.send();
    })
}
```

## 小程序权限校验
```js
// 权限检验
function authSetting({
  scope,
  openSetting=false,
  title,
  content
}) {
  
  return new Promise((resolve, reject) => {
    wx.getSetting({
      success(res) {
        // 第一次授权或拒绝授权
        if (!res.authSetting[scope]) {
          wx.authorize({
            scope: scope,
            success(res) {
              resolve(res)
            },
            fail(rej) {
              // 用户拒绝授权
              reject(rej)
              if(openSetting){
                wx.showModal({
                  title: title || '重新授权',
                  content: content || '是否去设置授权？',
                  success: res => {
                    if (res.confirm) {
                      // 打开设置授权
                      wx.openSetting()
                    }
                  }
                })
              }
            }
          })
        } else {
          // 已授权
          resolve(res)
        }
      },
      fail(rej) {
        reject(rej)
      }
    })
  })
}
```