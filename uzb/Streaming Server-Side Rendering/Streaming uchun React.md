Streaming uchun React

React 2016-yilda chiqarilgan React 16-da streaming uchun supportni joriy qildi. Quyidagi API-lar streamingni support qilish uchun ReactDOMServerga kiritilgan.

1. ReactDOMServer.renderToNodeStream(element): Bu funksiyadan chiqadigan HTML ReactDOMServer.renderToString(element) bilan bir xil, lekin string o’rniga Node.js readablestream formatida berilgan. Funksiya faqat HTMLni stream sifatida render qilish uchun serverda ishlaydi. Ushbu streamni qabul qiluvchi mijoz keyinchalik sahifani gidratsiyalash va interaktiv qilish uchun ReactDOM.hydrate() ga chaqirishi mumkin.

 2. ReactDOMServer.renderToStaticNodeStream(element): Bu ReactDOMServer.renderToStaticMarkup(element) ga mos keladi. HTML output bir xil, lekin stream formatida. U serverda statik, interaktiv bo'lmagan sahifalarni render qilish va keyin ularni mijozga uzatish uchun ishlatilishi mumkin.

Ikkala funksiya tomonidan o'qiladigan stream output siz undan o'qishni boshlaganingizdan so'ng baytlarni chiqarishi mumkin. Bunga javob obyekti kabi o'qiladigan streamni yoziladigan streamga ulash orqali erishish mumkin. Javob obyekti yangi qismlarni render qilinishini kutayotganda mijozga ma'lumotlar qismlarini asta-sekin yuboradi.

Bularning barchasini jamlagan holda, keling, bu yerda e'lon qilingan kod skeletini ko'rib chiqaylik.

import { renderToNodeStream } from 'react-dom/server';
2import Frontend from '../client';
3


4app.use('*', (request, response) => {
5  // Send the start of your HTML to the browser
6  response.write('<html><head><title>Page</title></head><body><div id="root">');
7


8  // Render your frontend to a stream and pipe it to the response
9  const stream = renderToNodeStream(<Frontend />);
10  stream.pipe(response, { end: 'false' });
11  // Tell the stream not to automatically end the response when the renderer finishes.
12


13  // When React finishes rendering send the rest of your HTML to the browser
14  stream.on('end', () => {
15    response.end('</div></body></html>');
16  });
17});


Oddiy SSR Vs Streaming uchun TTFB va First Meaningful Content o'rtasidagi taqqoslash quyidagi rasmda mavjud.

![Logo](hhttps://mxstbr.com/static/images/renderToString.png)
