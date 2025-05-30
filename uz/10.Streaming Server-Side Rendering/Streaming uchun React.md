# Streaming uchun React

React 2016-yilda chiqarilgan React 16 da streaming uchun supportni joriy qildi. Quyidagi API-lar streamingni support qilish uchun `ReactDOMServer` ga kiritilgan:

1. **`ReactDOMServer.renderToNodeStream(element)`**  
   Bu funksiyadan chiqadigan HTML `ReactDOMServer.renderToString(element)` bilan bir xil, lekin string o’rniga Node.js `readable stream` formatida berilgan.  
   Funksiya faqat HTMLni stream sifatida render qilish uchun serverda ishlaydi. Ushbu streamni qabul qiluvchi mijoz keyinchalik sahifani gidratsiyalash va interaktiv qilish uchun `ReactDOM.hydrate()` ga chaqirishi mumkin.

2. **`ReactDOMServer.renderToStaticNodeStream(element)`**  
   Bu `ReactDOMServer.renderToStaticMarkup(element)` ga mos keladi. HTML output bir xil, lekin stream formatida.  
   U serverda statik, interaktiv bo'lmagan sahifalarni render qilish va keyin ularni mijozga uzatish uchun ishlatilishi mumkin.


Ikkala funksiya tomonidan o'qiladigan stream output siz undan o'qishni boshlaganingizdan so'ng baytlarni chiqarishi mumkin.  
Bunga javob obyekti kabi o'qiladigan streamni yoziladigan streamga ulash orqali erishish mumkin.  
Javob obyekti yangi qismlarni render qilinishini kutayotganda mijozga ma'lumotlar qismlarini asta-sekin yuboradi.

Bularning barchasini jamlagan holda, keling, bu yerda e'lon qilingan kod skeletini ko'rib chiqaylik.

```js
import { renderToNodeStream } from 'react-dom/server';
import Frontend from '../client';

app.use('*', (request, response) => {
  // Send the start of your HTML to the browser
  response.write('<html><head><title>Page</title></head><body><div id="root">');

  // Render your frontend to a stream and pipe it to the response
  const stream = renderToNodeStream(<Frontend />);
  stream.pipe(response, { end: false }); 
  // Tell the stream not to automatically end the response when the renderer finishes.

  // When React finishes rendering send the rest of your HTML to the browser
  stream.on('end', () => {
    response.end('</div></body></html>');
  });
});
```


Oddiy SSR va Streaming uchun **TTFB** (Time To First Byte) hamda **First Meaningful Content** o'rtasidagi taqqoslash quyidagi rasmda mavjud:

![SSR vs Streaming TTFB va First Meaningful Content](https://mxstbr.com/static/images/renderToString.png)
