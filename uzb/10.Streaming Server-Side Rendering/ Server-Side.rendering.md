# Streaming Server-Side Rendering

Ilovamiz mazmunini ko'rsatuvchi **streaming server** orqali biz hali ham server dasturimizni render qilayotganda **Time to Interactive** vaqtini qisqartirishimiz mumkin. 

Joriy navigatsiya uchun kerakli belgilarni o'z ichiga olgan bitta katta HTML faylini yaratish o'rniga, biz uni **kichikroq bo'laklarga** bo'lamiz! `Node stream` bizga `response` obyektiga ma'lumotlarni uzatish imkonini beradi, ya'ni biz **doimiy ravishda mijozga ma'lumotlarni yuborishimiz** mumkin. 

Client ma'lumotlar qismlarini olgan zahoti, u kontentni render qilishni boshlashi mumkin.

🎥 Video: [Streaming SSR (1-qism)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056525/patterns.dev/ssr-1.mp4)

---

Reactning built-in `renderToNodeStream` funksiyasi bizga ilovamizni **kichikroq bo’laklarga** yuborish imkonini beradi. Mijoz hali ham ma'lumot olayotganda, **user interface**ni bo'yashni boshlashi mumkinligi sababli, biz birinchi yuklashda juda samarali tajriba yaratishimiz mumkin.

Qabul qilingan DOM nodelarida **gidratsiya** (hydration) usulini chaqirish tegishli **hodisa boshqaruvchilarini biriktiradi**, bu esa UI'ni interaktiv qiladi!

🎥 Video: [Streaming SSR (2-qism)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056525/patterns.dev/ssr-2.mp4)




Aytaylik, bizda `App` komponentida foydalanuvchiga minglab **cat facts** (mushuklar haqidagi faktlar)ni ko‘rsatadigan ilova bor!

Quyidagi kod `React`, `Express`, va `renderToNodeStream` funksiyasi yordamida **Streaming Server-Side Rendering**ni amalga oshiradi:


# React va Express server kodi

```javascript
import React from "react";
import path from "path";
import express from "express";
import { renderToNodeStream } from "react-dom/server";

import App from "./src/App";

const app = express();

// app.get("/favicon.ico", (req, res) => res.end());
app.use("/client.js", (req, res) => res.redirect("/build/client.js"));

const DELAY = 500;
app.use((req, res, next) => {
  setTimeout(() => {
    next();
  }, DELAY);
});

const BEFORE = `
<!DOCTYPE html>
  <html>
    <head>
      <title>Cat Facts</title>
      <link rel="stylesheet" href="/style.css">
      <script type="module" defer src="/build/client.js"></script>
    </head>
    <body>
      <h1>Stream Rendered Cat Facts!</h1>
      <div id="approot">
`.replace(/\s*/g, "");

app.get("/", async (request, response) => {
  try {
    const stream = renderToNodeStream(<App />);
    const start = Date.now();

    stream.on("data", function handleData() {
      console.log("Render Start: ", Date.now() - start);
      stream.off("data", handleData);
      response.useChunkedEncodingByDefault = true;
      response.writeHead(200, {
        "content-type": "text/html",
        "content-transfer-encoding": "chunked",
        "x-content-type-options": "nosniff"
      });
      response.write(BEFORE);
      response.flushHeaders();
    });

    await new Promise((resolve, reject) => {
      stream.on("error", err => {
        stream.unpipe(response);
        reject(err);
      });

      stream.on("end", () => {
        console.log("Render End: ", Date.now() - start);
        response.write("</div></body></html>");
        response.end();
        resolve();
      });

      stream.pipe(response, { end: false });
    });

  } catch (err) {
    response.writeHead(500, {
      "content-type": "text/pain"
    });
    response.end(String((err && err.stack) || err));
    return;
  }
});

app.use(express.static(path.resolve(__dirname, "src")));
app.use("/build", express.static(path.resolve(__dirname, "build")));

const listener = app.listen(process.env.PORT || 2048, () => {
  console.log("Your app is listening on port " + listener.address().port);
});
```



App komponenti built-in renderToNodeStream usuli yordamida streamni oladi. Dastlabki HTML App komponentidan maʼlumotlar bo’laklari bilan birga response obyektiga yuboriladi. 

```js
<!DOCTYPE html>
2<html>
3  <head>
4    <title>Cat Facts</title>
5    <link rel="stylesheet" href="/style.css" />
6    <script type="module" defer src="/build/client.js"></script>
7  </head>
8  <body>
9    <h1>Stream Rendered Cat Facts!</h1>
10    <div id="approot"></div>
11  </body>
12</html>
```

# Server-Side Rendering va Progressiv Gidratsiyalash haqida

Bu maʼlumotlar hujjat sarlavhasi va stylesheet kabi mazmunni to’g’ri renderlash uchun ilovamiz foydalanishi kerak bo’lgan foydali maʼlumotlarni o’z ichiga oladi.

Agar biz `renderToString` usuli yordamida `App` komponentini server orqali render qilmoqchi bo‘lsak, ilova ushbu meta ma’lumotni yuklash va qayta ishlashni boshlashdan oldin barcha ma’lumotlarni qabul qilishini kutishimiz kerak edi.

Buni tezlashtirish uchun `renderToNodeStream` ilovaga ushbu ma'lumotni yuklash va qayta ishlashni boshlash imkonini beradi, chunki u hali ham `App` komponentidan ma'lumotlarning bir qismini olmoqda!

Progressiv gidratsiyalash va Server Renderingni qanday amalga oshirish haqida ko'proq misollarni ko'rish uchun ushbu [GitHub repoga](https://github.com/vercel/react-ssr-example) tashrif buyuring.

Stylesheetni yetkazib berishni optimallashtirish uchun uslubli komponentlar oqimli renderlashdan qanday foydalanishini ko‘ring — bu [sozlarga havola](https://styled-components.com/docs/advanced#server-side-rendering).
