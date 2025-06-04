# Asinxron komponentlar

Katta veb-ilovalarni ishlab chiqishda performance eng muhim jihat hisoblanadi. Sahifa yuklanish tezligi va uning interaktiv elementlarining  javob berish tezligi foydalanuvchi tajribasiga jiddiy ta'sir ko'rsatishi mumkin. Veb-ilovalar hajmi va murakkabligi oshgani sayin, katta kod to'plamlari faqat kerak bo'lganda yuklanishini ta'minlash muhim bo'lishi mumkin. Vuega asinxron komponentlar bilan tanishib chiqishni ham unutmang.

Avvalgi [maqolamizdan](https://www.patterns.dev/vue/components) biz komponentlar UI yaratish uchun asosiy qurilish bloklari ekanligini tushundik. Odatda, komponentlardan foydalanilganda, zarurat bo'lmasa ham, ular avtomatik ravishda yuklanadi va ishlanadi.

Shu bilan birga, asinxron komponentlar bizga komponentlarni faqat zarur bo'lganda yoki ma’lum shartlar bajarilganda yuklanadigan va tahlil qilinadigan qilib belgilash imkonini beradi. Buni yaxshiroq tushunish uchun mashqni ko'rib chiqamiz. 

Faraz qilaylik, bizda oddiy modal komponent bor edi, u parent tomonidan button bosilganda paydo bo'ladi. Modal.vue komponent fayl faqat modal qanday ko'rinishini belgilaydigan template va uslublarni o'z ichiga oladi.

```js
<template>
  <div class="modal-mask">
    <div class="modal-container">
      <div class="modal-body">
        <h3>This is the modal!</h3>
      </div>


      <div class="modal-footer">
        <button class="modal-default-button" @click="$emit('close')">OK</button>
      </div>
    </div>
  </div>
</template>


<style>
  .modal-mask {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    transition: opacity 0.3s ease;
  }


  .modal-container {
    width: 300px;
    margin: auto;
    padding: 20px 30px;
    background-color: #fff;
    border-radius: 2px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
    transition: all 0.3s ease;
  }


  .modal-body h3 {
    margin-top: 0;
    color: #42b983;
  }


  .modal-default-button {
    float: right;
  }
</style>
```

Ilovaning asosiy komponentida biz modal komponentni va bosilganda modal komponentning ko'rinishini reaktiv boolean qiymat (showModal) yordamida almashtiradigan buttonni ko'rsatishimiz mumkin. Modal showModal reaktiv xossasining qiymatiga qarab shartli ravishda ko'rsatiladi yoki yashiriladi.

```js
<template>
  <button id="show-modal" @click="showModal = true">Show Modal</button>
  <Modal v-if="showModal" :show="showModal" @close="showModal = false" />
</template>


<script setup>
  import { ref } from "vue";
  import Modal from "./components/Modal.vue";


  const showModal = ref(false);
</script>
```

Show Modal butonni bosganimizda sahifada modal ko'rsatiladi.

[https://res.cloudinary.com/ddxwdqwkr/image/upload/c\_scale,w\_900,f\_auto/v1692047881/patterns.dev/Images/vue/async-components/simple-modal.gif](https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900,f_auto/v1692047881/patterns.dev/Images/vue/async-components/simple-modal.gif)

Ushbu misoldan biz modal komponent faqat ma'lum bir vaziyatda \- foydalanuvchi Modalni ko'rsatish tugmasini bosganida ko'rsatilishini ko'rishimiz mumkin. Shunga qaramay, komponent bilan bog'langan JavaScript bundle modal paydo bo'lishidan oldin ham butun veb-sahifa yuklanganda avtomatik ravishda yuklanadi. Buni brauzerimizning tarmoq jurnallaridan ko'rish mumkin.

[https://res.cloudinary.com/ddxwdqwkr/image/upload/c\_scale,w\_900,f\_auto/v1692048408/patterns.dev/Images/vue/async-components/modal-bundle-initial-load.gif](https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900,f_auto/v1692048408/patterns.dev/Images/vue/async-components/modal-bundle-initial-load.gif)

Bu yaxshi holat. Biroq, modal to'plamining o'lchami haqiqatan ham katta bo'lganda yoki dasturda bunday komponentlar ko'p bo'lgan sharoitlarda bu dastlabki yuklash vaqtining kechikishiga olib kelishi mumkin. Har bir qo'shilgan to'plam bilan, hatto u kamdan-kam ishlatiladigan komponentlar bilan bog'liq bo'lsa ham, dastlabki sahifani yuklash uchun ketadigan vaqt oshadi.

## defineAsyncComponent

Bu yerda Vue definAsyncComponent() funksiyasi yordamida komponentlarni asinxron yuklash orqali ilovani kichikroq qismlarga ajratish imkonini beradi.

```js
import { defineAsyncComponent } from "vue";


const AsyncComp = defineAsyncComponent(() => {
  return new Promise((resolve, reject) => {
    // ...load component from the server
    resolve(/* loaded component */);
  });
});

```

DefineAsyncComponent() funksiyasi import qilingan komponentni hal qiladigan promiseni qaytaruvchi yuklovchi funksiyani qabul qiladi. Komponent muvaffaqiyatli yuklangandan so'ng Promise'ni hal qiluvchi callback chaqirilishi kerak, agar yuklash jarayonida biror xato bo'lsa, reject callback chaqiriladi. 

Biroq, yuqoridagi kabi asinxron komponent funksiyamizni belgilash o‘rniga, biz ECMAScript modulini (ya’ni, bizning holatimizda komponent) asinxron tarzda yuklash uchun [dinamik importlardan](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import) foydalanishimiz mumkin. Bunga import() sintaksisi yordamida erishiladi.

```js
import { defineAsyncComponent } from "vue";


export const AsyncComp = defineAsyncComponent(() =>
  import("./components/MyComponent.vue")
);

```

Keling, modal misolimiz uchun buni amalda ko'rib chiqaylik. Biz AsyncModal.js nomli yangi fayl yaratamiz, faylda vue kutubxonasidan defineAsyncComponent() funksiyasini import qilamiz va defineAsyncComponent() funksiya chaqiruviga AsyncModal constantni tayinlaymiz.

```js
import { defineAsyncComponent } from "vue";

export const AsyncModal = defineAsyncComponent();
```

DefineAsyncComponent() funksiya chaqiruvida biz avval yaratilgan Modal komponentni asinxron import qilish uchun import() sintaksisidan foydalanamiz.

```js
import { defineAsyncComponent } from "vue";


export const AsyncModal = defineAsyncComponent(() => import("./Modal.vue"));
```

Bizning asosiy ilova komponentimizda endi Modal komponent o'rniga AsyncModal asinxron komponentini import qilamiz va ishlatamiz.

```js
<template>
  <button id="show-modal" @click="showModal = true">Show Modal</button>
  <AsyncModal v-if="showModal" :show="showModal" @close="showModal = false" />
</template>


<script setup>
  import { ref } from "vue";
  import { AsyncModal } from "./components/AsyncModal";


  const showModal = ref(false);
</script>
```

Ushbu kichik o'zgarish bilan bizning modal komponentimiz endi asinxron ravishda yuklanadi\! Ilova veb-sahifamiz dastlab yuklanganda, biz Modal komponent uchun to'plam sahifa yuklanganda avtomatik ravishda yuklanmasligini tan olamiz.

[https://res.cloudinary.com/ddxwdqwkr/image/upload/c\_scale,w\_900,f\_auto/v1692054312/patterns.dev/Images/vue/async-components/modal-bundle-no-initial-load.gif](https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900,f_auto/v1692054312/patterns.dev/Images/vue/async-components/modal-bundle-no-initial-load.gif)

Ko'rsatiladigan modalni ishga tushirish uchun tugmani bosganimizda, modal komponent ko'rsatilayotganda to'plam asinxron ravishda yuklanganligini ko'ramiz.

[https://res.cloudinary.com/ddxwdqwkr/image/upload/c\_scale,w\_900,f\_auto/v1692054534/patterns.dev/Images/vue/async-components/modal-async-load.gif](https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900,f_auto/v1692054534/patterns.dev/Images/vue/async-components/modal-async-load.gif)

## Loading va error UI 

DefineAsyncComponent() yordamida Vue dasturchilarga komponentlarni asinxron yuklash vositasidan ko'proq narsani taqdim etadi. Shuningdek, u yuklash jarayonida foydalanuvchilarga fikr-mulohazalarni ko'rsatish va yuzaga kelishi mumkin bo'lgan xatolarni hal qilish imkoniyatlarini taklif etadi. Tarmoq sharoitlari idealdan past bo’lsada, asinxron yuklash jarayonida xatolik yuzaga kelsa ham foydalanuvchining qoniqarli tajribasini ta'minlaydi. 

### loadingComponent

Ba'zida biz komponentni olish paytida foydalanuvchilarga vizual fikr-mulohazalarni taqdim etishni xohlashimiz mumkin, ayniqsa yuklash vaqti muhim bo'lsa. Bunga erishish uchun defineAsyncComponent() da yuklash bosqichida ko'rsatadigan komponentni belgilash imkonini beruvchi loadingComponent opsiyasi mavjud.

DefineAsyncComponent() funksiyamizda qo‘shimcha opsiyalarni e’lon qilishimiz sababli modal komponentni asinxron import qilish uchun loader() funksiyasidan foydalanamiz.

```js
import { defineAsyncComponent } from "vue";


export const AsyncModal = defineAsyncComponent({
  loader: () => import("./Modal.vue"),
});

```

Faraz qilaylik, bizda Loading.vue komponent faylida quyidagicha aniqlangan oddiy yuklash komponenti shabloniga egamiz:

```js
<template>
  <p>Loading...</p>
</template>
```

Keyin biz ushbu yuklash komponentini defineAsyncComponent() funksiyamizdagi loadingComponent parametrimizning qiymati sifatida belgilashimiz mumkin.

```js
import { defineAsyncComponent } from "vue";
import Loading from "./Loading.vue";


export const AsyncModal = defineAsyncComponent({
  loader: () => import("./Modal.vue"),
  loadingComponent: Loading,
});

```

Modal komponent asinxron yuklangan bo'lsa, foydalanuvchi Loading... xabari bilan taqdim etiladi. Tez internet ulanishlarida buni ko‘rish qiyin bo‘lishi mumkin, shuning uchun modal komponentlar to‘plami hali ham yuklanayotganda Loading... xabarini ko‘rish xatti-harakatlarini kuzatish uchun brauzer tarmog‘imiz jurnallarida sekin 3G tarmog‘iga taqlid qilamiz.

[https://res.cloudinary.com/ddxwdqwkr/image/upload/c\_scale,w\_900,f\_auto/v1692055716/patterns.dev/Images/vue/async-components/modal-loading-component.gif](https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900,f_auto/v1692055716/patterns.dev/Images/vue/async-components/modal-loading-component.gif)

### errorComponent

Muayyan sharoitlarda (masalan, yomon internet aloqasi), asinxron komponentni yuklamaslik ehtimoli bo'lishi mumkin. Ushbu ssenariylar uchun error haqida fikr-mulohaza bildirish yaxshi foydalanuvchi tajribasi uchun muhimdir. DefineAsyncComponent() funksiyasi bunday vaziyatlarni hal qilish uchun errorComponent opsiyasini taklif qiladi, bu bizga yuklashda error yuz berganda ko‘rsatiladigan komponentni belgilash imkonini beradi.

Error.vue komponent faylida xato komponenti shabloniga o'xshash tarzda aniqlangan deb faraz qilaylik:

```js
<template>
  <p>Error!</p>
</template>
```

Ushbu komponentni asinxron modal sozlamalarimizga integratsiya qilish uchun biz uni errorComponent parametrimizning qiymati sifatida belgilashimiz mumkin.

```js
import { defineAsyncComponent } from "vue";
import Loading from "./Loading.vue";
import Error from "./Error.vue";


export const AsyncModal = defineAsyncComponent({
  loader: () => import("./Modal.vue"),
  loadingComponent: Loading,
  errorComponent: Error,
});

```

Buni amalda tasavvur qilish uchun biz brauzerimiz ishlab chiqaruvchi vositalarida Oflayn tarmoq rejimini simulyatsiya qilishimiz va modalni ishga tushirishga harakat qilishimiz mumkin. Biz modal komponent yuklanmasa, Error komponenti shablonini ko'rsatamiz.

[https://res.cloudinary.com/ddxwdqwkr/image/upload/c\_scale,w\_900,f\_auto/v1692056166/patterns.dev/Images/vue/async-components/modal-error-component.gif](https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900,f_auto/v1692056166/patterns.dev/Images/vue/async-components/modal-error-component.gif)

Biz qilgan barcha o'zgarishlar bilan bizning ilovamiz quyida ko'rinadi.

```js
1 import { defineAsyncComponent } from "vue";
2  import Loading from "./Loading.vue";
3  import Error from "./Error.vue";
4 
5  export const AsyncModal = defineAsyncComponent({
6    loader: () => import("./Modal.vue"),
7    loadingComponent: Loading,
8    errorComponent: Error
9  });
```

DefineAsyncComponent() funksiyasi dasturchilarga asinxron yuklash harakati va foydalanuvchi tajribasi ustidan batafsil nazoratni taʼminlovchi kechikish, kutish vaqti, suspensible va onError() kabi qo’shimcha variantlarni qabul qiladi. Ushbu xususiyatlar haqida batafsil ma'lumot olish uchun [API hujjatlarini](https://vuejs.org/api/general.html#defineasynccomponent) ko'rib chiqing.

DefineAsyncComponent() funksiyasi Vue ilovasining dastlabki yukini boshqariladigan qismlarga ajratishda yordam berishi mumkin, bu orqali ma’lum komponentlarning yuklanishi kerak bo‘lgunga qadar kechiktiriladi. Ayniqsa ilova katta paket o’lchamiga ega bo’lgan ko’plab komponentlarga ega bo’lsa, u sahifani yuklash vaqtini va ilovaning umumiy ish faoliyatini yaxshilashga yordam beradi. 

## Foydali resurslari

* [Async Components | Vue Documentation](https://vuejs.org/guide/components/async.html#async-components)
