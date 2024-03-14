---
title: "CloudFlareを使用したWorkersによるTelegram Bot通知のリバースプロキシのデプロイ"
date: 2024-02-21T17:00:51+08:00
draft: false
showReadingTime: true
---

## 準備するもの

- Telegram Bot のトークン
- Cloudflare アカウント

### Telegram Bot のトークン

[BotFather](https://t.me/BotFather) からトークンを取得できます。

![スクリーンショット](Snipaste_2024-02-21_17-26-26.png)

### Cloudflare Workers

まず、`Worker` を作成し、次に名前と以下の `worker.js` の内容を入力します：

```js
const whitelist = ["/botxxxxxxxxxx:"];
const tg_host = "api.telegram.org";

addEventListener("fetch", (event) => {
  event.respondWith(handleRequest(event.request));
});

function validate(path) {
  for (var i = 0; i < whitelist.length; i++) {
    if (path.startsWith(whitelist[i])) return true;
  }
  return false;
}

async function handleRequest(request) {
  var u = new URL(request.url);
  u.host = tg_host;
  if (!validate(u.pathname))
    return new Response("Unauthorized", {
      status: 403,
    });
  var req = new Request(u, {
    method: request.method,
    headers: request.headers,
    body: request.body,
  });
  const result = await fetch(req);
  return result;
}
```

- `"/botxxxxxxxxxx:"` をお使いの `Telegram Bot Token` の数字部分に置き換えて保存して展開してください。
- また、独自のドメインを設定することもできます。`403` エラーが発生した場合は、Cloudflare IP ホワイトリストを開放してみてください。
