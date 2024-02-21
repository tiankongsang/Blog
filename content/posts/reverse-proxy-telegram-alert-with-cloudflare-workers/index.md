---
title: '用CloudFlare的workers部署Telegram Bot通知反代'
date: 2024-02-21T17:00:51+08:00
draft: false
showReadingTime: true
---

## 准备内容：
- Telegram Bot Token
- Cloudflare account

### Telegram Bot Token
可以从 https://t.me/BotFather 获取 Token。

![](Snipaste_2024-02-21_17-26-26.png)

### Cloudflare workers

新建 workers，填入名称，和 `worker.js` 内容

#### worker.js 内容
``` js
const whitelist = ["/botxxxxxxxxxx:"];
const tg_host = "api.telegram.org";

addEventListener('fetch', event => {
    event.respondWith(handleRequest(event.request))
})

function validate(path) {
    for (var i = 0; i < whitelist.length; i++) {
        if (path.startsWith(whitelist[i]))
            return true;
    }
    return false;
}

async function handleRequest(request) {
    var u = new URL(request.url);
    u.host = tg_host;
    if (!validate(u.pathname))
        return new Response('Unauthorized', {
            status: 403
        });
    var req = new Request(u, {
        method: request.method,
        headers: request.headers,
        body: request.body
    });
    const result = await fetch(req);
    return result;
}
```

替换其中 `"/botxxxxxxxxxx:"` 为你的 `Telegram Bot Token` 前面数字部分，然后保存并部署。

还可以自定义域名，如果遇到 `403` 的情况，可以开放 Cloudflare IP 白名单。