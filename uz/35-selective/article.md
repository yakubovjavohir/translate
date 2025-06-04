# Kerakli ma’lumotlarni gidratsiyalash

[Oldingi maqolalarda](https://www.patterns.dev/posts/ssr) biz gidratsiyali SSR foydalanuvchi tajribasini qanday yaxshilashi mumkinligini ko'rib chiqdik. React butun tree yaratilgandan so'ng mijozga yuboriladigan react-dom/server kutubxonasi taqdim etgan renderToString usuli yordamida serverda treeni (tezda) yaratishi mumkin. Ko'rsatilgan HTML JavaScript to’plami yuklab olinmaguncha va yuklanmaguncha interaktiv bo'lmaydi, shundan so'ng React hidratlanish uchun tree bo'ylab yuradi va ishlov beruvchilarni biriktiradi.

Biroq, bu yondashuv joriy amalga oshirish bilan bog'liq ayrim cheklovlar tufayli ba'zi ishlash muammolariga olib kelishi mumkin.

Server tomonidan ko'rsatilgan HTML tree mijozga yuborilishidan oldin barcha komponentlar tayyor bo'lishi kerak. Bu shuni anglatadiki, tashqi API callga yoki ba'zi kechikishlarga olib keladigan har qanday jarayonga tayanishi mumkin bo'lgan komponentlar kichikroq komponentlarning tezda render qilinishini bloklashi mumkin.

Slower tree generationdan tashqari, yana bir muammo shundaki, React treeni faqat bir marta gidratsiyalaydi. Bu shuni anglatadiki, React har qanday komponentni gidratsiyalashdan oldin, ulardan birortasini gidratsiyalashdan avval barcha komponentlar uchun JavaScriptni olishi kerak. Bu shuni anglatadiki, kichikroq komponentlar (kichikroq to'plamlar bilan) React veb-saytingizdagi istalgan narsani gidratsiyalay olmaguncha, kattaroq komponentlar kodini olish va yuklash uchun kutishlari kerak. Bu vaqt davomida veb-sayt interaktiv bo'lmagan.

[https://res.cloudinary.com/ddxwdqwkr/video/upload/f\_auto/v1631144761/patterns.dev/selective-hydration-before.mp4](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1631144761/patterns.dev/selective-hydration-before.mp4)

React 18 bu muammolarni server tomonida streaming renderlashni gidratsiyaga yangi yondashuv, ya’ni Selective hydration bilan birlashtirishga imkon berish orqali hal qiladi. 

Biz ilgari ko'rib chiqqan renderToString usulidan foydalanish o'rniga, endi biz serverda yangi pipeToNodeStream usuli yordamida HTML renderini streamlashimiz mumkin.

Bu usul createRoot usuli va Suspense bilan birgalikda kattaroq komponentlar tayyor bo‘lishini kutmasdan HTML translyatsiyasini boshlash imkonini beradi. Bu shuni anglatadiki, biz SSR-dan foydalanganda komponentlarni faqat zarur bo’lgandagina yuklashimiz mumkin, bu ilgari (haqiqatdan ham) mumkin emas edi\!

```js
1 import { pipeToNodeStream} from "react-dom/server";
2
3 export function render(res) {
4  const data = createServerData();
5  const { startWriting, abort } = pipeToNodeWritable(
6    <DataProvider data={data}>
7      <App assets={assets} />
8    </DataProvider>,
9    res,
10    {
11      onReadyToStream() {
12        res.setHeader('Content-type', 'text/html');
13        res.write('<!DOCTYPE html>');
14        startWriting();
16 }
17  );
18 };
```

Bu kodlar va [*codesandbox*](https://codesandbox.io/s/festive-star-9hfqt?file=/src/index.js:193-320) dan ilhomlanib tuzilgan soddalashtirilgan misol. 

Ilgari tree va TTI ni sekinlashtirgan Comments komponenti endi Suspensega o'ralgan. Bu Reactga ushbu komponent tree avlodining qolgan qismini sekinlashtirishiga yo'l qo'ymaslikni aytadi. Buning o'rniga, React fallback komponentlarini dastlab rendered HTML sifatida kiritadi va mijozga yuborilishidan oldin treening qolgan qismini yaratishda davom etadi.

[https://res.cloudinary.com/ddxwdqwkr/video/upload/eo\_9/v1631144841/patterns.dev/selective-hydration-after.mp4](https://res.cloudinary.com/ddxwdqwkr/video/upload/eo_9/v1631144841/patterns.dev/selective-hydration-after.mp4)

Ayni paytda biz Comments komponenti uchun zarur bo‘lgan tashqi ma’lumotlarni olishda davom etmoqdamiz.

Tanlangan gidratsiya mijozga yuborilgan komponentlarni, hatto Comments komponenti yuborilishidan oldin ham gidratsiyalash imkonini beradi\!

[https://res.cloudinary.com/ddxwdqwkr/video/upload/eo\_12/v1631144841/patterns.dev/selective-hydration-after.mp4](https://res.cloudinary.com/ddxwdqwkr/video/upload/eo_12/v1631144841/patterns.dev/selective-hydration-after.mp4)

Comments komponenti uchun ma'lumotlar tayyor bo'lgach, React ushbu komponent uchun HTMLni, shuningdek, zaxira yuklagichni almashtirish uchun kichik \<script\>ni translyatsiya qilishni boshlaydi.

React gidratsiyani yangi HTML kiritilgandan keyin boshlaydi.

[https://res.cloudinary.com/ddxwdqwkr/video/upload/f\_auto/v1631147513/patterns.dev/selective-hydration-after-2.mp4](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1631147513/patterns.dev/selective-hydration-after-2.mp4)

React 18 React bilan SSR dan foydalanishda odamlar tez-tez duch keladigan ba'zi muammolarni hal qiladi.

Streaming rendering komponentlarni serverda yaratish uchun ko‘proq vaqt talab qilishi mumkin bo‘lganligi sababli sekinroq FCP va TTI ni xavf ostiga qo‘ymasdan, ular tayyor bo‘lishi bilanoq translyatsiyalashni boshlash imkonini beradi.

Komponentlar mijozga uzatilishi bilanoq gidratsiyalanishi mumkin, chunki biz endi gidratsiyani boshlash uchun barcha JavaScriptning yuklanishini kutishimiz shart emas va barcha komponentlar gidratsiyalanmasidan oldin ilova bilan o’zaro aloqani boshlashimiz mumkin.

## Havolalar

* [New Suspense SSR Architecture in React 18](https://github.com/reactwg/react-18/discussions/37)