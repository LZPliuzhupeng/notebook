## telegram

### 分享 mini app

```env
.env.development
# tg bot
NEXT_PUBLIC_BOT=cyber_game_test_bot
# tg bot webapp
NEXT_PUBLIC_BOT_WEBAPP=appgame
```

```js
// ton分享
const handleTonShare = useCallback(() => {
  const _url = `https://t.me/${process.env.NEXT_PUBLIC_BOT}/${process.env.NEXT_PUBLIC_BOT_WEBAPP}?startapp=${user?.invitationCode}`;
  const _text = `文案`;
  const _link = `https://t.me/share/url?url=${_url}&text=${encodeURIComponent(
    _text
  )}`;
  if (window?.Telegram?.WebApp?.initData) {
    window?.Telegram?.WebApp?.openTelegramLink(_link);
  } else {
    window?.open(_link);
  }
}, [user]);
```

```md
t.me/+ （hash)
tg://join?invite=（hash） #加好友 or 群
t.me/addlist/（slug）
tg://addlist?slug=（slug） #聊天文件夹链接
t.me/proxy?server=（server）&port=（port）&secret=（secret）
tg://proxy?server=（server）&port=（port）&secret=（secret） #电报代理
t.me/addtheme/（name）
tg://addtheme?slug=（name） #name=主题包
tg://user?id=（id） # 用户 ID
tg://emoji?id=（id） # 自定义表情符号 ID
t.me/（bot_username）?start=（parameter） # 链接到机器人并可能传递启动参数
tg://resolve?domain=（bot_username）&start=（parameter） # 链接到机器人并可能传递启动参数
t.me/（username）?videochat # 链接到群组的视频聊天
t.me/（username）?videochat=（invite_hash） # 指定邀请码的视频聊天
t.me/（username）?livestream # 链接到频道的直播
t.me/（username）?livestream=（invite_hash） # 指定邀请码的直播
t.me/（username）?voicechat # 链接到群组的语音聊天
t.me/（username）?voicechat=（invite_hash） # 指定邀请码的语音聊天
tg://resolve?domain=（username）&videochat # 链接到群组的视频聊天
tg://resolve?domain=（username）&videochat=（invite_hash） # 指定邀请码的视频聊天
tg://resolve?domain=（username）&livestream # 链接到频道的直播
tg://resolve?domain=（username）&livestream=（invite_hash） # 指定邀请码的直播
tg://resolve?domain=（username）&voicechat # 链接到群组的语音聊天
tg://resolve?domain=（username）&voicechat=（invite_hash） # 指定邀请码的语音聊天
t.me/addstickers/（slug） # 导入贴纸集
t.me/addemoji/（slug） # 导入自定义表情符号贴纸集
tg://addstickers?set=（slug） # 导入贴纸集
tg://addemoji?set=（slug） # 导入自定义表情符号贴纸集
t.me/proxy?server=（server）&port=（port）&secret=（secret） # MTProxy 链接
tg://proxy?server=（server）&port=（port）&secret=（secret） # MTProxy 链接
t.me/socks?server=（server）&port=（port）&user=（user）&pass=（pass） # Socks5 代理链接
tg://socks?server=（server）&port=（port）&user=（user）&pass=（pass） # Socks5 代理链接
t.me/addtheme/（name） # 安装主题
tg://addtheme?slug=（name） # 安装主题
t.me/bg/（slug）?mode=（mode） # 图像壁纸
tg://bg?slug=（slug）&mode=（mode） # 图像壁纸
t.me/bg/（hex_color） # 纯色填充壁纸
tg://bg?color=（hex_color） # 纯色填充壁纸
t.me/bg/（top_color）-（bottom_color）?rotation=（rotation） # 渐变填充壁纸
tg://bg?gradient=（top_color）-（bottom_color）&rotation=（rotation） # 渐变填充壁纸
t.me/bg/（hex_color1）~（hex_color2）~（hex_color3） # 自由渐变填充壁纸
tg://bg?gradient=（hex_color1）~（hex_color2）~（hex_color3） # 自由渐变填充壁纸
t.me/bg/（slug）?intensity=（intensity）&bg_color=（bg_color）&mode=（mode） # 图案壁纸
tg://bg?slug=（slug）&intensity=（intensity）&bg_color=（bg_color）&mode=（mode） # 图案壁纸
t.me/login/（code） # 登录码链接
tg://login?code=（code） # 登录码链接
t.me/invoice/（slug） # 发票链接
```

## twitter

[tweet-button 文档](https://developer.x.com/en/docs/twitter-for-websites/tweet-button/guides/web-intent)

[web-intent 文档](https://developer.x.com/en/docs/twitter-for-websites/web-intents/overview)

### 帖子

```html
<head>
  <meta property="twitter:url" content="分享的链接" />
  <meta name="twitter:title" content="title" />
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:image" content="分享的链接/img/share.jpg" />
</head>
```

```js
const _text = "文案";
const _url = "分享的链接"; // 可以按summary_large_image卡片信息显示
const _hashtags = "数码";
const _via = "某人";

window.open(
  `https://x.com/intent/tweet?text=${encodeURIComponent(
    _text
  )}&hashtags=${_hashtags}&via=${_via}`
);
```

### 关注

```js
window.open(`https://x.com/intent/follow?screen_name=_WheelofFate`);
```

### 点赞

```js
window.open(`https://x.com/intent/like?tweet_id=463440424141459456`);
```
