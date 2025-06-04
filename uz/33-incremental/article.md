# Bosqichma-bosqich statik generatsiya

Statik generatsiya (SSG) SSR va CSR bilan bog'liq muammolarning aksariyatini hal qiladi, lekin asosan statik tarkibini ko'rsatish uchun mos keladi. Render qilinishi kerak bo'lgan kontent dinamik yoki tez-tez o'zgarib turadigan bo'lsa, bu cheklovlarni keltirib chiqaradi.

Bir nechta postlar bilan o'sib borayotgan blogni o'ylab ko'ring. Siz postlardan biridagi xatoni tuzatmoqchi bo'lganingiz uchun saytni qayta qurish va joylashtirishni xohlamasligingiz mumkin. Xuddi shunday, bitta yangi blog posti ham barcha mavjud sahifalar uchun qayta tiklashni talab qilmasligi kerak. Shunday qilib, SSG o'z-o'zidan katta veb-saytlar yoki ilovalarni ko'rsatish uchun yetarli emas.

Incremental Static Generation (iSSG) modeli dinamik maʼlumotlar muammosini hal qilishga yordam berish va statik saytlarning tez-tez o’zgarib turadigan katta hajmdagi maʼlumotlarini masshtablashga yordam berish uchun SSG-ni yangilash sifatida taqdim etildi. iSSG mavjud sahifalarni yangilash va sahifalar uchun yangi so’rovlar kelib tushayotgan paytda ham sahifalar to’plamini fonda oldindan renderlash orqali yangilarini qo’shish imkonini beradi.

## Kod namunasi

iSSG mavjud statik sayt qurilganidan keyin yangilanishlarni bosqichma-bosqich joriy etish uchun ikki jabhada ishlaydi.

1. Yangi sahifalar qo'shish imkonini beradi.   
2. Bosqichma-bosqich Statik "Regeneratsiya" deb nomlanuvchi mavjud sahifalarni yangilash imkonini beradi. 

### Yangi sahifalar qo’shish

Kechiktirib yuklash kontseptsiyasi veb-saytga qurilgandan keyin yangi sahifalarni kiritish uchun ishlatiladi. Bu shuni anglatadiki, yangi sahifa birinchi so’rovda darhol yaratiladi. Yaratish amalga oshirilayotganda, foydalanuvchiga oldingi qismda zahira sahifa yok yuklanmoqda belgisi ko'rsatilishi mumkin. Buni har bir mahsulotdagi alohida tafsilotlar sahifasi uchun avval muhokama qilingan SSG ssenariysi bilan solishtiring. 404 xato sahifa bu yerda mavjud bo'lmagan sahifalar uchun zahira sifatida ko'rsatilgan.

Keling, mavjud bo'lmagan sahifani iSSG bilan kechiktirib yuklash uchun zarur bo'lgan Next.js kodini ko'rib chiqamiz.
```js
1 // In getStaticPaths(), you need to return the list of
2 // ids of product pages (/products/[id]) that you'd
3 // like to pre-render at build time. To do so,
4 // you can fetch all products from a database.
5 export async function getStaticPaths() {
6  const products = await getProductsFromDatabase();
7
8  const paths = products.map((product) => ({
9     params: { id: product.id }
10  }));
11
12
13  // fallback: true means that the missing pages
14  // will not 404, and instead can render a fallback.
15  return { paths, fallback: true };
16 }
17
18 // params will contain the id for each generated page.
19 export async function getStaticProps({ params }) {
20  return {
21    props: {
22      product: await getProductFromDatabase(params.id)
23    }
24  }
25 }
26
27
28 export default function Product({ product }) {
29  const router = useRouter();
30
31  if (router.isFallback) {
32    return <div>Loading...</div>;
33  }
34
35  // Render product
36 }
```

Bu yerda biz fallback: true dan foydalandik. Endi ma'lum bir mahsulotga mos keladigan sahifa mavjud bo'lmasa, biz sahifaning zahira versiyasini ko'rsatamiz, masalan, yuqoridagi Product funksiyasida ko'rsatilgan yuklanmoqda belgisi. Shu bilan birga, Next.js sahifani orqa fonda yaratadi. U yaratilgandan so'ng, keshlanadi va zahira sahifa o'rniga ko'rsatiladi. Sahifaning keshlangan versiyasi endi so’rov bo'yicha har qanday keyingi foydalanuvchilarga darhol ko'rsatiladi. Yangi va mavjud sahifalar uchun biz Next.js uni qayta tekshirishi va yangilashi uchun amal qilish muddatini belgilashimiz mumkin. Bunga quyidagi bo'limda ko'rsatilganidek, revalidate xususiyatidan foydalanish orqali erishamiz.

### Mavjud sahifalarni yangilang

Mavjud sahifani qayta render qilish uchun sahifa uchun mos timeout belgilangan. Bu belgilangan timeout bilan sahifa qayta tekshirilishini ta'minlaydi. Timeout 1 soniyagacha o'rnatilishi mumkin. Foydalanuvchi sahifani revalidation tugaguniga qadar sahifaning oldingi versiyasini ko'rishda davom etadi. Shunday qilib, iSSG [stale-while-revalidate](https://web.dev/stale-while-revalidate/) strategiyadan foydalanadi, bunda foydalanuvchi keshlangan yoki eskirgan versiyani oladi. Revalidation to'liq qayta qurishni talab qilmasdan butunlay orqa fonda amalga oshiriladi.

Keling, bazadagi ma'lumotlar asosida mahsulotlar uchun statik ro'yxat sahifasini yaratish misoliga qaytaylik. Uni mahsulotlarning mos dinamik listiga xizmat qilishini ta'minlash uchun biz sahifani qayta tiklayotganda kutish vaqtini belgilash kodini kiritamiz. Timeoutni qo'shgandan keyin kod quyidagicha ko'rinadi.

```js
1 // This function runs at build time on the build server
2 export async function getStaticProps() {
3  return {
4    props: {
5      products: await getProductsFromDatabase(),
6      revalidate: 60, // This will force the page to revalidate after 60 seconds
7    }
8  }
9 }
10
11 // The page component receives products prop from getStaticProps at build time
12 export default function Products({ products }) {
13  return (
14    <>
15      <h1>Products</h1>
16      <ul>
17        {products.map((product) => (
18          <li key={product.id}>{product.name}</li>
19        ))}
20      </ul>
21    </>
22  )
23 }
```

Sahifani 60 soniyadan keyin qayta tekshirish uchun kod getStaticProps() funksiyasiga kiritilgan. Request kelib tushganda, avval mavjud statik sahifa taqdim etiladi. Har bir daqiqada statik sahifa orqa fonda yangi ma'lumotlar bilan yangilanadi. Yaratilgandan so'ng, statik faylning yangi versiyasi mavjud bo'ladi va keyingi daqiqalarda har qanday yangi so’rovlar uchun xizmat ko'rsatiladi. Bu funksiya Next.js 9.5 va undan yuqori versiyalarida mavjud.

## Ustun taraflari

iSSG SSG ning barcha afzalliklarini, keyin esa yana bir qancha imkoniyatlarni beradi. Quyidagi ro'yxat ularni batafsil yoritadi.

1. Dinamik ma’lumot: Birinchi afzallik, iSSG nima uchun nazarda tutilganligi aniq. Saytni qayta tiklashga hojat qoldirmasdan dinamik ma'lumotlarni qo'llab-quvvatlash qobiliyatidir.  
2. Tezlik: iSSG hech bo'lmaganda SSG kabi tezdir, chunki ma'lumotlarni olish va ko'rsatish hali ham orqa fonda amalga oshiriladi. Foydalanuvchi qurilmasi yoki serverda ozgina ishlov berish talab qilinadi.  
3. Mavjudlik: Har qanday sahifaning eng so'nggi versiyasi har doim foydalanuvchilar uchun onlayn bo'ladi. Regeneratsiya orqa fonda bajarilmasa ham, eski versiya o'zgarmagan holda qoladi.  
4. Davomiylik: Regeneratsiya serverda bir vaqtning o'zida bir sahifada sodir bo'lganligi sababli, ma’lumotlar bazasi va backend yuklanishi past va ishlash barqaror bo'ladi. Natijada, kechikishda keskin o'zgarishlar bo'lmaydi.  
5. Tarqatish osonligi: Xuddi SSG saytlari kabi, iSSG saytlari ham oldindan ko'rsatilgan veb-sahifalarga xizmat ko'rsatish uchun foydalaniladigan CDN tarmog'i orqali tarqatilishi mumkin.