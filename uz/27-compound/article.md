# Compound Pattern

Bizning ilovamizda ko'pincha bir-biriga tegishli bo'lgan komponentlar mavjud. Ular umumiy holat orqali bir-biriga bog'liq va mantiqni birgalikda baham ko'rishadi. Siz buni ko'pincha select, tanlash ro’yxati komponentlari yoki menyu elementi kabi komponentlar bilan ko'rasiz. Birlashgan komponentlar modeli vazifani bajarish uchun barchasi birgalikda ishlaydigan komponentlarni yaratishga imkon beradi.

## Context API

Keling, bir misolni ko'rib chiqaylik: bizda olmaxon tasvirlari ro'yxati bor\! Biz shunchaki olmaxon tasvirlarini ko'rsatishdan tashqari, foydalanuvchiga tasvirni tahrirlash yoki o'chirish imkonini beradigan tugmachani qo'shmoqchimiz. Foydalanuvchi komponentni almashtirganda ro'yxatni ko'rsatadigan  FlyOut  komponentini amalga oshirishimiz mumkin.

<div align="center">
  <img src="../../images/compound/01.png" alt="Rasm" />
</div>

<div align="center">
  <img src="../../images/compound/02.png" alt="Rasm" />
</div>

<div align="center">
  <img src="../../images/compound/03.png" alt="Rasm" />
</div>


 FlyOut komponenti tarkibida uchta narsa mavjud:

* FlyOut wrapperni almashtirish tugmasi va listni o’z ichiga oladi,  
* Toggle button List  ni almashtiradi,  
* List menyu elementlari ro’yxatini o’z ichiga oladi

Ushbu misol uchun Reactdagi [Context API](https://reactjs.org/docs/context.html) bilan Birlashgan komponentlar modelidan foydalanish juda mos keladi\!

Birinchidan, FlyOut komponentini yarataylik. Ushbu komponent holatni saqlaydi va FlyOutProvider ni qabul qilgan barcha childrenga almashtirish, ya’ni toggle qiymati bilan qaytaradi.

```js
const FlyOutContext = createContext();
function FlyOut(props) {
  const [open, toggle] = useState(false);


  return (
    <FlyOutContext.Provider value={{ open, toggle }}>
      {props.children}
    </FlyOutContext.Provider>
  );
}
```

Endi bizda open va toggle qiymatini o'z bolalariga o'tkazishi mumkin bo'lgan FlyOut komponenti mavjud\!

Keling, Toggle komponentini yarataylik. Ushbu komponent oddiygina menyuni almashtirish uchun foydalanuvchi bosishi mumkin bo'lgan komponentni ko'rsatadi.

```js
function Toggle() {
  const { open, toggle } = useContext(FlyOutContext);
  return (
    <div onClick={() => toggle(!open)}>
      <Icon />
    </div>
  );
}
```

Toggle ga FlyOutContext provayderga kirish huquqini berish uchun biz uni FlyOut ning asosiy komponenti sifatida ko'rsatishimiz kerak\! Biz buni oddiygina bola komponent sifatida ko'rsatishimiz mumkin. Shu bilan birga, biz Toggle komponentini FlyOut komponentidagi xususiyatga ham aylantira olamiz\!
```js
const FlyOutContext = createContext();
function FlyOut(props) {
  const [open, toggle] = useState(false);
  return (
    <FlyOutContext.Provider value={{ open, toggle }}>
      {props.children}
    </FlyOutContext.Provider>
  );
}
function Toggle() {
  const { open, toggle } = useContext(FlyOutContext);
  return (
    <div onClick={() => toggle(!open)}>
      <Icon />
    </div>
  );
}
FlyOut.Toggle = Toggle;
```

Bu biz biror faylda FlyOut komponentidan foydalanmoqchi bo'lsak, faqat FlyOutni import qilishimiz kerakligini anglatadi\!

```js
import React from "react";
import { FlyOut } from "./FlyOut";
export default function FlyoutMenu() {
  return (
    <FlyOut>
      <FlyOut.Toggle />
    </FlyOut>
  );
}
```

Bittagina toggle ning o’zi yetarli emas. Shuningdek, bizda ro'yxat elementlari bo'lgan List bo'lishi kerak, ular open qiymatiga qarab ochiladi va yopiladi.

```js
function List({ children }) {
  const { open } = React.useContext(FlyOutContext);
  return open && <ul>{children}</ul>;
}
function Item({ children }) {
  return <li>{children}</li>;
}
```

List komponenti open qiymatining true yoki false ekanligiga qarab o'z bolalarini ko'rsatadi. List va itemni xuddi Toggle komponentida qilganimiz kabi FlyOut komponentining xususiyatiga aylantiramiz.

```js
const FlyOutContext = createContext();
function FlyOut(props) {
  const [open, toggle] = useState(false);
  return (
    <FlyOutContext.Provider value={{ open, toggle }}>
      {props.children}
    </FlyOutContext.Provider>
  );
}
function Toggle() {
  const { open, toggle } = useContext(FlyOutContext);
  return (
    <div onClick={() => toggle(!open)}>
      <Icon />
    </div>
  );
}
function List({ children }) {
  const { open } = useContext(FlyOutContext);
  return open && <ul>{children}</ul>;
}
function Item({ children }) {
  return <li>{children}</li>;
}
FlyOut.Toggle = Toggle;
FlyOut.List = List;
FlyOut.Item = Item;
```

Endi biz ularni FlyOut komponentida xususiyat sifatida ishlatishimiz mumkin\! Bunday holda, foydalanuvchiga ikkita variant: edit va delete qilishni ko'rsatmoqchimiz. Keling, ikkita FlyOut.Item komponentini ko'rsatadigan FlyOut.List yarataylik, biri Edit opsiyasi va biri Delete opsiyasi uchun bo’ladi.

```js
import React from "react";
import { FlyOut } from "./FlyOut";
export default function FlyoutMenu() {
  return (
    <FlyOut>
      <FlyOut.Toggle />
      <FlyOut.List>
        <FlyOut.Item>Edit</FlyOut.Item>
        <FlyOut.Item>Delete</FlyOut.Item>
      </FlyOut.List>
    </FlyOut>
  );
}
```

Ajoyib\! Biz faqat FlyOutMenu ning o'zida hech qanday holat qo'shmasdan butun FlyOut komponentini yaratdik\!

```js
 import React from "react";
 import "./styles.css";
 import { FlyOut } from "./FlyOut";
 export default function FlyoutMenu() {
  return (
    <FlyOut>
      <FlyOut.Toggle />
      <FlyOut.List>
        <FlyOut.Item>Edit</FlyOut.Item>
        <FlyOut.Item>Delete</FlyOut.Item>
      </FlyOut.List>
    </FlyOut>
  );
 }
```

Komponentlar kutubxonasini qurayotganingizda compound pattern juda yaxshi.  [Semantic UI](https://react.semantic-ui.com/modules/dropdown/#types-dropdown) kabi UI kutubxonalaridan foydalanganda ushbu patternni tez-tez ko'rasiz.

## [React.Children.map](https://reactjs.org/docs/react-api.html#reactchildrenmap)

Komponentning bolalarini mapping qilish orqali biz Compount Component pattern-ni ham implement qilishimiz mumkin. Biz ushbu elementlarga qo'shimcha rekvizitlar bilan [klonlash](https://reactjs.org/docs/react-api.html#cloneelement) orqali open va toggle xususiyatlarini qo'shishimiz mumkin.

```js
export function FlyOut(props) {
  const [open, toggle] = React.useState(false);
  return (
    <div>
      {React.Children.map(props.children, (child) =>
        React.cloneElement(child, { open, toggle })
      )}
    </div>
  );
}
```

Barcha children komponentlari klonlanadi va open va toggle qiymatidan o'tadi. Oldingi misoldagi kabi Context APIdan foydalanish o'rniga, biz endi bu ikki qiymatga proplar, ya’ni rekvizitlar orqali kirishimiz mumkin.

```js
 import React from "react";
 import Icon from "./Icon";
 const FlyOutContext = React.createContext();
 export function FlyOut(props) {
  const [open, toggle] = React.useState(false);
  return (
    <div>
      {React.Children.map(props.children, child =>
        React.cloneElement(child, { open, toggle })
      )}
    </div>
  );
 }
 function Toggle() {
  const { open, toggle } = React.useContext(FlyOutContext);
  return (
    <div className="flyout-btn" onClick={() => toggle(!open)}>
     <Icon />
    </div>
  );
 }
 function List({ children }) {
  const { open } = React.useContext(FlyOutContext);
  return open && <ul className="flyout-list">{children}</ul>;
```

## Ustun taraflari

Murakkab komponentlar o'zlarining ichki holatini boshqaradi, ular bir nechta yordamchi komponentlar orasida ulashiladi. Murakkab komponentni amalga oshirishda biz holatni o'zimiz boshqarishimiz haqida tashvishlanishimizga hojat yo’q.

Murakkab komponentni import qilishda biz ushbu komponentda mavjud bo'lgan asosiy komponentlarni aniq import qilishimiz shart emas.

```js
import { FlyOut } from "./FlyOut";
export default function FlyoutMenu() {
  return (
    <FlyOut>
      <FlyOut.Toggle />
      <FlyOut.List>
        <FlyOut.Item>Edit</FlyOut.Item>
        <FlyOut.Item>Delete</FlyOut.Item>
      </FlyOut.List>
    </FlyOut>
  );
}
```

## Kamchiliklari

Qiymatlarni ta'minlash uchun React.Children.map dan foydalanilganda, komponentlarni joylashtirish cheklangan. Faqat parent komponentining bevosita bolalari open va toggle proplarga kirish huquqiga ega bo'ladi, ya'ni biz ushbu komponentlardan hech birini boshqa komponentga o'ray olmaymiz.

```js
export default function FlyoutMenu() {
  return (
    <FlyOut>
      {/* This breaks */}
      <div>
        <FlyOut.Toggle />
        <FlyOut.List>
          <FlyOut.Item>Edit</FlyOut.Item>
          <FlyOut.Item>Delete</FlyOut.Item>
        </FlyOut.List>
      </div>
    </FlyOut>
  );
}
```

React.cloneElement bilan elementni klonlash sayoz birlashtirishni amalga oshiradi. Allaqachon mavjud proplar biz o'tadigan yangi proplar bilan birlashtiriladi. Agar mavjud prop biz React.cloneElement usuliga o‘tayotgan proplar bilan bir xil nomga ega bo‘lsa, bu nomlar konflikti bilan yakunlanishi mumkin. Proplar sayoz ravishda birlashtirilganligi sababli, ushbu propning qiymati biz o'tkazgan oxirgi qiymat bilan qayta yoziladi.

## Havolalar

* [Render Props \- React](https://reactjs.org/docs/render-props.htm)  
* [React Hooks: Compound Components \- Kent C. Dodds](https://kentcdodds.com/blog/compound-components-with-react-hooks)