# Streaming SSR - Ijobiy va Salbiy Tomonlari

Streaming React yordamida SSR tezligini oshirishga qaratilgan va quyidagi afzalliklarni beradi.

1. **Faoliyat yaxshilanishi:**  
   Serverda renderlash boshlanganidan so'ng birinchi bayt mijozga yetib borishi sababli, TTFB (Time To First Byte) SSRga qaraganda yaxshiroq bo‘ladi.  
   Shuningdek, sahifa o'lchamidan qat'iy nazar, yanada izchil. Mijoz HTMLni qabul qilishi bilanoq uni tahlil qilishni boshlashi mumkinligi sababli, FP (First Paint) va FCP (First Contentful Paint) ham pastroq bo‘ladi.

2. **Orqa bosim bilan ishlov berish:**  
   Streaming tarmoqning orqa bosimi yoki to’xtab qolishiga yaxshi javob beradi va hatto qiyin sharoitlarda ham sezgir veb-saytlarga olib kelishi mumkin.

3. **SEO-ni qo'llab-quvvatlaydi:**  
   Streaming javobdagi qidiruv tizimi kelishuvini o'qishi mumkin, bu esa veb-saytda SEO-ga ruxsat beradi.




# Streaming SSR ga o'tishda e'tibor berilishi kerak bo‘lgan jihatlar

Shuni ta'kidlash kerakki, streamingni amalga oshirish `renderToString` dan `renderToNodeStream()` ga oddiygina almashtirish emas. SSR bilan ishlaydigan kod oqimdagi kabi ishlamasligi mumkin bo‘lgan holatlar mavjud. Quyida migratsiya oson bo‘lmasligi mumkin bo‘lgan ba'zi misollar keltirilgan.

1. **Frameworklar SSR chunkdan oldin dokumentga qo'shilishi kerak bo'lgan belgilarni yaratish uchun server-render-pass dan foydalaniladi.**  
   Misollar: oldingi `<style>` tegidagi sahifaga qaysi CSSni qo'shishni dinamik ravishda aniqlaydigan frameworklar yoki renderlash paytida `<head>` hujjatiga elementlar qo'shadigan frameworklar.  
   Buning uchun vaqtinchalik yechim bu yerda muhokama qilindi.

2. **Kod bu yerda `renderToStaticMarkup` sahifa shablonini yaratish uchun ishlatiladi va `renderToString` chaqiruvlari dinamik tarkibni yaratish uchun o'rnatilgan.**  
   Komponentga mos keladigan string bu holatlarda kutilganligi sababli, uni stream bilan almashtirib bo'lmaydi.  
   Bu yerda keltirilgan bunday kodning namunasi quyidagicha.



```javascript
res.write("<!DOCTYPE html>");

res.write(renderToStaticMarkup(
  <html>
    <head>
      <title>My Page</title>
    </head>
    <body>
      <div id="content">
        { renderToString(<MyPage />) }
      </div>
    </body>
  </html>
));


# Streaming va Progressiv Gidratsiya
Streaming va progressiv gidratsiya ikkalasi ham sof SSR (Server-Side Rendering) va CSR (Client-Side Rendering) tajribasi o'rtasidagi bo'shliqni bartaraf etishga yordam beradi.

Keling, biz o'rgangan barcha patternlarni solishtiramiz va ularning turli vaziyatlarga mosligini tushunishga harakat qilamiz.
