# Kirish

**Dizayn patternlar** dasturiy ta'minotni ishlab chiqishning fundamental qismi, chunki ular dasturiy ta'minotni loyihalashda tez-tez uchrab turadigan muammolarga sodda va namunaviy yechimlar taqdim etadi. Dizayn patternlar muayyan dasturiy ta'minot kodlarini emas, aksincha takrorlanuvchi masalalarni optimallashtirilgan tarzda hal qilish uchun qo’llanilishi mumkin bo'lgan konsepsiyalardir.

So'nggi bir necha yil ichida **veb-dasturlash ekotizimi** jadal sur’atda o'zgardi. Ba'zi mashhur dizayn patternlari avvalgi ahamiyatini yo’qotgan bo’lsa, boshqalari zamonaviy muammolarni eng so’nngi texnologiyalar yordamida hal qilish darajasiga chiqdi.


Facebook tomonidan yaratilgan React Javascript kutubxonasi so'nggi 5 yil ichida yuqori e’tirofga sazovor bo’ldi va hozirda Angular, Vue, Ember va Svelte kabi raqobatdosh JavaScript kutubxonalari orasida NPM’da eng ko'p yuklab olinadigan freymvork hisoblanadi. Reactning jadal sur’atda ommalashishi tufayli, dizayn patternlar zamonaviy veb-dasturlash ekotizimida yanada samarali bo’lishi uchun o'zgartirildi, optimallashtirildi va yangilari yaratildi. Reactning so’nggi versiyasi (kitobning yozilish vaqtidagi versiyasi) ilovangiz arxitekturasida juda muhim rol o’ynaydigan va ko'plab an'anaviy dizayn patternlarning o’rnini bosa oladigan “Hooks” nomli yangi xususiyatni taqdim etdi.


Zamonaviy veb-dasturlash juda ko'plab turli xil patternlarni o'z ichiga oladi. Ushbu loyiha ES2015+ yordamida keng tarqalgan dizayn patternlarning implementatsiyasi, afzalliklari va kamchiliklari, Reactga xos dizayn patternlari va ularni React Hooks yordamida modifikatsiyalash hamda implementatsiya qilish, shuningdek zamonaviy veb-ilovangizni takomillashtirishga yordam beradigan boshqa koʻplab patternlar va optimizatsiyalarni qamrab oladi!



# Dizayn patternlari

# Command pattern (Buyruq patterni)

Command patterni yordamida biz ma'lum bir vazifani bajaradigan obyektlarni metodni chaqiradigan obyektdan ajratishimiz mumkin.
Aytaylik, bizda oziq-ovqat yetkazib beruvchi onlayn platforma bor. Foydalanuvchilar buyurtmalarni joylashtirishi, kuzatishi va bekor qilishi mumkin.


![Rasm](https://lh3.googleusercontent.com/keep-bbsk/AFgXFlIkKh1LWK6_Rn06y4GeoBZXFKoOIbYN85iTpoqovWA--SUKkXDjHZBM0ZYOelRw9ILMa_Cy4tuPDMH1NtwTkmcrLFR1a9iR973leA0Eb3wbgPxtkEt00g=s512)

OrderManager klassida bizda placeOrder, trackOrder va cancelOrder metodlariga kirish imkoniyati mavjud. Ushbu metodlardan to’g’ridan-to’g’ri foydalanish JavaScript uchun to’g’ri yondashuv hisoblanadi.

![Rasm](https://lh3.googleusercontent.com/keep-bbsk/AFgXFlIkKh1LWK6_Rn06y4GeoBZXFKoOIbYN85iTpoqovWA--SUKkXDjHZBM0ZYOelRw9ILMa_Cy4tuPDMH1NtwTkmcrLFR1a9iR973leA0Eb3wbgPxtkEt00g=s512

Biroq, metodlarni to’g’ridan-to’g’ri manager obyekt nusxasida (instance) chaqirishning salbiy tomonlari mavjud.  Keyinchalik ba'zi metodlarning nomini o'zgartirishga qaror qilishimiz yoki metodlarning funksionalligi o'zgarishi mumkin.
Aytaylik, biz  placeOrder deb nomlash o’rniga,  addOrder deb qayta nomlasak! Bu esa placeOrder metodini kod bazamizning hech bir joyida chaqirmasligimizga ishonch hosil qilishimizni talab qiladi, bu katta ilovalarda muammo paydo bo’lishiga olib kelishi mumkin.  Buning o'rniga metodlarni manager obyektidan ajratib olamiz va har bir buyruq uchun alohida buyruq funksiyalarini yaratamiz!
Keling, OrderManager klassini qayta tuzib chiqamiz:  placeOrder, cancelOrder va trackOrder metodlari o’rniga u bitta execute metodiga ega bo’ladi. Bu metod unga berilgan har qanday buyruqni bajaradi.
Har bir buyruq menejerning orders ma’lumotlariga kirish huquqiga ega bo'lishi kerak, biz uni birinchi argument sifatida uzatamiz. )

![Rasm](https://keep.google.com/u/0/media/v2/1uKW4nlyRG6Xw2xlVNUKyzyWXdBZkhJTWJKNm--w8XXc5EAenmcfOJCfsejXCTIA/1BYhCY0_TpkFRcYqR2DFNkl5E1eoDRvQ4gGk6CyH9NbwNVe-njDi0GeHEtb1Ulw?sz=512&accept=image%2Fgif%2Cimage%2Fjpeg%2Cimage%2Fjpg%2Cimage%2Fpng%2Cimage%2Fwebp)

# Buyurtma menejeri uchun Commandlar
Buyurtma menejeri uchun uchta Command yaratishimiz kerak: 

- PlaceOrderCommand  
- CancelOrderCommand  
- TrackOrderCommand


![Rasm](https://keep.google.com/u/0/media/v2/1w57mnQLGDeY6hsQd3aPaJDT8_NFzF23w0hlMbe4I8PHpPy7_lSTR4H-Fmc8TBQ/1pb0AnoUuyYstWoW3HuWmEQq6_56SeQf60JUQPvZzT-oqvGjcHVlgkIvxtE4MaA?sz=512&accept=image%2Fgif%2Cimage%2Fjpeg%2Cimage%2Fjpg%2Cimage%2Fpng%2Cimage%2Fwebp)

Ajoyib! To'g'ridan-to'g'ri OrderManager nusxasiga bog'langan metodlar o'rniga, ular endi alohida, mustaqil funksiyalar bo'lib, ularni OrderManager da mavjud  execute metodi orqali chaqirishimiz mumkin.

![Rasm](https://keep.google.com/u/0/media/v2/1MA6QeXRBhx19u9N8Wk3bfEZqdAPViPOL178HXyNmF817t5ElJDFtnhQHMb5352Q/14g3mjDyd0uXgCMFV86hVCZWIvU-H5aAYxcLckfjPKX81AoHjvsaUbqHxkkiDTA?sz=512&accept=image%2Fgif%2Cimage%2Fjpeg%2Cimage%2Fjpg%2Cimage%2Fpng%2Cimage%2Fwebp)

Afzalliklari

Command patterni bizga operatsiyani bajaruvchi obyektdan metodlarni ajratish imkonini beradi. Agar siz ma'lum vaqt oralig’ida ishlashi kerak bo’lgan buyruqlar yoki ma'lum vaqtda navbatga qo'yilib, keyinroq bajarilishi kerak bo'lgan buyruqlar bilan ishlayotgan bo'lsangiz, u sizga ko'proq nazorat imkoniyatini beradi.

Kamchiliklari

Command pattern’ning qo’llanilish holatlari juda cheklangan va ko'pincha ilovaga ortiqcha ko’p takrorlanuvchi kod (boilerplate code) qo'shadi.