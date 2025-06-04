# React Server komponentlari

React jamoasi [nol o'lchamli React Server komponentlari](https://reactjs.org/blog/2020/12/21/data-fetching-with-react-server-components.html) ustida ishlamoqda, ular zamonaviy UXni serverga asoslangan mental model bilan faollashtirishga qaratilgan. Bu komponentlarning Server-side rendering (SSR) dan ancha farq qiladi va client tomoni JavaScript to'plamlarini sezilarli darajada [kamaytirishga](https://twitter.com/sophiebits/status/1341098388062756867) olib kelishi mumkin.

Ushbu ishning yo'nalishi hayajonli va u hali ishlab chiqarishga tayyor bo'lmasa-da, kuzatib borishga arziydi. Quyidagi manbalar qiziqarli bo'lishi mumkin:

* Huddi [Den va Lorenning nutqi](https://www.youtube.com/watch?v=TQQPAU21ZUw&feature=emb_title) tomosha qilishga arziganidek [RFC](https://github.com/reactjs/rfcs/blob/bf51f8755ddb38d92e23ad415fc4e3c02b95b331/text/0000-server-components.md) ham o’qishga arziydi.  
* Next.js uchun reaksiya 18 holati va Next.js uchun Server komponentlari yo‘l xaritasi  
* [React 18 beta status](https://twitter.com/reactjs/status/1460380211262930948)  
* [Shopify Hydrogen va Server Component](https://shopify.dev/custom-storefronts/hydrogen/framework/react-server-components)lari

## Server-side rendering cheklovlari

Bugungi kunda ьшощя tomonidan JavaScriptning server tomonida ko'rsatilishi optimal bo'lmagan bo'lishi mumkin. Komponentlaringiz uchun JavaScript serverda HTML qatoriga aylantiriladi. Ushbu HTML brauzerga yetkaziladi, natijada First Contentful Paint yoki Largest Contentful Paint paydo bo'lishi mumkin.

Biroq, ko'pincha gidratsiya bosqichi orqali erishiladigan interaktivlik uchun JavaScript hali ham olinishi kerak. Server tomonida ko'rsatish odatda sahifani dastlabki yuklash uchun ishlatiladi, shuning uchun gidratsiyadan keyin uni qayta ishlatmaysiz.

Eslatma:

To'g'ri bo'lsa-da, SSR-dan foydalanadigan va mijozni umuman gidratsiyalamaslik uchun faqat server uchun React ilovasini yaratish mumkin bo'lsa-da, modeldagi og'ir interaktivlik ko'pincha Reactdan tashqariga chiqishni o'z ichiga oladi. Server komponentlari yoqadigan gibrid model buni har bir komponent asosida hal qilish imkonini beradi.

React Server komponentlari yordamida bizning komponentlarimiz muntazam ravishda qayta yuklanishi mumkin. Yangi ma'lumotlar mavjud bo'lganda qayta ko'rsatiladigan komponentlarga ega dastur serverda ishga tushiriladi, bu mijozga qancha kod yuborish kerakligini cheklaydi.

*\[RFC\]: Dasturchilar doimiy ravishda uchinchi taraf paketlaridan foydalanishni tanlashlari kerak. Ba'zi markdown yoki sanani formatlash uchun paketdan foydalanish dasturchi sifatida biz uchun qulay, ammo bu kod hajmini oshiradi va foydalanuvchilarimiz ish faoliyatini yomonlashtiradi.*

```js
1 // *Before* Server Components
2 import marked from "marked"; // 35.9K (11.2K gzipped)
3 import sanitizeHtml from "sanitize-html"; // 206K (63.3K gzipped)
4
5 function NoteWithMarkdown({text}) {
6  const html = sanitizeHtml(marked(text));
7  return (/* render */);
8 }
```

## Server komponentlari

Reactning yangi Server komponentlari JavaScriptni qo'shmasdan turib, oraliq abstraksiya formatida ko'rsatish imkonini beruvchi server tomonida renderlashni qo'llab-quvvatlaydi. Bu ikkalasi ham server treeni mijoz tomonidagi tree bilan holatni yo'qotmasdan birlashtirishga imkon beradi va ko'proq komponentlar uchun masshtablash imkonini beradi.

Server komponentlari SSR o'rnini bosmaydi. Birga bog'langanda ular oraliq formatda tezkor renderlashni qo'llab-quvvatlaydi, so'ngra uni HTMLga ko'rsatuvchi server tomonidagi renderlash infratuzilmasiga ega bo'lib, early paintlar tez ishlashini ta'minlaydi. Xuddi SSR boshqa ma'lumotlarni olish mexanizmlari bilan qanday qo'llanilsa, server komponentlari chiqaradigan mijoz komponentlarini SSR dan ham shunday foydalanamiz. 

Biroq, bu safar JavaScript to’plami sezilarli darajada kichikroq bo'ladi. Dastlabki tadqiqotlar shuni ko'rsatdiki, to’plamlar hajmi bo'yicha g'alaba qozonish sezilarli bo'lishi mumkin (-18-29%), ammo keyingi infratuzilma ishlari tugallangach, React jamoasi tabiatda g'alabalar haqida aniqroq tasavvurga ega bo'ladi.

*\[RFC\]: Agar biz yuqoridagi misolni Server komponentiga koʻchirsak, biz funksiyamiz uchun aynan bir xil koddan foydalanishimiz mumkin, lekin uni mijozga yuborishdan saqlanamiz – code savings 240K dan ortiq (siqilmagan):*

```js
1 import marked from "marked"; // zero bundle size
2 import sanitizeHtml from "sanitize-html"; // zero bundle size
3
4 function NoteWithMarkdown({text}) {
5  // same as before
6 }
```

## Kodni avtomatik ravishda qismlarga ajratish

Kodni bo'lish orqali faqat foydalanuvchilarga kerak bo'lgan kodlarga xizmat ko'rsatish eng yaxshi amaliyot hisoblanadi. Bu sizga ilovangizni mijozga kamroq kod yuborishni talab qiladigan kichikroq paketlarga ajratish imkonini beradi. Server komponentlaridan oldin “split-pointlar”ni aniqlash uchun qo‘lda React.lazy() dan foydalaniladi yoki yangi qismlar yaratish uchun routerlar/sahifalar kabi meta-framework tomonidan evristik to‘plamga tayanadi.

```js
1 // *Before* Server Components
2 import React from "react";
3
4 // one of these will start loading *when rendered on the client*:
5 const OldPhotoRenderer = React.lazy(() => import("./OldPhotoRenderer.js"));
6 const NewPhotoRenderer = React.lazy(() => import("./NewPhotoRenderer.js"));
7
8 function Photo(props) {
9  // Switch on feature flags, logged in/out, type of content, etc:
10  if (FeatureFlags.useNewPhotoRenderer) {
11    return <NewPhotoRenderer {...props} />;
12  } else {
13    return <PhotoRenderer {...props} />;
14  }
15 }
```

Kodni ajratish bilan bog'liq ba'zi qiyinchiliklar:

* Meta-frameworkdan tashqari (masalan, Next.js), siz import bayonotlarini dinamik import bilan almashtirib, ushbu optimallashtirishni mustaqil hal qilishingiz kerak bo'ladi.  
* Ilova foydalanuvchi tajribasiga ta'sir qiluvchi komponentni yuklashni boshlaganda kechikishi mumkin.

Server komponentlari mumkin bo'lgan kodni ajratish nuqtalari sifatida Client komponentlaridagi barcha oddiy importlarni ko'rib chiqadigan avtomatik kod bo'linishini joriy qiladi. Shuningdek, ular ishlab chiquvchilarga qaysi komponentdan ancha oldinroq (serverda) foydalanishni tanlash imkonini beradi, bu esa mijozga uni renderlash jarayonida avvalroq olish imkonini beradi.

```js
1 import React from "react";
2
3 // one of these will start loading *once rendered and streamed to the client*:
4 import OldPhotoRenderer from "./OldPhotoRenderer.client.js";
5 import NewPhotoRenderer from "./NewPhotoRenderer.client.js";
6
7 function Photo(props) {
8  // Switch on feature flags, logged in/out, type of content, etc:
9  if (FeatureFlags.useNewPhotoRenderer) {
10    return <NewPhotoRenderer {...props} />;
11  } else {
12    return <PhotoRenderer {...props} />;
13  }
14 }
```

## Server Componentlari Next.js SSR o’rnini bosadimi?

Yo'q. Ular butunlay boshqa. Tadqiqot va eksperimentlar davom etar ekan, Server komponentlarini dastlab qabul qilish aslida Next.js kabi meta-frameworklar orqali sinab ko'riladi.

Dan Abramovdan Next.js SSR va Server komponentlari o'rtasidagi farqlarni [yaxshi tushuntirish](https://news.ycombinator.com/item?id=25499171) uchun:

* Server komponentlari uchun kod hech qachon mijozga yetkazilmaydi. Reactdan foydalangan holda SSRning ko'plab ilovalarida komponent kodi clientga JavaScript to’plami orqali yuboriladi. Bu interaktivlikni kechiktirishi mumkin.  
* Server komponentlari treening istalgan joyidan orqa qismga kirish imkonini beradi. Next.js-dan foydalanayotganda siz getServerProps() orqali orqa qismga kirishga odatlangansiz, bu faqat yuqori darajadagi sahifada ishlash chekloviga ega. Tasodifiy npm komponentlari buni qila olmaydi.  
* Tree ichidagi client side holatini saqlab qolgan holda server komponentlari qayta yuklanishi mumkin. Buning sababi, asosiy transport mexanizmi shunchaki HTMLga qaraganda ancha boy bo'lib, server-rendered partni (masalan, qidiruv natijalari ro'yxati kabi) ichidagi holatni o'chirmasdan (masalan, qidiruv kiritish matni, fokus, matn tanlash) qayta tiklash imkonini beradi.

Server komponentlari uchun dastlabki integratsiya ishlarining ba'zilari veb-paket plaginlari orqali amalga oshiriladi, masalan:

* Barcha mijoz komponentlarini topadi,  
* IDs \=\> chunk URL-lar o’rtasida mapping yaratadi,  
* Node.js loader client komponentlariga importni ushbu mapga havolalar bilan almashtiradi.  
* Ushbu ishlarning ba'zilari chuqurroq integratsiyani talab qiladi (masalan, Routing kabi qismlar bilan), shuning uchun uni Next.js kabi framework bilan ishlash qimmatli bo'ladi.

Dan ta'kidlaganidek, ushbu ishning maqsadlaridan biri meta-frameworklarni yanada yaxshilashga imkon berishdir.

## Batafsil ma’lumot oling va  React jamoasi bilan fikr-mulohazalaringizni bo’lishing

Ushbu ish haqida ko'proq bilish uchun [Dan va Lorenning nutqini tomosha qiling](https://reactjs.org/blog/2020/12/21/data-fetching-with-react-server-components.html), [RFC](https://reactjs.org/blog/2020/12/21/data-fetching-with-react-server-components.html)ni o'qing va uni ko’rish uchun [Server komponentlari demosini](http://github.com/reactjs/server-components-demo) tekshiring. Sebastyan Markbåge, Lauren Tan, Jozef Savona va Dan Abramovga server komponentlari borasidagi ishlari uchun rahmat.

Mavzuga aloqador qiziq ma’lumotlar:

* [Lauren Tan on Server Components](https://twitter.com/sugarpirate_/status/1341141198258524163)  
* [Sophie Alpert explaining them](https://twitter.com/sophiebits/status/1341098388062756867)  
* [Sebastian Markbåge with a discussion on hydration](https://twitter.com/sebmarkbage/status/1341102430147276803)  
* [HN discussion thread](https://news.ycombinator.com/item?id=25497065) 