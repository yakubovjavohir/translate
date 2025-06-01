# Konteyner/taqdimot modeli (Container/ Presentational pattern)

Reactda concernlarni ajratishning eng zo’r yo’li bu Konteyner/Taqdimot modeli usulidan foydalanishdir. Ushbu model yordamida biz viewni ilova mantig’idan ajratishimiz mumkin.

Aytaylik, biz 6 ta it tasvirini oladigan va bu tasvirlarni ekranda ko'rsatadigan dastur yaratmoqchimiz. 

```js
import React from "react";
 export default function DogImages({ dogs }) {
  return dogs.map((dog, i) => <img src={dog} key={i} alt="Dog" />);
 }
```

Biz ushbu jarayonni ikki qismga bo'lish orqali concernlarni ajratmoqchimiz:

1. Taqdimot komponentlari: ma'lumotlarning foydalanuvchiga qanday ko'rsatilishi haqida qayg'uradigan komponentlar. Ushbu misolda, bu it tasvirlari ro'yxati render qilinadi.  
2. Konteyner komponentlari: foydalanuvchiga nima va qanday ma'lumotlar ko'rsatilishi haqida qayg'uradigan komponentlar. Bu misolda it tasvirlari olinadi.

[https://res.cloudinary.com/ddxwdqwkr/video/upload/f\_auto/v1609056518/patterns.dev/jspat-40\_af2vga.mp4](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056518/patterns.dev/jspat-40_af2vga.mp4)

It tasvirlarini olish ilova mantig’i bilan, tasvirlarni ko'rsatish esa faqat view bilan bog'liq.

## Taqdimot komponenti

Taqdimot komponenti o'z ma'lumotlarini proplar orqali oladi. Uning asosiy vazifasi \- olingan ma'lumotlarni biz xohlagan tarzda, shu jumladan uslublarni ushbu ma'lumotlarni o'zgartirmasdan ko'rsatishdir.

Keling, it tasvirlarini ko'rsatadigan misolni ko'rib chiqaylik. It tasvirlarini ko'rsatishda biz API dan olingan har bir it tasvirini har bir elementga qo’llashni va bu tasvirlarni ko'rsatishni xohlaymiz. Buning uchun biz rekvizitlar orqali ma'lumotlarni qabul qiladigan va olingan ma'lumotlarni ko'rsatadigan funksional komponentni yaratishimiz mumkin.

```js
import React from "react";
 export default function DogImages({ dogs }) {
  return dogs.map((dog, i) => <img src={dog} key={i} alt="Dog" />);
 }
```

DogImages komponenti taqdimot komponentidir. Taqdimot komponentlari odatda stateless hisoblanadi: ular UI maqsadlari uchun state kerak bo'lmasa, o'zlarining React state’ini o'z ichiga olmaydi. Ular olgan ma'lumotlar taqdimot komponentlarining o'zlari tomonidan o'zgartirilmaydi.

Taqdimot komponentlari o'z ma'lumotlarini konteyner komponentlaridan oladi.

## Konteyner komponentlari

Konteyner komponentlarining asosiy vazifasi ma'lumotlarni ular o'z ichiga olgan taqdimot komponentlarga uzatishdir. Konteyner komponentlarining o'zlari odatda ma'lumotlariga e'tibor beradigan taqdimot komponentlaridan tashqari boshqa komponentlarni ko'rsatmaydi. Ular o'zlari hech narsa bermagani uchun ular odatda formatlashni ham o'z ichiga olmaydi.

Bizning misolimizda biz it tasvirlarini DogsImages taqdimot komponentiga o'tkazmoqchimiz. Buni amalga oshirishdan oldin biz tashqi API dan tasvirlarni olishimiz kerak. Biz ushbu ma'lumotlarni oladigan konteyner komponentini yaratishimiz kerak va bu ma'lumotlarni ekranda ko'rsatish uchun DogImages taqdimot komponentiga o'tkazamiz.

```js
 import React from "react";
 import DogImages from "./DogImages";
 export default class DogImagesContainer extends React.Component {
  constructor() {
    super();
    this.state = {
      dogs: []
    };
  }
  componentDidMount() {
    fetch("https://dog.ceo/api/breed/labrador/images/random/6")
      .then(res => res.json())
      .then(({ message }) => this.setState({ dogs: message }));
  }
  render() {
    return <DogImages dogs={this.state.dogs} />;
  }
 }
```

Ushbu ikki komponentni birgalikda birlashtirish view bilan ishlash mantig’ini ajratish imkonini beradi. 

[https://res.cloudinary.com/ddxwdqwkr/video/upload/f\_auto/v1609056519/patterns.dev/jspat-45\_budnfb.mp4](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056519/patterns.dev/jspat-45_budnfb.mp4)

## Hooks

Ko'p hollarda Konteyner/taqdimot modeli React Hooks bilan almashtirilishi mumkin. Hooksning joriy etilishi ishlab chiquvchilarga ushbu holatni ta'minlash uchun konteyner komponentiga muhtoj bo'lmasdan holatni qo'shishni osonlashtirdi.

DogImagesContainer komponentida ma'lumotlarni olish mantig’iga ega bo'lish o'rniga, biz tasvirlarni oladigan va itlar qatorini qaytaradigan maxsus hook yaratishimiz mumkin.

```js
export default function useDogImages() {
  const [dogs, setDogs] = useState([]);
  useEffect(() => {
    fetch("https://dog.ceo/api/breed/labrador/images/random/6")
      .then((res) => res.json())
      .then(({ message }) => setDogs(message));
  }, []);
  return dogs;
}
```

Ushbu hookdan foydalanib, biz endi ma'lumotlarni olish uchun wrapping DogImagesContainer konteyner komponentiga muhtoj emasmiz va buni DogImages taqdimot komponentiga jo'natamiz. Buning o'rniga, biz ushbu hookka to'g'ridan-to'g'ri DogImages taqdimot komponentimizda foydalanishimiz mumkin\!

```js
 import React from "react";
 import useDogImages from "./useDogImages";
 export default function DogImages() {
  const dogs = useDogImages();
  return dogs.map((dog, i) => <img src={dog} key={i} alt="Dog" />);
 }
```

UseDogImages hookdan foydalanib, ilova mantig’ini viewdan ajratdik. Biz shunchaki useDogImages hookdan qaytarilgan ma'lumotlarni DogImages komponentida o'zgartirmasdan foydalanamiz.

[https://res.cloudinary.com/ddxwdqwkr/video/upload/f\_auto/v1609056518/patterns.dev/jspat-46\_evhhpd.mp4](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056518/patterns.dev/jspat-46_evhhpd.mp4)

Hooklar Konteyner/taqdimot modeli kabi mantiqni ajratish va komponentda ko‘rishni osonlashtiradi. Bular taqdimot komponentini konteyner komponentiga o'rash uchun zarur bo'lgan qo'shimcha qatlamni tejaydi.

## Ustun taraflari

Konteyner/taqdimot modelidan foydalanishning ko‘p afzalliklari bor.

ushbu modellar concernlarni ajratishni rag'batlantiradi. Taqdimot komponentlari UI uchun mas'ul bo'lgan sof funksiyalar bo'lishi mumkin, konteyner komponentlari esa dastur holati va ma'lumotlari uchun javobgardir. Bu concernlarni ajratishni amalga oshirishni osonlashtiradi.

Taqdimot komponentlari osongina qayta ishlatilishi mumkin, chunki ular ma'lumotlarni o'zgartirmasdan ko'rsatishadi. Biz taqdimot komponentlarini ilovamiz davomida turli maqsadlarda qayta ishlatamiz.

Taqdimot komponentlari ilova mantig’ini o'zgartirmaganligi sababli, taqdimot komponentlarining ko'rinishi kodlar bazasini bilmagan kishi tomonidan osongina o'zgartirilishi mumkin, masalan, dizaynerlar. Agar taqdimot komponenti ilovaning ko'p qismlarida qayta ishlatilgan bo'lsa, o'zgartirish butun ilovada izchil bo'lishi mumkin.

Taqdimot komponentlarini sinash oson, chunki ular odatda sof funksiyalardir. Biz ma'lumotlar omborini soxta nusxasini yaratmasdan, qaysi ma'lumotlarga asoslanib, komponentlar nima qilishini bilamiz.

## Kamchiliklari

Konteyner/taqdimot namunasi ilova mantig’ini renderlash mantiqdan ajratishni osonlashtiradi. Biroq, Hooks Konteyner/taqdimot namunasidan foydalanmasdan va stateless funksional komponentni class komponentiga qayta yozmasdan bir xil natijaga erishish imkonini beradi. Eslatma: bugungi kunda biz statedan foydalanish uchun class komponentlarini yaratishimiz shart emas.

Garchi biz hali ham Konteyner/taqdimot namunasidan foydalana olsakda, hatto React Hooks bilan ham, bu pattern kichikroq o'lchamdagi ilovalarda osonlikcha ortiqcha bo'lishi mumkin.

## Havolalar

* [Presentational and Container Components \- Dan Abramov](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)