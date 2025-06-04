# Render Props Pattern

[Higher Order Components](https://www.patterns.dev/posts/hoc-pattern) bo'limida, agar bir nechta komponentlar bir xil ma'lumotlarga kirishga muhtoj bo'lsa yoki bir xil mantiqni o'z ichiga olgan bo'lsa, komponent mantiqdan qayta foydalanish juda qulay bo'lishi mumkinligini ko'rdik.

Komponentlarni qayta foydalanishga yaroqli qilishning yana bir usuli render propdan foydalanishdir. Render prop komponentdagi prop bo‘lib, uning qiymati JSX elementini qaytaruvchi funksiyadir. Komponentning o'zi render propidan tashqari hech narsa ko'rsatmaydi. Buning o'rniga, komponent o'zining renderlash mantig’ini amalga oshirish o'rniga, oddiygina render propini chaqiradi.

Tasavvur qiling, bizda Title komponenti bor. Bunday holda, Title komponenti biz o'tkazadigan qiymatni ko'rsatishdan tashqari hech narsa qilmasligi kerak. Buning uchun biz render propidan foydalanishimiz mumkin\! Keling, Title komponenti ko'rsatishni xohlagan qiymatni render propiga o'tkazamiz.

```js
<Title render={() => <h1>I am a render prop!</h1>} />
```

Title komponentida biz chaqirilgan render propni qaytarish orqali ushbu ma'lumotlarni ko'rsatishimiz mumkin\!

```js
const Title = (props) => props.render();
```

Komponent elementiga biz render deb nomlangan propni o'tkazishimiz kerak, bu React elementini qaytaruvchi funksiyadir.

```js
import React from "react";
 import { render } from "react-dom";
  import "./styles.css";
 const Title = (props) => props.render();
 render(
  <div className="App">
    <Title
      render={() => (
        <h1>
          <span role="img" aria-label="emoji">
            ✨
          </span>
          I am a render prop!{" "}
          <span role="img" aria-label="emoji">
            ✨
          </span>
        </h1>
      )}
    />
  </div>,
  document.getElementById("root")
 );
```

Zo'r, muammosiz ishlaydi\! Render proplarining ajoyib tomoni shundaki, propni qabul qiluvchi komponentdan qayta foydalanish mumkin. Biz uni bir necha marta ishlatishimiz mumkin, har safar render propga turli qiymatlarni o'tkazamiz.

```js
 import React from "react";
 import { render } from "react-dom";
 import "./styles.css";
 const Title = (props) => props.render();
 render(
  <div className="App">
    <Title render={() => <h1>✨ First render prop! ✨</h1>} />
    <Title render={() => <h2>🔥 Second render prop! 🔥</h2>} />
    <Title render={() => <h3>🚀 Third render prop! 🚀</h3>} />
  </div>,
  document.getElementById("root")
 );
```

Garchi ular render proplar deb atalsa-da, render propni render deb atash shart emas. JSX-ni ko'rsatadigan har qanday prop render prop hisoblanadi\! Keling, oldingi misolda ishlatilgan render proplarining nomini o'zgartiramiz va ularning o'rniga aniq nomlar beramiz\!

```js
import React from "react";
 import { render } from "react-dom";
 import "./styles.css";
 const Title = (props) => (
  <>
    {props.renderFirstComponent()}
    {props.renderSecondComponent()}
    {props.renderThirdComponent()}
  </>
 );
 render(
  <div className="App">
    <Title
      renderFirstComponent={() => <h1>✨ First render prop! ✨</h1>}
      renderSecondComponent={() => <h2>🔥 Second render prop! 🔥</h2>}
      renderThirdComponent={() => <h3>🚀 Third render prop! 🚀</h3>}
   />
  </div>,
  document.getElementById("root")
 );
```

Ajoyib\! Biz hozirgina komponentni qayta foydalanishga yaroqli qilish uchun render proplaridan foydalanishimiz mumkinligini ko'rdik, chunki biz har safar render propiga turli ma'lumotlarni uzatishimiz mumkin. Lekin, nega bundan foydalanmoqchimiz?

Render propni oladigan komponent odatda render propni chaqirishdan ko'ra ko'proq narsani bajaradi. Buning o'rniga, biz odatda ma'lumotlarni render prop sifatida qabul qiluvchi komponentdan ko’ra render prop sifatida uzatadigan elementga o'tkazmoqchimiz\!

```js
function Component(props) {
  const data = { ... }

  return props.render(data)
}
```

Render prop endi o'z argumenti sifatida qabul qilgan bu qiymatni olishi mumkin.

```js
<Component render={data => <ChildComponent data={data} />}
```

Keling, bir misolni ko'rib chiqaylik\! Bizda oddiy dastur bor, unda foydalanuvchi haroratni Selsiy bo'yicha yozishi mumkin. Ilova bu haroratning Farengeyt va Kelvindagi qiymatini ko'rsatadi.

```js
1 import React, { useState } from "react";
2 import "./styles.css";
3
4 function Input() {
5  const [value, setValue] = useState("");
6
7  return (
8    <input
9      type="text"
10      value={value}
11      onChange={e => setValue(e.target.value)}
12      placeholder="Temp in °C"
13    />
14  );
15 }
16
17 export default function App() {
18  return (
19    <div className="App">
20      <h1>☃️ Temperature Converter 🌞</h1>
21      <Input />
22      <Kelvin />
23      <Fahrenheit />
24    </div>
25  );
26 }
27
28 function Kelvin({ value = 0 }) {
29  return <div className="temp">{value + 273.15}K</div>;
30 }
31
32 function Fahrenheit({ value = 0 }) {
33  return <div className="temp">{(value * 9) / 5 + 32}°F</div>;
34 }
```

Hmm.. Hozirda muammo bor. Stateful Input komponenti foydalanuvchi input qiymatini o’z ichiga oladi, yaʼni Farengeyt va Kelvin komponentlari user’s input ga kirish huquqiga ega emas\!

## Holatni ko’tarish

Yuqoridagi misoldagi Farengeyt va Kelvin komponentlari uchun foydalanuvchi ma'lumotlarini taqdim etishning bir usuli, ya’ni biz lifting state, ya’ni holatni ko’tarish usulini amalga oshirishimiz kerak.

Bunday holda, bizda holat ko'rsatilgan Input komponenti mavjud. Biroq, Farengeyt va Kelvin aka-uka komponentlari ham ushbu ma'lumotlarga kirishlari kerak. Stateful Input komponentiga ega bo'lish o'rniga biz holatni Input, Farengeyt va Kelvin bilan bog'langan birinchi umumiy ajdod komponenti, bu holda App komponentiga ko'tarishimiz mumkin\!

```js
function Input({ value, handleChange }) {
  return <input value={value} onChange={(e) => handleChange(e.target.value)} />;
}

export default function App() {
  const [value, setValue] = useState("");


  return (
    <div className="App">
      <h1>☃️ Temperature Converter 🌞</h1>
      <Input value={value} handleChange={setValue} />
      <Kelvin value={value} />
      <Fahrenheit value={value} />
    </div>
  );
}
```

Garchi bu to’g’ri yechim bo’lsa-da, ko’p bolalarga mo’ljallangan komponentlar bilan kattaroq ilovalarda holatni ko’tarish qiyin bo’lishi mumkin. Har bir holat o’zgarishi barcha bolalarning, hatto maʼlumotlar bilan ishlamaydigan bolalarning ham qayta render qilinishiga olib kelishi mumkin, bu esa ilovangizning ishlashiga salbiy taʼsir ko’rsatadi.

## Render props

Buning o'rniga biz render proplaridan foydalanishimiz mumkin\! Keling, Input komponentini render proplarini qabul qiladigan tarzda o'zgartiraylik.

```js
function Input(props) {
  const [value, setValue] = useState("");


  return (
    <>
      <input
        type="text"
        value={value}
        onChange={(e) => setValue(e.target.value)}
        placeholder="Temp in °C"
      />
      {props.render(value)}
    </>
  );
}


export default function App() {
  return (
    <div className="App">
      <h1>☃️ Temperature Converter 🌞</h1>
      <Input
        render={(value) => (
          <>
            <Kelvin value={value} />
            <Fahrenheit value={value} />
          </>
        )}
      />
    </div>
  );
}
```

Zo'r, Kelvin va Farengeytt komponentlari endi user’s input qiymatiga kirish huquqiga ega\!

```js
1 import React, { useState } from "react";
2 import "./styles.css";
3
4 function Input(props) {
5  const [value, setValue] = useState("");
6
7  return (
8    <>
9      <input
10        type="text"
11        value={value}
12        onChange={e => setValue(e.target.value)}
13        placeholder="Temp in °C"
14      />
15      {props.render(value)}
16    </>
17  );
18 }
19
20 export default function App() {
21  return (
22    <div className="App">
23      <h1>☃️ Temperature Converter 🌞</h1>
24      <Input
25        render={value => (
26          <>
27            <Kelvin value={value} />
28            <Fahrenheit value={value} />
29          </>
30        )}
31      />
32    </div>
33  );
34 }
35
36 function Kelvin({ value }) {
37  return <div className="temp">{parseInt(value || 0) + 273.15}K</div>;
38 }
39
40 function Fahrenheit({ value }) {
41  return <div className="temp">{(parseInt(value || 0) * 9) / 5 + 32}°F</div>;
42 }
```

## Bola funksiya sifatida

Oddiy JSX komponentlaridan tashqari biz React komponentlariga bola sifatida funksiyalarni o‘tkazishimiz mumkin. Bu funksiya biz uchun children prop orqali mavjud bo'lib, u texnik jihatdan ham render prop hisoblanadi.

Keling, Input komponentini o'zgartiramiz. Render propni aniq o'tkazish o'rniga, biz faqat Input komponenti uchun funksiyani bola sifatida o'tkazamiz.

```js
export default function App() {
  return (
    <div className="App">
      <h1>☃️ Temperature Converter 🌞</h1>
      <Input>
        {(value) => (
          <>
            <Kelvin value={value} />
            <Fahrenheit value={value} />
          </>
        )}
      </Input>
    </div>
  );
}
```

Input komponentida mavjud bo‘lgan props.children prop orqali biz ushbu funksiyadan foydalanishimiz mumkin. Foydalanuvchi Input qiymati bilan props.render ni chaqirish o‘rniga, biz props.children ni user input qiymati bilan call qilamiz.

```js
function Input(props) {
  const [value, setValue] = useState("");


  return (
    <>
      <input
        type="text"
        value={value}
        onChange={(e) => setValue(e.target.value)}
        placeholder="Temp in °C"
      />
      {props.children(value)}
    </>
  );
}
```

Ajoyib, shu tarzda Kelvin va Farengeyt komponentlari render prop nomi haqida qayg'urmasdan, qiymatga kirish imkoniyatiga ega bo'ladi.

```js
1 import React, { useState } from "react";
2 import "./styles.css";
3
4 function Input(props) {
5  const [value, setValue] = useState(0);
6
7  return (
8    <>
9      <input
10        type="number"
11        value={value}
12        onChange={e => setValue(e.target.value)}
13        placeholder="Temp in °C"
14      />
15      {props.children(value)}
16    </>
17  );
18 }
19
20 export default function App() {
21  return (
22    <div className="App">
23      <h1>☃️ Temperature Converter 🌞</h1>
24      <Input>
25        {value => (
26          <>
27            <Kelvin value={value} />
28            <Fahrenheit value={value} />
29          </>
30        )}
31      </Input>
```

## Hooks

Ba'zi hollarda biz render proplarini Hooks bilan almashtirishimiz mumkin. Bunga yaxshi misol \- [Apollo Client](https://www.apollographql.com/docs/react).

Ushbu misolni tushunish uchun Apollo Client bilan tajriba kerak emas.

Apollo Clientdan foydalanishning bir usuli Mutation va Query komponentlari orqali amalga oshiriladi. Keling, Higher Order Components bo'limida yoritilgan bir xil Input misolini ko'rib chiqaylik. Yuqori tartibli graphql() komponentidan foydalanish o‘rniga, endi biz render propini oladigan Mutation komponentidan foydalanamiz.

```js
1 import React from "react";
2 import "./styles.css";
3
4 import { Mutation } from "react-apollo";
5 import { ADD_MESSAGE } from "./resolvers";
6
7 export default class Input extends React.Component {
8  constructor() {
9    super();
10    this.state = { message: "" };
11  }
12
13  handleChange = (e) => {
14    this.setState({ message: e.target.value });
15  };
16
17  render() {
18    return (
19      <Mutation
20        mutation={ADD_MESSAGE}
21        variables={{ message: this.state.message }}
22        onCompleted={() =>
23          console.log(`Added with render prop: ${this.state.message} `)
24        }
25      >
26        {(addMessage) => (
27          <div className="input-row">
28            <input
29              onChange={this.handleChange}
30              type="text"
31              placeholder="Type something..."
32            />
33            <button onClick={addMessage}>Add</button>
34          </div>
35        )}
36      </Mutation>
37    );
38  }
39 }
```

Mutation komponentidan ma'lumotlarni kerakli elementlarga o'tkazish uchun biz funksiyani bola sifatida o'tkazamiz. Funksiya ma'lumotlarning qiymatini o'z argumentlari orqali oladi.

```js
<Mutation mutation={...} variables={...}>
  {addMessage => <div className="input-row">...</div>}
</Mutation>
```

Garchi biz hali ham render prop modelidan foydalanishimiz mumkin va ko'pincha yuqori komponent modeliga nisbatan afzalroq bo'lsa-da, uning salbiy tomonlari ko’p.

Kamchiliklardan biri bu \- ichki komponentlar ketma-ketligi. Agar komponentga bir nechta mutatsiyalar yoki so‘rovlarga kirish kerak bo‘lsa, biz bir nechta Mutation yoki Query komponentlarini joylashtirishimiz mumkin.

```js
<Mutation mutation={FIRST_MUTATION}>
  {(firstMutation) => (
    <Mutation mutation={SECOND_MUTATION}>
      {(secondMutation) => (
        <Mutation mutation={THIRD_MUTATION}>
          {(thirdMutation) => (
            <Element
              firstMutation={firstMutation}
              secondMutation={secondMutation}
              thirdMutation={thirdMutation}
            />
          )}
        </Mutation>
      )}
    </Mutation>
  )}
</Mutation>
```

Hooks chiqarilgandan so'ng, Apollon Apollo Client kutubxonasiga Hooks supportni qo'shdi. Mutation va Query render proplardan foydalanish o'rniga, dasturchilar endi kutubxona taqdim etgan hooklar orqali ma'lumotlarga bevosita kirishlari mumkin.

Keling, Query render propdagi misolda ilgari ko'rganimizdek, xuddi shu ma'lumotlardan foydalanadigan misolni ko'rib chiqaylik. Bu safar biz komponentga ma'lumotlarni Apollo Client bizga taqdim etgan useQuery hookdan foydalanib taqdim etamiz.

```js
1 import React, { useState } from "react";
2 import "./styles.css";
3
4 import { useMutation } from "@apollo/react-hooks";
5 import { ADD_MESSAGE } from "./resolvers";
6
7 export default function Input() {
8  const [message, setMessage] = useState("");
9  const [addMessage] = useMutation(ADD_MESSAGE, {
10    variables: { message }
11  });
12
13  return (
14    <div className="input-row">
15      <input
16        onChange={(e) => setMessage(e.target.value)}
17        type="text"
18        placeholder="Type something..."
19      />
20      <button onClick={addMessage}>Add</button>
21    </div>
22  );
23 }
```

UseQuery hookdan foydalanib, biz komponentga ma'lumotlarni taqdim etish uchun zarur bo'lgan kod miqdorini kamaytirdik.

## Ustun taraflari

Mantiq va ma'lumotlarni bir nechta komponentlar o'rtasida almashish render props pattern bilan oson. Komponentlarni render yoki children prop yordamida qayta foydalanishga yaroqli holga keltirish mumkin. Higher Order Component pattern asosan bir xil muammolarni, ya'ni qayta foydalanish va ma'lumotlarni almashishni hal qilsa-da, render rekvizitlari namunasi HOC patterndan foydalangan holda biz duch kelishi mumkin bo'lgan ba'zi muammolarni hal qiladi.

HOC patterndan foydalangan holda biz duch kelishi mumkin bo'lgan nomlash bilan bog’liq chalkashlik masalasi render prop modelidan foydalangan holda endi qo'llanilmaydi, chunki biz proplarni avtomatik ravishda birlashtirmaymiz. Biz proplarni asosiy komponent tomonidan taqdim etilgan qiymat bilan aniq tarzda pastki komponentlarga o'tkazamiz.

Biz proplarni aniq topshirganimiz sababli, biz HOCning implicit prop muammosini hal qilamiz. Elementga o'tkazilishi kerak bo'lgan proplarning barchasi render prop argumentlari ro'yxatida ko'rinadi. Shunday qilib, biz ba'zi proplar qayerdan kelganini aniq bilamiz.

Biz ilovamiz mantig’ini render prop orqali komponentlarni renderlashdan ajratishimiz mumkin. Render propini qabul qiluvchi stateful komponent ma'lumotlarni faqat ma'lumotlarni taqdim qiluvchi stateless komponentlarga o'tkazadi.

## Kamchiliklari

Biz render proplari yordamida hal qilishga uringan muammolar asosan React Hooks bilan almashtirildi. Hooks komponentlarga qayta foydalanish imkoniyati va ma'lumotlar almashish usulini o'zgartirganligi sababli, ular ko'p hollarda render proplari namunani almashtirishi mumkin.

Render propga lifecycle usullarini qo'sha olmasligimiz sababli, biz uni faqat qabul qilingan ma'lumotlarni o'zgartirishi kerak bo'lmagan komponentlarda ishlatishimiz mumkin.

## Havolalar

* [Render Props \- React](https://reactjs.org/docs/render-props.html)  
* [React, Inline Functions, and Performance \- Ryan Florence](https://cdb.reacttraining.com/react-inline-functions-and-performance-bdff784f5578)  
* [Use a Render Prop\! \- Michael Jackson](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce)