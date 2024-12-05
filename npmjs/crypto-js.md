#### crypto-js

## Node.js (Install)

`npm install crypto-js`

## Usage

```ts
const CryptoJS = require("crypto-js");
// 数据、密钥、密钥偏移量
const aesEncrypt = function (data: string, key: string, lv: string) {
  let srcs = CryptoJS.enc.Utf8.parse(data);
  let encrypted = CryptoJS.AES.encrypt(srcs, CryptoJS.enc.Utf8.parse(key), {
    mode: CryptoJS.mode.CBC,
    padding: CryptoJS.pad.Pkcs7,
    iv: CryptoJS.enc.Utf8.parse(lv),
  });
  let encryptedBase64Data = CryptoJS.enc.Base64.stringify(encrypted.ciphertext);
  return encryptedBase64Data;
};

aesEncrypt(JSON.stringify({ a: 1 }), "fgvs", "usda");
```
