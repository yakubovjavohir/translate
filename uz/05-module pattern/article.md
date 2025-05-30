# Module pattern (Modul patterni)

Ilovangiz va kod baza o'sib borishi bilan, kodni qo’llab-quvvatlash va alohida qismlarga ajratish ahamiyat kasb etadi. <b>Modul patterni</b> kodingizni kichikroq, qayta ishlatiluvchi qismlarga ajratish imkonini beradi.
Kodingizni qayta ishlatiluvchi kichikroq qismlarga ajratish imkoniyatidan tashqari, modullar faylingizdagi ma'lum qiymatlarni yopiq (private) holatda saqlashga imkon beradi. Modul ichidagi deklaratsiyalar standart holatda faqat shu modul doirasida chegaralangan (inkapsulyatsiya qilingan) bo’ladi. Agar biz biror qiymatni aniq eksport qilmasak, bu qiymat moduldan tashqarida mavjud bo’lmaydi. Bu kod bazangizning boshqa qismlarida e'lon qilingan qiymatlar bilan nom to'qnashuvlarining xavfini kamaytiradi, chunki qiymatlar global qamrov (global scope)da mavjud bo’lmaydi.

# ES2015 Modullari

ES2015 ichki o'rnatilgan JavaScript modullarini taqdim etdi. Modul - bu JavaScript kodini o'z ichiga olgan fayl bo'lib, oddiy skriptdan farqli xususiyatlarga ega.
Keling, matematik funksiyalarni o'z ichiga olgan math.js deb nomlangan modul misolini ko'rib chiqaylik.

<p align="center">
  <img src="../../images/01.module.png" alt="Rasm" width="400" />
</p>

Bizda oddiy matematik amallarni bajaruvchi <b>math.js</b> fayli bor. Unda foydalanuvchilarga qo'shish, ko'paytirish, ayirish va kvadratga ko’tarish imkonini beruvchi funksiyalar mavjud.

Biroq, biz bu funksiyalardan faqat <b>math.js</b> faylida emas, balki  <b>index.js</b>  faylida ham foydalanish imkoniyatiga ega bo‘lmoqchimiz! Hozirda  <b>index.js</b>  faylida xatolik yuzaga keladi:  <b>index.js</b>  faylida add, subtract, multiply yoki square funksiyalari mavjud emas. Biz  index.js  faylida mavjud bo'lmagan funksiyalarga murojaat qilishga harakat qilyapmiz.

<p align="center">
  <img src="../../images/02.module.png" alt="Rasm" width="400" />
</p>

<b>math.js</b>funksiyalarini boshqa fayllarda ishlata olishimiz  uchun avval ularni eksport qilishimiz kerak. Moduldan kodni eksport qilish uchun export kalit so'zidan foydalanishimiz mumkin. Funksiyalarni eksport qilishning usullaridan biri - bu nomli eksport (named export)lardan foydalanish: export kalit so‘zini ommaga ochiq qilmoqchi bo‘lgan elementlar oldiga qo‘shamiz. Bu holatda, export kalit so'zini har bir funksiya oldiga qo'shishimiz kerak, chunki index.js barcha to'rtta funksiyaga murojaat qila olishi kerak.

<p align="center">
  <img src="../../images/03.module.png" alt="Rasm" width="400" />
</p>

Biz add, subtract, multiply va square funksiyalarini eksport qilinuvchi qildik! Biroq, moduldan qiymatlarni eksport qilishning o’zi ularni barcha fayllar uchun ochiq qilish uchun yetarli emas. Moduldan eksport qilingan qiymatlardan foydalanish uchun ularni kerakli faylga aniq import qilish kerak.

import kalit so'zidan foydalanib, index.js faylining yuqori qismiga qiymatlarni import qilamiz. Javascriptga bu funksiyalarni qaysi moduldan import qilmoqchi ekanligimizni bildirish uchun from  kalit so’zi va modulga nisbiy yo’l (relative path) ko’rsatishimiz kerak.

<p align="center">
  <img src="../../images/04.module.png" alt="Rasm" width="400" />
</p>

Biz index.js fayliga math.js modulidan to'rtta funksiyani import qildik! Keling, endi funksiyalardan foydalana olishimizni tekshirib ko’ramiz!

<p align="center">
  <img src="../../images/05.module.png" alt="Rasm" width="400" />
</p>

Murojaat xatosi (reference error) yo'qoldi, endi moduldan eksport qilingan qiymatlardan foydalana olamiz!

Modullarning  katta afzalligi shundaki, biz faqat export kalit so'zi yordamida aniq eksport qilgan qiymatlargagina kira olamiz. export kalit so‘zi yordamida aniq eksport qilinmagan qiymatlar faqat shu modul doirasida mavjud bo’ladi.

Keling, faqat math.js faylida mavjud bo’lishi kerak bo'lgan privateValue nomli qiymat yarataylik.

<p align="center">
  <img src="../../images/06.module.png" alt="Rasm" width="400" />
</p>

Agar payqagan bo’lsangiz privateValue oldiga export kalit so'zini qo'shmadik. privateValue oʻzgaruvchisini eksport qilmaganimiz uchun math.js modulidan tashqarida bu qiymatga kira olmaymiz!

<p align="center">
  <img src="../../images/07.module.png" alt="Rasm" width="400" />
</p>

Qiymatni modul uchun yopiq holatda saqlash orqali global qamrovni tasodifan ifloslantirish xavfi kamayadi. Modulingizdan foydalanuvchi dasturchilar yaratgan qiymatlarni tasodifan qayta yozib yuborishdan xavfsirashingiz shart emas, chunki ular siz yozgan yopiq qiymatingiz bilan bir xil nomga ega bo'lishi mumkin: bu nom to'qnashuvlarining (name collisions) oldini oladi.


Ba'zida eksport nomlari lokal qiymatlar bilan to’qnashishi mumkin.

<p align="center">
  <img src="../../images/08.module.png" alt="Rasm" width="400" />
</p>

export kalit so'zi bilan belgilangan nomli eksport (named export)lardan tashqari, standart eksport (default export)dan ham foydalanishingiz mumkin. Har bir modulda faqat bitta standart eksport bo’lishi mumkin.

Keling, add funksiyasini default export qilamiz va boshqa funksiyalarni named export sifatida qoldiramiz. Qiymat oldiga default export kalit so’zini qo’shish orqali standart qiymatni eksport qilishimiz mumkin.

<p align="center">
  <img src="../../images/09.module.png" alt="Rasm" width="400" />
</p>

Named export va default export o'rtasidagi farq - bu qiymatni moduldan eksport qilish usuli bo’lib, natijada qiymatni import qilish usulini samarali tarzda o'zgartiradi.

Avval biz nomli (named) eksportlar uchun jingalak qavslardan foydalanishimiz kerak edi: import { module } from 'module'. Standart (default) eksport bilan esa qiymatni qavslarsiz import qilishimiz mumkin: import module from 'module'.

<p align="center">
  <img src="../../images/10.module.png" alt="Rasm" width="400" />
</p>

Moduldan qavslarsiz import qilingan qiymat, agar default export mavjud bo'lsa, har doim default export qiymati bo'ladi.

JavaScript bu qiymat har doim default export bilan eksport qilingan qiymat ekanligini bilganligi sababli, import qilingan standart qiymatga uni eksport qilgan nomdan boshqa nom berishimiz mumkin. add funksiyasini add nomi bilan import qilish o'rniga, masalan, biz uni addValue ​​deb nolmashimiz mumkin. 

<p align="center">
  <img src="../../images/11.module.png" alt="Rasm" width="400" />
</p>

Biz add nomli funksiyasini eksport qilgan bo‘lsak ham, uni o‘zimizga maqul kelgan istalgan nom bilan import qilishimiz mumkin, chunki JavaScript default export’ni import qilayotganingizni biladi.

Shuningdek, biz moduldan barcha eksportlarni - barcha nomli export va default export’larni yulduzcha * yordamida va modulga beradigan nomimiz orqali import qilishimiz mumkin. Import qiymati barcha import qilingan qiymatlarni o'z ichiga olgan obyektga teng bo’ladi. Aytaylik, butun modulni math sifatida import qilmoqchimiz.

<p align="center">
  <img src="../../images/12.module.png" alt="Rasm" width="400" />
</p>

Import qilingan qiymatlar math obyektining xususiyatlari bo’ladi.

<p align="center">
  <img src="../../images/13.module.png" alt="Rasm" width="400" />
</p>

Bu holatda moduldan barcha eksportlarni import qilamiz. Buni qilishda ehtiyot bo'ling, chunki keraksiz qiymatlarni ham import qilib olishingiz mumkin.

* kalit so’zi faqat barcha eksport qilingan qiymatlarni import qiladi. Agar siz ularni aniq eksport qilmagan bo’lsangiz, modulning yopiq qiymatlar uni import qiluvchi faylda mavjud bo’lmaydi.

# React

React yordamida ilovalar yaratishda ko'pincha katta miqdordagi komponentlar bilan ishlashga to’g’ri keladi. Bu komponentlarning barchasini bitta faylga yozish o'rniga, ularni alohida fayllarga ajratishimiz mumkin, ya’ni har bir komponent uchun alohida modul yaratamiz.
Bizda ro'yxat, ro'yxat elementlari, input maydoni va tugmani o'z ichiga olgan sodda vazifalar ro'yxati (todo-list) mavjud.

<p align="center">
  <img src="../../images/14.module.png" alt="Rasm" width="400" />
</p>

<p align="center">
  <img src="../../images/15.module.png" alt="Rasm" width="400" />
</p>

<p align="center">
  <img src="../../images/16.module.png" alt="Rasm" width="400" />
</p>

<p align="center">
  <img src="../../images/17.module.png" alt="Rasm" width="400" />
</p>

Biz komponentlarimizni quyidagi alohida fayllarga ajratdik:

 - TodoList.js - List komponenti uchun
 - Button.js - moslashtirilgan Button komponenti uchun
 - Input.js - moslashtirilgan Input komponenti uchun.

 Ilovamiz davomida biz [material-ui](https://mui.com/material-ui/) kutubxonasidan import qilingan default Button va Input komponentlaridan foydalanmoqchi emasmiz. Buning o'rniga, ularning fayllaridagi styles obyektida belgilangan maxsus stillarni qo’shib, o’zimizning komponentlarimizdan foydalanmoqchimiz. Ilovamizda har safar default Button va Input  komponentlarini import qilib, ularga qayta-qayta maxsus stillar qo‘shish o‘rniga, ularni bir marta import qilib, stillar qo’shib, o’zimizning maxsus komponentimizni eksport qilishimiz mumkin.

 # Dinamik import (Dynamic import)

 Faylning yuqori qismida barcha modullarni import qilganda, ular faylning qolgan qismidan oldin yuklanadi. Ba'zi hollarda bizga modul faqat ma'lum bir shartlar asosida kerak bo’ladi. Dinamik import yordamida biz modullarni kerak bo’lganda yuklashimiz mumkin.

 <p align="center">
  <img src="../../images/18.module.png" alt="Rasm" width="400" />
</p>


Keling, oldingi misollarda ishlatilgan math.js ni dinamik ravishda import qilaylik.

Modul faqat foydalanuvchi tugmani bosgandagina yuklanadi.

 <p align="center">
  <img src="../../images/19.module.png" alt="Rasm" width="400" />
</p>

Modullarni dinamik import qilish orqali sahifi yuklash vaqtini qisqartirishimiz mumkin. Biz faqat foydalanuvchiga aynan kerak bo'lgan kodni kerak bo’lgan vaqtda yuklash, tahlil qilish va kompilyatsiya qilishimiz mumkin.

Modullarni talabga binoan (on-demand) import qilish imkoniyatidan tashqari import() funksiyasi ifoda (expressoin) qabul qilishi mumkin. Bu bizga shablon satrlar  (template literals)dan foydalanib, berilgan qiymat asosida modullarni dinamik yuklash imkonini beradi.

 <p align="center">
  <img src="../../images/20.module.png" alt="Rasm" width="400" />
</p>

Yuqoridagi misolda, foydalanuvchi “Click to load image” tugmasini bosgandagina date.js moduli import qilinadi. date.js moduli uchinchi tomon moment modulini import qiladi, u ham faqat date.js moduli yuklangandagina import qilinadi. Agar foydalanuvchiga sanalarni ko'rsatish kerak bo'lmasa, bu uchinchi tomon kutubxonasini yuklashni umuman amalga oshirmasligimiz mumkin.

Har bir rasm foydalanuvchi tugmani bosgandan so'ng yuklanadi. Rasmlar lokal .png fayllar bo'lib, ular num qiymatiga asosan yuklanadi. 

 <p align="center">
  <img src="../../images/21.module.png" alt="Rasm" width="400" />
</p>

Bu holat o’zgarmas kodli (hard-coded) modul yo'llariga bog'liq emas. U foydalanuvchi kiritgan maʼlumotlar, tashqi manbadan olingan maʼlumotlar, funksiya natijasi va boshqalar asosida modullarni import qilishga moslashuvchanlik qo’shadi.


Modul patterni yordamida biz kodimizning ommaga oshkor etilmasligi kerak bo'lgan qismlarini inkapsulyatsiya (encapsulation) qilishimiz mumkin. Bu nomlarning tasodifiy to'qnashuvi va global qamrovning ifloslanishining oldini oladi, bu esa bir nechta qaramliklar (dependencies) va nomlar makoni (namespace) bilan ishlashni xavfsizroq qiladi. Barcha JavaScript kodni bajarilish vaqtlari (runtime)larida ES2015 modullaridan foydalanish uchun Babel kabi transpiler kerak bo’ladi. 